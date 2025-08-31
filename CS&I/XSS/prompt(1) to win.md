# prompt(1) to win
###### Solved by @Juh-s
>This is a game about XSS injection
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

Como não dá para usar o mesmo método para resolver a questão, porque se o comando `<script>` for inserido ele será deletado. 

...

**Análise final**

...

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

**Análise Inicial**

A estrutura desta fase é a seguinte:

<img width="1447" height="747" alt="image" src="https://github.com/user-attachments/assets/0b75ae20-3810-4d0d-ab1c-8ea045a69425" />

E o código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) que a representa é:

```
1  function escape(input) {
2      // make sure the script belongs to own site
3      // sample script: http://prompt.ml/js/test.js
4      if (/^(?:https?:)?\/\/prompt\.ml\//i.test(decodeURIComponent(input))) {
5          var script = document.createElement('script');       
6          script.src = input;
7          return script.outerHTML;
8      } else {
9          return 'Invalid resource.';
10     }
11  }
``` 
Dessa vez o código é um pouco maior do que o comum, então é valido entender o que cada linha faz.

```
1  function escape(input) {
```
Define a função que recebe um valor _input_ como entrada
```
2      // make sure the script belongs to own site
3      // sample script: http://prompt.ml/js/test.js
```
Comentários que expressam que a função só aceitará scripts que vierem to próprio site `https://prompt.ml/`, além de dar um exemplo de URL que seria aceita.
```
4      if (/^(?:https?:)?\/\/prompt\.ml\//i.test(decodeURIComponent(input))) {
```
Irá checar se a URL começa com `http://`, `https://` ou só `//` e em seguida vem o `prompt.ml`. Depois o `.test( )` retorna _true_ caso o começo tenha sido digitado no padrão correto. Além disso, o `decodeURIComponent(input)` irá decodificar componentes [URI](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) em caracteres especiais.
```
5          var script = document.createElement('script');       
```
Se passar pela validação, irá gerar um novo `<script>`.
```
6          script.src = input;
```
Coloca na variável _scr_ do script, o que foi digitado.
```
7          return script.outerHTML;
```
Retorna o HTML completo com as alterações feitas pela função.

Para testar a funcionalidade da validação da função, é possível digitar `prompt(1)` e analisar o que retornará.

<img width="1450" height="750" alt="image" src="https://github.com/user-attachments/assets/73206bfa-1c8e-4086-8dec-4617161db64c" />

Exatamente o que era esperado, a função retorna _Invalid resource._ para essa entrada.

**Análise final**

...

Input necessário:
>

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

>"><svg/a=#"onload='/*#*/prompt(1)'

## Fase 8

**Análise Inicial**

Segue a estrutura da fase 8:



Seu código em [JavaScript](https://pt.wikipedia.org/wiki/JavaScript) é representado pelas linhas:

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


**Análise final**





Input necessário:

>

## Conclusão
...

Existem diversas maneiras de quebrar as regras impostas por essas fases, principalmente com payloads mais curtos dos que foram usados, como sugerem as regras do jogo. 

Essa vulnerabilidade é perigosa pois permite que:
* haja roubo de dados do usuário,
* seja possível modificar a página exibida,
* haja redirecionamento para sites falsos,
* qualquer código malicioso possa ser executado no navegador do usuário.
