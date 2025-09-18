# Secrets
###### Solved by @Juh-s
>This is a CTF about Web Exploitation and SSTI
## Sobre o desafio

I made a cool website where you can announce whatever you want! Try it out!

I heard templating is a cool and modular way to build web apps! Check out my website [here](http://rescued-float.picoctf.net:51371/)!

Hints: Server Side Template Injection

**Tradução**:

Eu fiz um site legal onde você pode anunciar o que quiser! Experimente!

Eu ouvi que a utilização de templates é uma maneira legal e modular de construir aplicativos web! Confira meu site [aqui](http://rescued-float.picoctf.net:51371/)!

Dicas: Server Side Template Injection

## Solução
**Introdução**

Ao acessar o site dado no enunciado, esse é o conteúdo:

<img width="627" height="195" alt="image" src="https://github.com/user-attachments/assets/ab8b8ed1-eb8f-4805-8f02-4e0bb15525df" />

Além disso, pela dica passada é possível saber que a solução do desafio será através de [Server Side Template Injection (SSTI)](https://portswigger.net/web-security/server-side-template-injection).

Um teste de como o site funciona:

<img width="556" height="186" alt="image" src="https://github.com/user-attachments/assets/4339e3cd-c5e1-4ae8-b659-fe4363939df6" /> <img width="486" height="307" alt="image" src="https://github.com/user-attachments/assets/bb4521e4-2455-4c5a-be2b-42eab5230d1c" />

**Análise Inicial**

Uma informação muito importante para saber se um servidor é vulnerável a SSTI é analisar essa tabela:

<img width="640" height="386" alt="image" src="https://github.com/user-attachments/assets/d9da9aa9-b5b2-4c66-a7d0-745310fde3f7" />

**Solução**

Feito isso, conseguimos obter a flag:

>`picoCTF{succ3ss_@h3n1c@10n_39849bcf}`

**Aplicação no dia a dia**
