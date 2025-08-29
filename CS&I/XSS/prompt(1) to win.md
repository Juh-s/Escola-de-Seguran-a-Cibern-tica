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
>`"><script>prompt(1)</script>`

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

...

**Análise final**

...
**Conclusão**
...
Essa vulnerabilidade é perigosa pois permite que:
* haja roubo de dados do usuário,
* seja possível modificar a página exibida,
* haja redirecionamento para sites falsos,
* qualquer código malicioso possa ser executado no navegador do usuário.
