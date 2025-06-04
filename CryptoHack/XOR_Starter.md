# XOR Starter
###### Solved by @Juh-s
>This is a CTF about Cryptography, XOR
## Sobre o desafio
XOR is a bitwise operator which returns 0 if the bits are the same, and 1 otherwise. In textbooks the XOR operator is denoted by ⊕, but in most challenges and programming languages you will see the caret `^` used instead.

![image](https://github.com/user-attachments/assets/f67c9e28-b472-4ca1-9dd2-75902b7f44fe)

For longer binary numbers we XOR bit by bit: `0110 ^ 1010 = 1100`. We can XOR integers by first converting the integer from decimal to binary. We can XOR strings by first converting each character to the integer representing the Unicode character.

Given the string `label`, XOR each character with the integer `13`. Convert these integers back to a string and submit the flag as `crypto{new_string}`.

Hint: The Python `pwntools` library has a convenient `xor()` function that can XOR together data of different types and lengths. But first, you may want to implement your own function to solve this.

Tradução:

XOR é um operador bit a bit que retorna 0 se os bits forem iguais e 1 caso contrário. Em livros didáticos, o operador XOR é denotado por ⊕, mas na maioria dos desafios e linguagens de programação, você verá o acento circunflexo `^` usado em vez disso.

Para números binários mais longos, fazemos XOR bit a bit: `0110 ^ 1010 = 1100`. Podemos fazer XOR com inteiros convertendo primeiro o inteiro de decimal para binário. Podemos fazer XOR com strings convertendo primeiro cada caractere para o inteiro que representa o caractere Unicode.

Dada a de string `label`, faça XOR com cada caractere com o inteiro `13`. Converta esses inteiros de volta para uma string e envie a bandeira como `crypto{nova_string}`.

Dica: A biblioteca `pwntools` do Python tem uma função `xor()` conveniente que pode realizar XOR entre dados de diferentes tipos e tamanhos. Mas primeiro, você pode querer implementar sua própria função para resolver isso.

## Solução
**Introdução**

O exercício nos explica como funciona o operador [XOR](https://pt.wikipedia.org/wiki/Ou_exclusivo) e apresenta um longo número binário para que possamos utilizar o operador, bit a bit.

**Análise Inicial**

Observando a dica dada no desafio, podemos ver que é possível descobrir a flag utilizando a linguagem [Python](https://pt.wikipedia.org/wiki/Python), preenchendo-a com as informações dadas no enunciado.

**Análise Final**

Implementando o código no [Visual Studio Code](https://pt.wikipedia.org/wiki/Visual_Studio_Code) para solucionar o problema, temos:

![Screenshot 2025-06-04 135416](https://github.com/user-attachments/assets/532e16b4-86a9-4ffa-887b-b4dff74868c9)

Explicação por linhas:

```
1  original = "label"
```
Essa linha define uma [string](https://pt.wikipedia.org/wiki/Cadeia_de_caracteres) chamada original, com o valor `label` dado no enunciado. Essa é a mensagem que será transformada com o uso do operador XOR.

```
3  xor_value = 13
```
Define a chave do XOR, que será usada para codificar cada caractere da string `original`. O valor `13` será aplicado bit a bit em cada caractere com a operação XOR.

```
5  string = ''.join([chr(ord(c)^xor_value) for c in original])
```
Essa linha faz a tranformação da string `original` usando o operador XOR.
* `for c in original`: Percorre cada caractere "c" da string "label".
* `ord(c)`: Converte o caractere "c" em seu código [ASCII](https://pt.wikipedia.org/wiki/ASCII).
* `ord(c)^xor_value`: Aplica a operação XOR bit a bit entre o valor ASCII de "c" e o valor de `xor_value` que é `13`.
* `chr(  )`:Converte o resultado numérico de volta em caractere.
* `[  for c in original]`: Cria uma lista de caracteres já transformados.
* `string = ''.join([  ])`: Junta todos os caracteres em uma frase chamada `string`.

```
7  print(string)
```
Imprime o resultado final da `string` codificada com XOR.

**Solução final**

Ao rodar o código, obtemos a palavra `aloha` e então, basta digitar no modelo pedido pelo desafio: `crypto{nova_string}`.

A flag do desafio fica:

>`crypto{aloha}`

**Aplicação no dia a dia**

Solucionar um desafio como esse é de extrema importância, já que a criptografia com o operador XOR representa um conceito fundamental em segurança da informação. Alguns exemplos tanto para uso pessoal ou para empresas, são: Proteção de dados pessoais, arquivos armazenados com senha e desenvolvimento de software seguro
