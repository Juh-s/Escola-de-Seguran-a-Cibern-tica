# Flag Hunters
###### Solved by @Juh-s
>This is a CTF about Reverse Engineering
## Sobre o desafio
Lyrics jump from verses to the refrain kind of like a subroutine call. There's a hidden refrain this program doesn't print by default. Can you get it to print it? There might be something in it for you.
The program's source code can be downloaded [here](https://challenge-files.picoctf.net/c_verbal_sleep/9ae83ffa696e29c2b57d0373cb616bc097de3dda1e87e4780273e13de712abb6/lyric-reader.py).

Connect to the program with netcat:

$ nc verbal-sleep.picoctf.net 61138

## Solução
**Introdução**

Ao conectar ao programa do desafio, ele faz a leitura de uma letra de música, com uma interação "Crowd:", esperando que o usuário insira algum dado:

![image](https://github.com/user-attachments/assets/b8272bae-56fa-4ffa-869f-7643de5e2c67)

**Análise Inicial**

Ao digitar uma mensagem qualquer, o programa continua com a leitura da música, printando automaticamente o que foi digitado pelo usuário na tela após cada "Crowd:".
Dessa forma, é perceptível que é necessário digitar um dado específico após a interação do programa.

Ao analisar o código dado no enunciado, utilizando a dica dada, encontramos a parte escondida da música logo nas primeiras linhas do código.

![image](https://github.com/user-attachments/assets/74902ede-afb9-4dbd-ad48-4b7be7d34e65)

**Análise Final**

Ao fazer a leitura das linhas do código, conseguimos observar o que deve ser digitado no programa, para que a flag seja printada em uma das linhas da letra da música.

![image](https://github.com/user-attachments/assets/a636678a-8c1d-4a0c-a878-4941b174f793)

**Solução**

Como mostrado na linha 121, podemos usar o comando `RETURN 0` para fazer a leitura de uma linha específica. Porém, para que ele seja lido de fato como um comando, e não somente como uma string, é necessário digitar dessa forma: `teste;RETURN 0`.

Colocando os dados corretos quando o programa pede pelo "Crowd:", conseguimos fazer com que seja lida e printada a parte que contém a flag do desafio.

![image](https://github.com/user-attachments/assets/04c041f2-4d5b-4129-8e27-06c042cc453e)

>`picoCTF{70637h3r_f0r3v3r_710a5048}`
