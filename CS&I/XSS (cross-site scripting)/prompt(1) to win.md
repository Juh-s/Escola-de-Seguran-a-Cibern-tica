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

Este [jogo](https://prompt.ml/0) é um desafio de [XSS injection](https://www.kaspersky.com.br/resource-center/definitions/what-is-a-cross-site-scripting-attack). Isso porque o XSS é uma falha de segurança em sites e ao realizar esse procedimento é possível explorar erros de validação e escape de dados. Dessa forma, conseguimos fazer com que o navegador execute o comando `prompt(1)` sem precisar clicar em nada, permitindo que possamos avançar para a próxima fase.  

## Fase 1
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
Este código pega o que será digitado no campo _Enter input_ e insere no [HTML](https://pt.wikipedia.org/wiki/HTML) como o valor (na 4ª linha do código) do campo de saída do texto.

Um exemplo prático seria digitar no campo _Your name_, o nome `Juh`, e no campo _Enter input_ a palavra `teste`, para obtermos o seguinte:

<img width="1471" height="637" alt="image" src="https://github.com/user-attachments/assets/30261433-1cb0-4eb1-b9c7-7df3dc71e46b" />

Porém, ao observar melhor o JavaScript, é possível entender que ele pega o valor digitado e coloca dentro do HTML, sem verificar o uso dos caracteres especiais. Sendo assim, é fácil modificar o que o código está retornando.

**Análise final**

...

**Conclusão**
...
Essa vulnerabilidade é perigosa pois permite que:
* haja roubo de dados do usuário,
* seja possível modificar a página exibida,
* haja redirecionamento para sites falsos,
* qualquer código malicioso possa ser executado no navegador do usuário.
