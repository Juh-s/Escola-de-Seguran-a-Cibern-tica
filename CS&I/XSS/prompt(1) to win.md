# prompt(1) to win
###### Solved by @Juh-s
>This is a game about XSS injection, JavaScript and HTML analysis
## Sobre o desafio
**Rules**

Call prompt(1) to win. You should make it without user interaction. After completing each level, your payload will be pending for verification and then tested in:

* Chrome (Latest version)
* Firefox (Latest version)
* Internet Explorer 10

While most levels have all browser solutions, some may not. It is guaranteed that each level has solutions for at least two browsers.
Last but not least, payloads take fewer characters rock.

_**Tradução:**_

**Regras**

Chame prompt(1) para vencer. Você deve fazer isso sem interação do usuário.
Após completar cada fase, seu payload será verificado e testado nos seguintes navegadores:

* Chrome (versão mais recente)
* Firefox (versão mais recente)
* Internet Explorer 10

Embora a maioria das fases tenha soluções para todos os navegadores, algumas podem não ter. É garantido que cada fase tenha pelo menos uma solução válida para dois navegadores.

E por último, mas não menos importante: payloads mais curtos são ótimos.
## Solução
**Introdução**

Este [jogo](https://prompt.ml/0) é composto por várias fases, onde o objetivo é usufruir da aplicação de [XSS injection](https://www.kaspersky.com.br/resource-center/definitions/what-is-a-cross-site-scripting-attack) para avançarmos de fase. Isso porque o XSS é uma falha de segurança em sites e ao realizar esse procedimento é possível explorar erros de validação e escape de dados. Dessa forma, conseguimos fazer com que o navegador execute o comando `prompt(1)` sem precisar clicar em nada, permitindo que possamos avançar para a próxima fase.  

## Fase 0

**Análise Inicial**

A fase inicial tem a seguinte estrutura:

<img width="1491" height="647" alt="1 desafio" src="https://github.com/user-attachments/assets/99b952ec-e585-45d3-b291-ca852cf445ee" />

Na imagem ilustrada é possível observar um código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript), descrito pelas linhas:
```
1  function escape(input) {
2      // warm up
3      // script should be executed without user interaction
4      return '<input type="text" value="' + input + '">';
5  }     
```
Além disso, existem dois campos interativos, o primeiro para a inserção do nome do usuário e o segundo para a inserção de algum dado que interage com a função.

