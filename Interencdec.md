# interencdec
###### Solved by @Juh-s
>This is a CTF about Cryptography, Base64, Caesar
## Sobre o desafio
Can you get the real meaning from this file.

Download the file [here](https://artifacts.picoctf.net/c_titan/108/enc_flag).

Tradução:

Você pode obter o significado real deste arquivo?

Baixe o arquivo [aqui](https://artifacts.picoctf.net/c_titan/108/enc_flag).

## Solução
**Introdução**

Ao abrir o arquivo dado pelo desafio, obtemos um código claramente criptografado: `YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgyZzBOMm8yYXpZNWZRPT0nCg==`

**Análise Inicial**

Utilizando o [dCode](https://www.dcode.fr/cipher-identifier) para identificar qual cifra está sendo usada, descobrimos que será necessário decodificar o código pela [Base64](https://pt.wikipedia.org/wiki/Base64).

![image](https://github.com/user-attachments/assets/d73b606f-122a-4696-aecd-ed8eab536fc0)

Passando para o site [Base64 Decode and Encode](https://www.base64decode.org/) e colando o texto dado pelo desafio para decodificá-lo, é mostrado:

`b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2g0N2o2azY5fQ=='`

Dessa forma, tiramos apenas o que está citado no código, retirando o `b`, para tentar decodificar novamente a frase. Feito isso, esse outro código é retornado:

![image](https://github.com/user-attachments/assets/ee199ba2-b601-4d79-a11c-9b77f62a5a75)




**Solução final**

Como sabemos que o desafio é também sobre [Caesar cipher](https://en.wikipedia.org/wiki/Caesar_cipher), pegamos o resultado anterior e tentamos decodificá-lo usando essa cifra.

A resposta dada é exatamente a flag do desafio:

![image](https://github.com/user-attachments/assets/a6b96eb1-08fb-474b-981a-73fa67d773bc)


>`picoCTF{caesar_d3cr9pt3d_a47c6d69}`
