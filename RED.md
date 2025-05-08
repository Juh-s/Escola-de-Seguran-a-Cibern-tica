# RED
###### Solved by @Juh-s
>This is a CTF about Forensics
## Sobre o desafio
RED, RED, RED, RED

Download the image: [red.png](https://challenge-files.picoctf.net/c_verbal_sleep/831307718b34193b288dde31e557484876fb84978b5818e2627e453a54aa9ba6/red.png)

## Solução
**Introdução**

A imagem dada pelo desafio é apenas um quadrado vermelho sem nenhuma informação adicional.

![red](https://github.com/user-attachments/assets/2d81f4ad-e5ca-4e9d-a67e-b6acbffedc05)

**Análise Inicial**

Como esse desafio nos entrega somente uma imagem, é necessário encontrar algum outro jeito de obter informações para solucionar o problema.

Utilizando o terminal para abrir o arquivo, obtemos o seguinte texto: 

![image](https://github.com/user-attachments/assets/d5ce1c8e-d55f-4aae-bce0-98e70931ca97)

**Análise Final**

Retirando do texto a parte que parece estar codificada, temos:

`cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==`

**Solução**

Para solucionar o problema, basta pegar a parte do texto codificada e utilizar o site [dCode](https://www.dcode.fr/cipher-identifier), para descobrir a cifra, que nesse caso será a `Base64`.

![image](https://github.com/user-attachments/assets/ae092cc4-ed16-4a64-b604-064d81380f90)

Decodificando a mensagem, obtemos a flag:

![image](https://github.com/user-attachments/assets/c97b21bb-4f0d-4d43-9b56-0ef1ba1b3803)


>`picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}`
