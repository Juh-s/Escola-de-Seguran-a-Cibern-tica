# Secrets
###### Solved by @Juh-s
>This is a CTF about Web Exploitation and SSTI
## Sobre o desafio

I made a cool website where you can announce whatever you want! Try it out!

I heard templating is a cool and modular way to build web apps! Check out my website [here](http://rescued-float.picoctf.net:51371/)!

Hints: Server Side Template Injection

**Tradução**:

Eu fiz um site legal onde você pode anunciar o que quiser! Experimente!

Eu ouvi que a utilização de templates é uma maneira legal e modular de construir aplicativos web! Confira meu site [aqui](http://rescued-float.picoctf.net:51371/)!

Dicas: Server Side Template Injection

## Solução
**Introdução**

Ao acessar o site dado no enunciado, esse é o conteúdo:

<img width="627" height="195" alt="image" src="https://github.com/user-attachments/assets/ab8b8ed1-eb8f-4805-8f02-4e0bb15525df" />

Além disso, pela dica passada é possível saber que a solução do desafio será através de [Server Side Template Injection (SSTI)](https://portswigger.net/web-security/server-side-template-injection).

Um teste de como o site funciona:

<img width="556" height="186" alt="image" src="https://github.com/user-attachments/assets/4339e3cd-c5e1-4ae8-b659-fe4363939df6" /> <img width="486" height="307" alt="image" src="https://github.com/user-attachments/assets/bb4521e4-2455-4c5a-be2b-42eab5230d1c" />

**Análise Inicial**

Uma informação muito importante para saber se um servidor é vulnerável a SSTI é analisar essa tabela, que nos indica qual engine está sendo usada:

<img width="640" height="386" alt="image" src="https://github.com/user-attachments/assets/d9da9aa9-b5b2-4c66-a7d0-745310fde3f7" />

Seguindo passo a passo da tabela, usando o input `${7*7}` primeiro:

<img width="575" height="311" alt="image" src="https://github.com/user-attachments/assets/713f7e5a-38a3-4b7c-9840-bb74a7afac06" />

O site renderiza literalmente `${7*7}`, ou seja, nada é avaliado.

Porém, ao passar para o próximo passo, `{{7*7}}`: 

<img width="443" height="297" alt="image" src="https://github.com/user-attachments/assets/502c1d98-aa5f-45f1-bd03-c5e9a8e0649e" />

Dessa vez, o site avalia o resultado como `49`.

Com isso, podemos concluir que o [template engine](https://www.treinaweb.com.br/blog/o-que-e-template-engine/) usa a sintaxe de `{{...}}` para avaliação. Ao observar a tabela, o próximo comando para usar é o `{{7*'7'}}`:

<img width="581" height="293" alt="image" src="https://github.com/user-attachments/assets/36fb67a6-bf94-4aec-8272-d474e06b6887" />

O retorno de `7777777` é útil para entendermos que o engine segue uma semântica compatível com Python, isso porque ele sabe tratar tipos distintos e aplicar [operator overloading](https://learn.microsoft.com/pt-br/cpp/cpp/operator-overloading?view=msvc-170) como no Python. Além disso, ele nos mostrar que é possível injetar strings literais no comando `{{...}}` sem que o site as rejeite.

Isso é importante porque:
* Os payloads de SSTI geralmente precisam passar strings.
* O engine usado é provavelmente Jinja2 ou Twig, porém como usamos python, iremos considerar o Jinja2.

Primeiro, iremos verificar se é possível acessar objetos com o `request`:

<img width="942" height="83" alt="image" src="https://github.com/user-attachments/assets/8fe70b78-3b90-4962-a634-791bb398933a" />

Isso porque, se esse objeto está disponível no contexto do template, significa que existe uma porta para o ambiente interno do servidor.

Além disso, linguagens e engines em templates permitem acessar objetos e em Python, esses objetos têm atributos especiais:

* `__globals__`: Dicionário de variáveis globais associadas a uma função ou objeto.
* `__builtins__`: Contém funções e objetos embutidos do Python.

Usamos essa cadeia para alcançar o mecanismo de importação do Python. Pois, a partir de um objeto acessível `request` é possível alcançar `__globals__` e depois `__builtins__` para chamar `__import__`.

A parte `__import__` do payload deve ser escrita da seguinte forma: `__import__('os')`. Porque dessa forma conseguimos obter o módulo `os` dentro do template, que é crucial visto que ele dá acesso a funcionalidades do sistema.

É necessário também usar `.popen('ls')` para descobrirmos os arquivos do diretório atual e encontrarmos o nome e caminho exatos antes de tentar `cat`.

Montando o payload: `{{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}`

<img width="933" height="121" alt="image" src="https://github.com/user-attachments/assets/c3215088-262b-4858-9d32-4df787be1615" />

**Solução**

Ao acessar `ls` descobrimos que existe um arquivos chamado `flag` nesse mesmo diretório, e então basta ler esse arquivo para obtermos a flag.

Para solucionar o problema, usamos o mesmo payload modificando apenas o `.popen('ls')` para `.popen('cat flag')` que irá ler o arquivo `flag` no sistema do site e retornar o conteúdo:

`{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}`

<img width="937" height="147" alt="image" src="https://github.com/user-attachments/assets/674f3272-6c92-407f-95f6-7e929c3cfceb" />

Feito isso, conseguimos obter a flag:

>`picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_09365533}`

**Aplicação no dia a dia**

Essa vulnerabilidade é facilmente confundida com ataques de XSS, então nem sempre existe uma proteção contra ela. 

Além disso, para que não exista esse tipo de leitura de arquivos ou exploração da vulnerabilidade, algumas práticas podem ser realizadas:

* Nunca renderizar templates a partir de strings controladas pelo usuário.
* Não expor objetos inteiros ao contexto do template.
* Usar sandboxing do engine quando possível.

Feito isso, o servidor ficará menos vulnerável à esse tipo de ataque.
