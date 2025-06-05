# Favourite byte
###### Solved by @Juh-s
>This is a CTF about Cryptography, XOR, XOR Properties, Hex
## Sobre o desafio
For the next few challenges, you'll use what you've just learned to solve some more XOR puzzles.

I've hidden some data using XOR with a single byte, but that byte is a secret. Don't forget to decode from hex first.

![image](https://github.com/user-attachments/assets/78b8239b-e658-4fef-be8f-fcb2271a19e3)

Tradução:

Para os próximos desafios, você usará o que acabou de aprender para resolver alguns quebra-cabeças XOR.

Eu escondi alguns dados usando XOR com um único byte, mas esse byte é um segredo. Não se esqueça de decodificar do hex primeiro.

## Solução
**Introdução**

O desafio menciona que a mensagem foi escondida por [XOR](https://pt.wikipedia.org/wiki/Ou_exclusivo) com um único [byte](https://pt.wikipedia.org/wiki/Byte) (um byte repetido ao longo da mensagem) e o objetivo é descobrir qual byte foi usado para encontrar a flag.

**Análise Inicial**

Para resolver o desafio é válido usar a linguagem [Python](https://pt.wikipedia.org/wiki/Python), seguindo os passos para desvendar a flag, começando pela dica dada no enunciado de decodificar a [string](https://pt.wikipedia.org/wiki/Cadeia_de_caracteres), que está no modelo [hexadecimal](https://pt.wikipedia.org/wiki/Sistema_de_numera%C3%A7%C3%A3o_hexadecimal).

Além disso, é dito que uma mensagem foi escondida em um único byte, o que nos leva a tentar todos os 256 valores possíveis de byte. Depois disso, usar XOR em cada byte com a mensagem e verificar se o resultado parece com o modelo que a flag é pedida: `crypto{FLAG}`.

 
**Análise Final**

Ao implementar o código no [Visual Studio Code](https://pt.wikipedia.org/wiki/Visual_Studio_Code) para solucionar o problema, temos:

![image](https://github.com/user-attachments/assets/0a879457-1057-4822-9d06-72967183cc4c)

Explicação por linhas:

```
1  hex_data = (
2      "73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d"
3  )
```
Nas linhas 1-3, `hex_data` armazena a string hexadecimal fornecida pelo desafio. Essa string é o texto encriptado que foi usado XOR com um único byte desconhecido.
```
5  cipher_bytes = bytes.fromhex(hex_data)
```
Essa linha converte a string hexadecimal em uma sequência de bytes reais.
```
7  def xor_with_byte(data, key_byte):
8      return bytes(b ^ key_byte for b in data)
```
As linhas 7-8 definem uma função chamada `xor_with_byte`, que recebe uma lista de bytes (`data`) e faz XOR de cada byte com `key_byte` (de 0 a 255) e o resultado disso é uma nova sequência de bytes.
```
10  for i in range(256):
```
Uma estrutura de repetição que começa um laço de 0 até 255. Testa todos os valores possíveis de um byte, porque o desafio utiliza somente um byte secreto como chave XOR.
```
11  decrypted = xor_with_byte(cipher_bytes, i)
```
Chama a função criada anteriormente `xor_with_byte(...)` e faz o XOR da mensagem inteira (`cipher_bytes`) com o byte atual (`i`).
```
12  try:
13      text = decrypted.decode()
```
As linhas 12-13 tentam decodificar os bytes resultantes para texto (string) usando `.decode()`. Caso o resultado de algum erro (bytes inválidos), elas direcionam o código para o `except` da linha 18.
```
14  if "crypto{" in text:
15      print(f"Chave: {i}")
16      print(f"Flag: {text}")
17      break
```
Se a string decodificada contém `"crypto{...}` significa que o código encontrou a flag correta do desafio, onde ele mostra a chave usada e o texto decifrado. Além disso, para o laço com o `break`, já que a flag foi encontrada.

**Solução final**

Ao rodar o código, ele nos entrega a flag já no modelo pedido pelo desafio:

>`crypto{0x10_15_my_f4v0ur173_by7e}`

**Aplicação no dia a dia**

Trazendo o desafio para uma situação real, esse desafio simula a quebra de uma cifra XOR com um único byte, que é uma técnica realmente usada em contextos fracos de segurança, como em softwares antigos ou mal desenvolvidos que utilizam uma chave fixa para proteger dados, o que é facilmente reversível. Ou seja, desafios como esse, nos preparam para análise forense, pentesting, malware analysis, entre outros, sendo base para problemas muito mais complexos no dia a dia.
