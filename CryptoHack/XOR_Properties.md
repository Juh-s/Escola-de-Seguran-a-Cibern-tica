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

Uma maneira de resolver o desafio, é utilizar a linguagem [Python](https://pt.wikipedia.org/wiki/Python), realizando as operações de XOR necessárias.

Além disso, o exercício nos mostra algumas propriedades específicas do operador XOR e pede para que sejam usadas para desfazer a criptografia na linha da `FLAG`.

Dessa forma, ao fazer a análise da linha:
```
FLAG ^ KEY1 ^ KEY3 ^ KEY2 = 04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf
```
É possivel realizar a operação XOR novamente com `KEY1`, `KEY3`, `KEY2` para usar as propriedades `Associative` (Associativa) e `Self-Inverse` (Inverso próprio), anulando esses termos e restando somente a `FLAG`.
 
**Análise Final**

Implementando o código no [Visual Studio Code](https://pt.wikipedia.org/wiki/Visual_Studio_Code) para solucionar o problema, temos:

![image](https://github.com/user-attachments/assets/0b251c0d-ef16-4e9e-9bbf-7ae8d25a4a52)


Explicação por linhas:

```
1  key1_hex = "a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313"
2  key1_key2_xor_hex = "37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e"
3  key2_key3_xor_hex = "c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1"
4  final_xor_hex = "04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf"
```
Nas linhas 1-4 definimos as entradas do desafio, que são valores [hexadecimais](https://pt.wikipedia.org/wiki/Sistema_de_numera%C3%A7%C3%A3o_hexadecimal), dados na segunda imagem. Dessa forma, é possível observar que a `key1` foi definida diretamente, diferente das outras que são resultados de operações XOR entre elas.

```
6  key1 = bytes.fromhex(key1_hex)
7  key1_key2_xor = bytes.fromhex(key1_key2_xor_hex)
8  key2_key3_xor = bytes.fromhex(key2_key3_xor_hex)
9  final_xor = bytes.fromhex(final_xor_hex)
```
Nas linhas 6-9, convertemos as [strings](https://pt.wikipedia.org/wiki/Cadeia_de_caracteres) hexadecimais para objetos de [bytes](https://pt.wikipedia.org/wiki/Byte), que são manipuláveis no Python para realizar o XOR byte a byte.
```
11  def xor_bytes(a, b):
12      return bytes(x ^ y for x, y in zip(a, b))
```
Essa é uma função auxiliar para aplicar o operador XOR entre dois [vetores](https://docs.python.org/pt-br/3/library/array.html) de bytes, ela pega dois blocos de bytes e devolve um novo bloco, onde cada byte é o resultado do XOR dos bytes correspondentes.

`zip(a, b)`: Emparelha os bytes correspondentes nos dois vetores.

`return bytes(x ^ y for x, y in zip(a, b))`: Retorna uma nova sequência de bytes com a operação feita byte a byte.
```
14  key2 = xor_bytes(key1, key1_key2_xor)
```
Usamos a propriedade do XOR na qual se o valor de `key1` é conhecido e temos `key1 ^ key2`, conseguimos obter o valor de `key2` realizando a conta `key1 ^ (key1 ^ key2) = key2`.
```
16  key3 = xor_bytes(key2, key2_key3_xor)
```
Da mesma forma que foi possível encontrar `key2` pela propriedade do XOR, esse passo deve ser repetido para achar o `key3`, agora usando os valores de `key2 e key3`.
```
18  step1 = xor_bytes(final_xor, key1)
19  step2 = xor_bytes(step1, key2)
20  flag = xor_bytes(step2, key3)
```
As linhas 18-20 representam o último passo antes de se obter a flag do desafio. O que elas fazem é desfazer a operação `FLAG ^ KEY1 ^ KEY2 ^ KEY3` utilizando a mesma properiedade de XOR que foi utilizada para descobrir os valores de `key2 e key3`, porém excluindo `key1`, depois `key2` e por fim `key3`, isolando a `FLAG`
```
22  print(flag.decode())
```
A flag final é exibida como uma string, porque ela está codificada em [ASCII](https://pt.wikipedia.org/wiki/ASCII).

**Solução final**

Ao rodar o código, ele nos entrega a flag já no modelo pedido pelo desafio:

>`crypto{x0r_i5_ass0c1at1v3}`

**Aplicação no dia a dia**

É muito importante entender as propriedades do XOR, afim de facilitar desafios como esse, além de ser fundamental em situações reais como: Engenharia reversa e análise de malware, protocolos de rede e detecção de falhas ou até mesmo na compactação e codificação de dados.
Ou seja, esse exercício ensina a manipular e desfazer operações XOR, que estão presentes em várias áreas da cibersegurança, desde a criptografia básica até a análise de malware e sistemas de proteção de dados.

