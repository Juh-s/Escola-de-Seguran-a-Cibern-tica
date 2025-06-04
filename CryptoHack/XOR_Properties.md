# XOR Properties
###### Solved by @Juh-s
>This is a CTF about Cryptography, XOR, XOR Properties
## Sobre o desafio
In the last challenge, you saw how XOR worked at the level of bits. In this one, we're going to cover the properties of the XOR operation and then use them to undo a chain of operations that have encrypted a flag. Gaining an intuition for how this works will help greatly when you come to attacking real cryptosystems later, especially in the block ciphers category.

There are four main properties we should consider when we solve challenges using the XOR operator

![image](https://github.com/user-attachments/assets/cda1fa69-f207-47e3-b73f-a0aa367a8f54)

Let's break this down. Commutative means that the order of the XOR operations is not important. Associative means that a chain of operations can be carried out without order (we do not need to worry about brackets). The identity is 0, so XOR with 0 "does nothing", and lastly something XOR'd with itself returns zero.

Let's put this into practice! Below is a series of outputs where three random keys have been XOR'd together and with the flag. Use the above properties to undo the encryption in the final line to obtain the flag.

![image](https://github.com/user-attachments/assets/f14525d8-6754-48e5-aa9d-1ed2d96cdac2)

Hint: Before you XOR these objects, be sure to decode from hex to bytes.

Tradução:

No último desafio, você viu como o XOR funciona no nível dos bits. Neste, vamos abordar as propriedades da operação XOR e depois usá-las para desfazer uma cadeia de operações que criptografaram uma flag. Ganhar uma intuição sobre como isso funciona ajudará muito quando você começar a atacar sistemas criptográficos reais mais tarde, especialmente na categoria de cifras de bloco.

Existem quatro propriedades principais que devemos considerar ao resolver desafios usando o operador XOR.

Vamos analisar isso. Comutativo significa que a ordem das operações XOR não é importante. Associativo significa que uma cadeia de operações pode ser realizada sem ordem (não precisamos nos preocupar com parênteses). A identidade é 0, então XOR com 0 "não faz nada", e por último algo XOR'd consigo mesmo, retorna zero.

Vamos colocar isso em prática! Abaixo está uma série de saídas onde três chaves aleatórias foram XOR'd juntas e com a flag. Use as propriedades acima para desfazer a criptografia na linha final para obter a flag.

Dica: Antes de aplicar XOR a esses objetos, certifique-se de decodificá-los de hexadecimal para bytes.

## Solução
**Introdução**

O exercício nos explica como funcionam as [propriedades do XOR](https://pt.wikipedia.org/wiki/Ou_exclusivo) para que possamos usá-las para desfazer uma cadeia de operações que criptografam a `flag`.

**Análise Inicial**

Observando a dica dada no desafio, podemos ver que é possível realizar as operações para descobrir a flag utilizando a linguagem [Python](https://pt.wikipedia.org/wiki/Python),

**Análise Final**

Implementando o código no [Visual Studio Code](https://pt.wikipedia.org/wiki/Visual_Studio_Code) para solucionar o problema, temos:

![image](https://github.com/user-attachments/assets/33831839-1656-4b25-bc72-830f8bd9950b)


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

Solucionar um desafio como esse é de extrema importância, já que a criptografia com o operador XOR representa um conceito fundamental em segurança da informação. Alguns exemplos tanto para uso pessoal ou para empresas, são: Proteção de dados pessoais, arquivos armazenados com senha e desenvolvimento de software seguro.
Dessa forma, mesmo que o XOR seja uma operação simples, entender como ela funciona é essencial para a criptografia e outras áreas da informação.