Ao fazer a análise, entende-se que código pega o que será digitado no campo _Enter input_ e insere na variável _value_ (4ª linha do código) no [HTML](https://pt.wikipedia.org/wiki/HTML).

Um exemplo prático seria digitar no campo _Your name_, o nome `Juh`, e no campo _Enter input_ a palavra `teste`, para obtermos o seguinte:

<img width="1471" height="637" alt="image" src="https://github.com/user-attachments/assets/30261433-1cb0-4eb1-b9c7-7df3dc71e46b" />

Porém, ao observar melhor o JavaScript, é possível entender que ele pega o valor digitado e coloca dentro do HTML, sem verificar o uso dos caracteres especiais. Sendo assim, é fácil modificar o que o código está retornando apenas alterando onde o _return_, da função, termina.

**Análise final**

Para conseguir modificar o que a função está retornando, é necessário utilizar a tag [`<script>`](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Reference/Elements/script) que é usada para incluir ou referenciar um dado executável. Ao realizar esse procedimento, é possível modificar a estrutura original do HTML, já que ele vê um novo script e considera como algo válido para executar.

Para fazer com que o código retorne o que o jogo deseja, é necessário aplicar essa ferramenta que modificará a lógica para que o usuário não precise digitar o comando desejado, mas faz com que o navegador o execute imediatamente.

Digitando o seguinte: `"><script>prompt(1)</script>`, o navegador fecha o campo do _value_ e executa o comando. Dessa forma é possível avançar para o próximo nível.

<img width="1481" height="646" alt="image" src="https://github.com/user-attachments/assets/49c7ae56-bf1f-4f79-957f-c0427e6cdc8a" />

Input necessário:

>"><script>prompt(1)</script>

## Fase 1

**Análise Inicial**

A segunda fase tem a seguinte estrutura:

<img width="1463" height="672" alt="image" src="https://github.com/user-attachments/assets/ecb96f1d-05a2-41a4-881c-c72dadac4ace" />

Na imagem ilustrada é possível observar que o nome de usuário se mantém e um código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é descrito pelas linhas:
```
1  function escape(input) {
2      // tags stripping mechanism from ExtJS library
3      // Ext.util.Format.stripTags
4      var stripTagsRE = /<\/?[^>]+>/gi;
5      input = input.replace(stripTagsRE, '');
6  
7      return '<article>' + input + '</article>';
8  }         
```

Porém dessa vez, o código cria uma váriável `var stripTagsRE = /<\/?[^>]+>/gi;` que confere o uso de tags que podem ser injetadas e além disso possui um `input = input.replace(stripTagsRE, '');`, um comando que remove essas possíveis tags. Além disso, ele insere o dado recebido pelo usuário dentro de um [`<article>`](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Reference/Elements/article), ao invés de colocar no _value_ como na questão passada.

Como não dá para usar o mesmo método para resolver a questão, porque se o comando `<script>` for inserido ele será deletado, é possível utilizar a tag [`<svg>`](https://www.w3schools.com/TAGS/tag_svg.asp) juntamente ao evento [`onload`](https://www.w3schools.com/jsref/event_onload.asp) para executar imediatamente o comando.

**Análise final**

Ao digitar o seguinte no _Enter input_: `<svg/onload=prompt(1)`

<img width="1457" height="672" alt="image" src="https://github.com/user-attachments/assets/6bb89c17-4124-43a7-9ff9-54244ce88597" />

Não deu certo porque o comando entrou no `<article>`, que não permitiu que ele fosse executado. Porém, basta fazer uma quebra de linha para que o comando seja colocado para fora dessa tag.

<img width="1178" height="527" alt="image" src="https://github.com/user-attachments/assets/38436f0f-196c-440a-95b3-4291df3ab63c" />

Input necessário:

><svg/onload

>=prompt(1)

## Fase 2

**Análise Inicial**

Esta fase tem a seguinte estrutura:

<img width="1457" height="653" alt="image" src="https://github.com/user-attachments/assets/b54620c5-9763-4597-9180-fac7f9c604bb" />

Dessa vez, o código descrito em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é esse:

```
1  function escape(input) {
2      //                      v-- frowny face
3      input = input.replace(/[=(]/g, '');
4      
5      // ok seriously, disallows equal signs and open parenthesis
6      return input;
7  }        
``` 

Essa função bloqueia todos os tipos de abertura de parênteses e sinais de igualdade (mostrado na 3ª linha do código). Logo, não é possível digitar `prompt(1)` porque ele removeria o sinal que foi bloqueado e restaria apenas `prompt1)`.

Então, a maneira possível para resolver esse desafio, é encontrar outra forma de escrever esse comando para que seja possível rodar um `<script>` da mesma forma que ele foi usado na primeira fase.

**Análise final**

Um jeito simples de rodar uma função é digitar [`.call`](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Function/call) após o comando que precisa ser dado. Ao fazer isso no contexto da questão, o código vai interpretar que o comando `prompt` precisa ser executado. Porém, o uso comum dessa ferramenta é digitar `( )` para inserir o argumento dentro dos parênteses, mas como não dá para usar esses caracteres especiais, uma maneira alternativa é digitar `${ }` no lugar deles.

Além disso, um atalho muito útil que serve para passar valores é o [caractere (``)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) que funciona em conjunto com o `${ }` para determinar o que será inserido no comando.

Portanto, ao digitar essas ferramentas a fim de substituir os caracteres bloqueados, é possível avançar para a próxima fase.

<img width="1458" height="627" alt="image" src="https://github.com/user-attachments/assets/a631c2e9-8893-4e8b-9558-f9fcfb3cc6c2" />

Input necessário:

><script>prompt.call`${1}`</script>

## Fase 3

**Análise Inicial**

A próxima fase tem essa representação:

<img width="1461" height="661" alt="image" src="https://github.com/user-attachments/assets/2b989b3e-37df-4f78-bd81-7c785dec5f1b" />

Além disso, o código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é esse:

```
1  function escape(input) {
2      // filter potential comment end delimiters
3      input = input.replace(/->/g, '_');
4      
5      // comment the input to avoid script execution
6      return '<!-- ' + input + ' -->';
7  }        
``` 

Ao analisar essa função, é possível observar que ela pega o que foi digitado no campo _Enter input_ e coloca como um comentário (5ª e 6ª linhas), para evitar que caso o usuário digite o comando `prompt(1)` esse mesmo seja executado.

Logo, para resolver esse problema é preciso realizar algum truque que evite que isso aconteça.

**Análise final**

Uma maneira extremamente simples de resolver, é fechar o comentário antes que o usuário insira o comando necessário.

Para isso, ao analisar o funcionamento do [comentário do HTML](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Comments), entende-se que para fechá-lo antes de digitar o comando, deve ser usada a expressão `--!>`.

É importante ressaltar que o uso da expressão `--!>` geralmente leva a um [_parse error_](https://king.host/wiki/artigo/parse-error/) mas nas especificações do HTML essa expressão é válida mesmo assim. Com isso, o uso do `-->` também funcionaria, porém o código bloqueia a operação `->` (3ª linha), não permitindo que esses dois caracteres especiais sejam usados juntos.

Um teste para provar que a função bloqueia essa expressão:

<img width="1458" height="650" alt="image" src="https://github.com/user-attachments/assets/48ca8c7c-f6cc-4c68-895e-ece34a25304b" />

Com isso, basta digitar a tag `<script>` novamente, porém com o uso do operador que não foi bloqueado pela função, responsável por fechar o comentário antes que seja digitado o comando.

<img width="1456" height="632" alt="image" src="https://github.com/user-attachments/assets/f4b16235-93ec-4c50-94fb-935d58781836" />

Input necessário:

>--!><script>prompt(1)</script>

## Fase 4

Não foi possível resolver.

## Fase 5

**Análise Inicial**

Essa fase é bem parecida com a primeira com a seguinte estrutura:

<img width="1461" height="652" alt="image" src="https://github.com/user-attachments/assets/292b18b2-4f06-4250-b1ec-69899ad0d8ac" />

Com o código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) sendo:

```
1  function escape(input) {
2      // apply strict filter rules of level 0
3      // filter ">" and event handlers
4      input = input.replace(/>|on.+?=|focus/gi, '_');
5             
6      return '<input value="' + input + '" type="text">';
7  }
``` 
Dessa vez, o código aplica filtros no que poderia ser facilmente resolvido na primeira fase (2ª linha da função). Então não é possível utilizar a tag `<script>` pois ele bloqueia o uso de `>`, além de bloquear qualquer informação que comece com `on` e tenha o sinal de igual na frente (=) a substituindo por `_`.

Uma resolução válida para essa fase é tentar quebrar a estrutura do _input_, inserindo novos parâmetros dentro dela.

**Análise final**

Um atributo que pode ser adicionado é o de [type=image](https://www.w3schools.com/tags/att_input_type_image.asp), que funciona em conjunto com o `src`, que é por onde o caminho para essa imagem é especificado.

Além disso, é necessário usar o comando [`onerror=`](https://www.w3schools.com/jsref/event_onerror.asp) que irá executar o comando do `prompt(1)`.

Ao fazer a montagem do _input_ com a nova estrutura temos que:

```
"type=image src onerror="prompt(1)
```
* O caractere `"` irá fechar o campo do _input value="_.
* A parte do `type=image` irá definir uma imagem como um botão interativo.
* O `src` definiria o caminho pelo qual essa imagem levaria, porém como ele não tem nenhum valor atribuído, ativa a função do `onerror=`.
* `onerror=` executa o comando `"prompt(1)`

Montar a estrutura dessa forma serve apenas para que seja possível ativar o comando de erro, que nesse caso, leva a função à executar o `prompt(1)`. 

Porém, ao digitar isso no campo de _Enter input_, o resultado é esse:

<img width="1458" height="651" alt="image" src="https://github.com/user-attachments/assets/d138eaa6-c94c-4307-9381-75b742e6f722" />

Ou seja, não deu certo porque a função substitui o valor de `onerror=` para `_`. Porém uma maneira fácil de contornar essa situação, seria passar o conteúdo `="prompt(1)` para a próxima linha, dessa forma o filtro da função não irá remover o que foi digitado e o comando funcionará do mesmo jeito.

<img width="1457" height="672" alt="image" src="https://github.com/user-attachments/assets/86b575b6-c69f-4f02-ab94-1b76cf616130" />

Input necessário:

>"type=image src onerror

> ="prompt(1)

## Fase 6

**Análise Inicial**

A fase 6 tem uma extensa estrutura:

<img width="1184" height="1027" alt="Screenshot 2025-08-31 163433" src="https://github.com/user-attachments/assets/2c6c39fe-6236-4b24-9bb9-1471603672d4" />

E o seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é:

```
1  function escape(input) {
2      // let's do a post redirection
3      try {
4          // pass in formURL#formDataJSON
5          // e.g. http://httpbin.org/post#{"name":"Matt"}
6          var segments = input.split('#');
7          var formURL = segments[0];
8          var formData = JSON.parse(segments[1]);
9  
10         var form = document.createElement('form');
11         form.action = formURL;
12         form.method = 'post';
13  
14         for (var i in formData) {
15             var input = form.appendChild(document.createElement('input'));
16             input.name = i;
17             input.setAttribute('value', formData[i]);
18          }
19  
20          return form.outerHTML + '                         \n\
21  <script>                                                  \n\
22      // forbid javascript: or vbscript: and data: stuff    \n\
23      if (!/script:|data:/i.test(document.forms[0].action)) \n\
24          document.forms[0].submit();                       \n\
25      else                                                  \n\
26          document.write("Action forbidden.")               \n\
27  </script>                                                 \n\
28          ';
29      } catch (e) {
30          return 'Invalid form data.';
31      }
32  }   
``` 
De maneira resumida, esse código espera que o usuário dê uma entrada _input_, que deve ter um endereço [URL](https://tecnoblog.net/responde/o-que-e-url/) e dados em [JSON](https://developer.mozilla.org/pt-BR/docs/Glossary/JSON) e divide essa entrada com base no caractere `#`, então o que vier antes desse sinal será a URL e o que vier depois serão os dados em JSON.

Depois disso, o código cria um formulário escondido no navegador, onde:

* action = URL (para onde enviar os dados).
* method = post (manda por post).
* um input adicionado a cada campo do JSON.

Caso a entrada URL digitada não seja válida, o código mostra: `"Action forbidden."` e caso seja válida, ele retorna automaticamente o formulário montado:

<img width="1475" height="563" alt="image" src="https://github.com/user-attachments/assets/9e07ce3e-e958-4acf-95e4-edff72ca207c" />

Além disso, nessa fase não é possível rodar o `prompt(1)` via evento como foi feito anteriormente usando `onerror=`, e é importante ressaltar que o código bloqueia o uso de `script:` e `data:` (linha 23), mas não bloqueia o uso de `javascript:`.

**Análise final**

Dessa forma, ao digitar `javascript:prompt(1)`, um comando de execução de JavaScript, o código manda o formulário para essa URL e o navegador executa o comando de `prompt(1)`.

Porém, como é necessário que haja uma segunda parte que contenha dados em JSON, é possível usar `#{"action":0}` que servirá apenas para satisfazer esse preenchimento de dados que o código pede. Depois dos dois pontos, qualquer número é aceito, mas podemos usar o 0 como exemplo.

Essa parte do JSON só foi escrita dessa forma porque tem uma chave (action), um valor (0) e é curta para facilitar. Mas poderia ser substituída por outro conteúdo, como mostra o exemplo do código (linha 5).

<img width="1462" height="867" alt="image" src="https://github.com/user-attachments/assets/da5509ab-3882-4391-b946-8b7a90cd5f55" />

Input necessário:

>javascript:prompt(1)#{"action":0}

## Fase 7

**Análise Inicial**

A fase 7 tem o seguinte formato:

<img width="1461" height="702" alt="image" src="https://github.com/user-attachments/assets/d5020092-865f-4a01-8419-46b97f673e88" />

Já seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é:

```
1  function escape(input) {
2      // pass in something like dog#cat#bird#mouse...
3      var segments = input.split('#');
4      return segments.map(function(title) {
5          // title can only contain 12 characters
6          return '<p class="comment" title="' + title.slice(0, 12) + '"></p>';
7      }).join('\n');
8  }
```
Ao fazer a análise do código, é possível observar que ao usar o caractere `#`, o _input_ que será atribuído ao _title=_ será dividido. Além disso, esse título poderá ter no máximo 12 caracteres.

Um exemplo prático das duas funcionalidades do código seria:

<img width="1458" height="720" alt="image" src="https://github.com/user-attachments/assets/13878a11-280c-4e89-804c-3edc39aea2fe" />

Como mostrado na imagem, sempre que for utilizado o `#` o código interpretará como um outro título e caso esse título passe de 12 caracteres, os excedentes não aparecerão.

Dessa forma, para resolver essa questão, será necessário utilizar alguma ferramenta que execute o comando de `prompt(1)` que possa ser dividida em várias partes, visto que se não fosse pela limitação de caracteres, o comando `"><script>prompt(1)</script>` poderia ser usado.

**Análise final**

Uma entrada válida seria `"><svg/a=#"onload='/*#*/prompt(1)'`, pois:

* `"><svg/a=` Fecha o atributo _title="_ e insere uma nova tag [`<svg>`](https://www.w3schools.com/tags/tag_svg.asp), que permite atributos como [onload](https://www.w3schools.com/jsref/event_onload.asp).
* `"onload='/*` Um evento disparado automaticamente quando o elemento é carregado. Além disso o `*/` serve para comentar qualquer fechamento que o código possa gerar, mantendo o HRML válido.
* `*/prompt(1)'` Começa fechando o comentário feito na linha anterior e completa o código para que o `prompt(1)` seja executado.

<img width="1448" height="683" alt="image" src="https://github.com/user-attachments/assets/e41dcac9-d866-4e62-8004-6fbf0b508d60" />

Input necessário:

>`"><svg/a=#"onload='/*#*/prompt(1)'`

## Fase 8

**Análise Inicial**

Segue a estrutura da fase 8:

<img width="1457" height="715" alt="Screenshot 2025-08-31 184414" src="https://github.com/user-attachments/assets/7554d745-d054-479a-b786-08df77c1c382" />

Seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é representado pelas linhas:

```
1   function escape(input) {
2      // prevent input from getting out of comment
3      // strip off line-breaks and stuff
4      input = input.replace(/[\r\n</"]/g, '');
5      
6      return '                                \n\
7   <script>                                   \n\
8      // console.log("' + input + '");        \n\
9   </script> ';
10  }
```

Essa função pega o _input_ do usuário, porém não permite o uso de alguns caracteres especiais. Além disso, ela pega o que foi digitado e insere no `console.log` na forma de comentário.

Para resolver essa fase, é necessário fazer com que o código entenda o comando e não o considere como um comentário, para que seja possível executá-lo. Porém, para fechar a seção do comentário, seria preciso utilizar `"`, mas como a função filtra esse caractere, outra ação precisa ser feita.

**Análise final**

Uma maneira de escapar dessa situação, é usar `\u2028`, que é um caractere de quebra de linha para o JavaScript, porém será necessário inserir esse dado diretamente no _console_ assim como a função mostra na linha 8.

Caso esse caractere seja digitado no _Enter input_ normalmente, ele não funcionará. Isso porque ele vai direto para o comentário e não o executa.

<img width="1147" height="645" alt="image" src="https://github.com/user-attachments/assets/bd843174-1198-4626-b9af-6901615887ac" />

Partindo para o console com a parte do _input_ selecionada, é necessário digitar o seguinte:

`document.getElementById("input").value="\u2028prompt(1)\u2028-->"`

Esse comando irá atribuir o que for escrito depois do sinal de igual ao _input_, além disso o uso do caractere apresentado anteriormente irá quebrar as linhas e o operador `-->` fechará o comentário.

<img width="1630" height="647" alt="image" src="https://github.com/user-attachments/assets/bf9abc15-af26-49a4-98c7-5e5989d82f0f" />

Como é possível ver, o comando de prompt foi adicionado ao _Enter input_ diretamente do console, por isso ele nem entra no comentário do _console.log_. Então basta digitar `enter` no campo do _Enter input_ para que o comando seja separado do fim do comentário.

<img width="1158" height="567" alt="image" src="https://github.com/user-attachments/assets/067c43a4-7cfa-4e7a-ab4b-25dcbf2e5363" />

Input necessário:

>`document.getElementById("input").value="\u2028prompt(1)\u2028-->"`
(diretamente no console)

## Fase 9

**Análise Inicial**

A fase 9 tem a seguinte estrutura:

<img width="1442" height="711" alt="image" src="https://github.com/user-attachments/assets/8a40a477-9cf8-465d-a453-feadcdf5bbbe" />

Além disso, seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é esse:

```
1   function escape(input) {
2      // filter potential start-tags
3      input = input.replace(/<([a-zA-Z])/g, '<_$1');
4      // use all-caps for heading
5      input = input.toUpperCase();
6      
7      // sample input: you shall not pass! => YOU SHALL NOT PASS!
8      return '<h1>' + input + '</h1>';
9   }
```

Essa função tenta evitar um HTML malicioso ao converter o texto dentro da tag no _return_ para maiúsculas, além de alterar o texto da tag `<script>`.

Porém, é possível substituir os textos por [entidades HTML](https://www.freeformatter.com/html-entities.html), no caso do comando `prompt(1)`. Além disso, será preciso alterar a tag `<svg>` para usá-la também.

**Análise final**

Uma maneira de mudar a tag `<svg>`, é alterar a letra s pelo símbolo [ſ](https://unicodeplus.com/U+017F), pois eles tem a mesma funcionalidade no HTML.

Utilizando a mesma resolução da fase 1, porém com alterações na escrita da tag `<svg>` e escrevendo o comando através das entidades HTML, temos:

<img width="1166" height="510" alt="image" src="https://github.com/user-attachments/assets/ca946c75-0362-4c34-affc-731b7a21c78c" />

Input necessário:

><ſvg/onload=&#112;&#114;&#111;&#109;&#112;&#116;&#40;&#49;&#41;>

## Fase A (10)

**Análise Inicial**

Esta fase tem a seguinte estrutura:

<img width="1470" height="696" alt="image" src="https://github.com/user-attachments/assets/01d3f0b4-8c9b-44bf-ad0e-810e61cc42da" />

O seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é representado por essas linhas:

```
1   function escape(input) {
2      // (╯°□°）╯︵ ┻━┻
3      input = encodeURIComponent(input).replace(/prompt/g, 'alert');
4      // ┬──┬ ﻿ノ( ゜-゜ノ) chill out bro
5      input = input.replace(/'/g, '');
6      
7      // (╯°□°）╯︵ /(.□. \）DONT FLIP ME BRO
8      return '<script>' + input + '</script> ';
9   }
```

Essa fase é mais divertida, principalmente por sua estrutura, já que existe uma historinha entre as linhas do código. Mas fora isso, o que o código faz é mandar um alerta para o usuário caso ele digite `prompt` e também remover as aspas simples se digitadas. Além disso, a linha 8 nos mostra que a função já retorna uma tag `<script>`.

Dessa forma, ao fazer o teste colocando o comando `prompt(1)` no __Enter input_, obtemos:

<img width="1446" height="712" alt="image" src="https://github.com/user-attachments/assets/ff302508-7775-41bf-8017-e843aad3121a" />

Ou seja, ele transformou o comando em um alerta que retorna o número 1.

**Análise final**

Uma análise bem fácil de ser feita é entender que o código "ignora" as aspas simples, então elas podem ser usadas a nosso favor para que seja possível digitar o comando de `prompt(1)` de forma que ele não seja substituído por um comando de alerta.

Então basta colocar um caractere `'` no meio da palavra `prompt` para que o código não a mude, já que esse sinal será ignorado de qualquer maneira.

<img width="1452" height="632" alt="image" src="https://github.com/user-attachments/assets/b216e25d-11ea-44ea-a8e3-12f3b5ec33e9" />

Input necessário:

>pro'mpt(1)

## Fase B (11)

**Análise Inicial**

A próxima fase tem essa representação:

<img width="1446" height="847" alt="image" src="https://github.com/user-attachments/assets/e162f51f-a271-40b7-822a-1b030b197c1f" />

E o seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é esse:

```
1  function escape(input) {
2      // name should not contain special characters
3      var memberName = input.replace(/[[|\s+*/\\<>&^:;=~!%-]/g, '');
4
5      // data to be parsed as JSON
6      var dataString = '{"action":"login","message":"Welcome back, ' + memberName + '."}';
7
8      // directly "parse" data in script context
9      return '                                \n\
10  <script>                                   \n\
11     var data = ' + dataString + ';          \n\
12     if (data.action === "login")            \n\
13         document.write(data.message)        \n\
14  </script> ';
15  }   
```

Explicando mais sobre o que o código faz:

* O _input_ do usuário vai para a variável _memberName_.
* Esse _memberName_ é colocado dentro de uma string [JSON](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/JSON).
* O que foi digitado pelo usuário será devolvido na saída, no formato de `Welcome back, (input do usuário)`.
* Ele bloqueia diversos tipos de caracteres especiais, porém não remove a palavra `in` nem os parentêses.

Para resolver essa fase, é necessário fazer com que o código rode o comando `prompt(1)`, porém sem o uso de tags ou argumentos que utilizam os caracteres bloqueados.

**Análise final**

Como foi analisado anteriormente, o código não bloqueia o uso do operador `in` nem o uso dos parentêses, então é válido usar o [operador in](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/in) para que seja executado o comando que desejamos.

O funcionamento desse operador é bem simples, ele verifica se uma propriedade existe em um objeto. Porém, antes de verificar ele precisa avaliar o que foi passado anteriormente e nesse caso, é o `prompt(1)` que será executado instantaneamente. Além disso, não importa se o valor que o operador retorna é _true_ ou _false_, visto que o comando já foi executado.

Então basta colocar esse operador após o comando, que será possível avançar de fase.

<img width="1452" height="777" alt="image" src="https://github.com/user-attachments/assets/892ee1dd-dfc0-4a8f-aa7b-e227bdca2efb" />

Input necessário:

>"(prompt(1))in"

## Fase C (12)

**Análise Inicial**

A fase 12 tem a seguinte representação:

<img width="1463" height="701" alt="image" src="https://github.com/user-attachments/assets/b958d230-8983-497d-b54a-8cebe231380e" />

E o seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é esse:

```
1   function escape(input) {
2      // in Soviet Russia...
3      input = encodeURIComponent(input).replace(/'/g, '');
4      // table flips you!
5      input = input.replace(/prompt/g, 'alert');
6      
7      // ノ┬─┬ノ ︵ ( \o°o)\
8      return '<script>' + input + '</script> ';
9   }
```
Essa fase faz uma referência a historinha da fase 10, invertendo a situação que aconteceu, tanto na história quanto no código. Estes foram os valores invertidos:

* `input = encodeURIComponent(input).replace(/prompt/g, 'alert');` se tornou `input = encodeURIComponent(input).replace(/'/g, '');`.
* `input = input.replace(/'/g, '');` se tornou `input = input.replace(/prompt/g, 'alert');`.

Ao analisar o funcionamento desse código, entende-se que será necessário executar o comando `prompt(1)` sem digitar essa palavra, porque a função irá transformá-la diretamente em um alerta no site.

<img width="1455" height="631" alt="image" src="https://github.com/user-attachments/assets/3d7c8a8a-a07a-41c4-ada7-55723e9dad5e" />

**Análise final**

Para fugir desse bloqueio do código, é possível escrever a palavra `prompt` de forma codificada, para que ela não seja transformada em `alert`.

Dito disso, o método .toString( ) pega um número inteiro e o converte para uma string usando dígitos válidos na base escolhida. Ou seja, caso a base escolhida fosse 16, não seria possível converter as letras da palavra `prompt` visto que a base 16 só vai até a letra f. Por isso, a base escolhida foi 30, visto que ela termina exatamente na letra t, que é a última do alfabeto necessária para formar a palavra. Portanto, o número `0x258da033` convertido para a base 30, retorna exatamente a palavra necessária para executar o comando.

Um operador válido que computa um código em JavaScript representado como uma string é a função [`eval( )`](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/eval).

Ao montar exatamente como o _input_ deve ser, temos:

* `eval(0x258da033.toString(30))(1)` onde o comando eval computa o código, o número 0x258da033 é convertido em string na base 30 e o número (1) é inserido após a chamada do `prompt`.

<img width="1451" height="617" alt="image" src="https://github.com/user-attachments/assets/563e351e-b824-4f6c-b42a-6d434ef9a8de" />

Input necessário:

>eval(0x258da033.toString(30))(1)

## Fase D (13)

**Análise Inicial**

Esta fase tem a seguinte estrutura:

<img width="1188" height="1041" alt="Screenshot 2025-09-01 123029" src="https://github.com/user-attachments/assets/78ebf3c7-5f73-4f76-905b-8eb9078ef968" />

O seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é representado por essas linhas:

```
1  function escape(input) {
2      // extend method from Underscore library
3      // _.extend(destination, *sources) 
4      function extend(obj) {
5         var source, prop;
6          for (var i = 1, length = arguments.length; i < length; i++) {
7              source = arguments[i];
8              for (prop in source) {
9                  obj[prop] = source[prop];
10             }
11         }
12         return obj;
13     }
14     // a simple picture plugin
15     try {
16         // pass in something like {"source":"http://sandbox.prompt.ml/PROMPT.JPG"}
17         var data = JSON.parse(input);
18         var config = extend({
19             // default image source
20             source: 'http://placehold.it/350x150'
21         }, JSON.parse(input));
22         // forbit invalid image source
23         if (/[^\w:\/.]/.test(config.source)) {
24            delete config.source;
25         }
26         // purify the source by stripping off "
27         var source = config.source.replace(/"/g, '');
28         // insert the content using mustache-ish template
29         return '<img src="{{source}}">'.replace('{{source}}', source);
30     } catch (e) {
31         return 'Invalid image data.';
32     }
33 }        
```

De maneira simples, essa função:

* Analisa um [JSON](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/JSON) contendo a [URL](https://tecnoblog.net/responde/o-que-e-url/) de uma imagem
* Usa um método de [extend](https://www.tutorialspoint.com/underscorejs/underscorejs_extend.htm)
* Tenta validar a URL com uma [regex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_expressions) simples
* Gera uma tag que substitui _{{source}}_

**Análise final**

Para avançarmos de fase, será necessário usar o seguinte _Input_:

`{"source":{},"&#95;&#95;proto&#95;&#95;":{"source":"$`onerror=prompt(1)>"}}`

* A entrada usa [proto](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto) para inserir _source_ no `obj[prop] = source[prop]`.
* A função _extend( )_ copia isso "sem querer".
* O código acessa _config.source_, que resolve para _Object.prototype.source_.
* A string é inserida no HTML, via `onerror`.

<img width="1171" height="512" alt="image" src="https://github.com/user-attachments/assets/41d1eb7f-e1ca-4d85-a741-bb0911384f8e" />

Input necessário:

>{"source":{},"&#95;&#95;proto&#95;&#95;":{"source":"$`onerror=prompt(1)>"}}

## Fase E (14)

Não foi possível resolver.

## Fase F (15)

**Análise Inicial**

Essa fase é bem parecida com a fase 7, com a seguinte estrutura:

<img width="1456" height="752" alt="image" src="https://github.com/user-attachments/assets/0b218ae3-37db-4ec3-8e0f-1074fa83d7c7" />

Com o código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) sendo:

```
1  function escape(input) {
2      // sort of spoiler of level 7
3      input = input.replace(/\*/g, '');
4      // pass in something like dog#cat#bird#mouse...
5      var segments = input.split('#');
6  
7      return segments.map(function(title, index) {
8          // title can only contain 15 characters
9          return '<p class="comment" title="' + title.slice(0, 15) + '" data-comment=\'{"id":' + index + '}\'></p>';
10     }).join('\n');
11 }     
```
Assim como na fase 7, o _input_ pode ser dividido em partes com o uso do `#` e cada segmento pode conter até 15 caracteres dessa vez.

A principal diferença é que nesse nível, diferente da fase 7, não é possível usar os comentários JavaScript e as aspas serão cortadas devido ao _data-comment_.

**Análise final**

Uma solução para resolver a fase é usar comentários HTML em uma tag para esconder o que queremos executar. Além disso, essa técnica de comentário foi usada na fase 3 também.

<img width="1455" height="745" alt="image" src="https://github.com/user-attachments/assets/4157f84d-4b13-4d25-b711-a2f960d37a0a" />

Input necessário:

>`"><svg><!--#--><script><!--#-->prompt(1<!--#-->)</script>`

## Conclusão

Esse site é muito interessante para aprender sobre vulnerabilidades em JavaScript, pois ele desafia o usuário na prática como explorá-las. Além disso, existem diversas maneiras de quebrar as regras impostas por essas fases, principalmente com payloads mais curtos dos que foram usados, como sugerem as regras do jogo.

É importante ressaltar que entender sobre essas vulnerabilidades nos ajuda a evitá-las muitas das vezes, isso porque caso elas não sejam detectadas, podem ocasionar em:

* Roubo de dados do usuário,
* Mudança na página exibida,
* Redirecionamento para sites falsos,
* Códigos maliciosos sendo executados no navegador do usuário.

Por isso, saber como uma vulnerabilidade funciona pelo lado de quem está "atacando", é extremamente importante para quem irá realizar a segurança do site.
