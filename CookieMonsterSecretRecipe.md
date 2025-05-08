# Cookie Monster Secret Recipe
###### Solved by @Juh-s
>This is a CTF about Web Exploitation
## Sobre o desafio
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe?
You can access the Cookie Monster [here](http://verbal-sleep.picoctf.net:60118/) and good luck.
## Solução
**Introdução**

Esse desafio é resolvido através de um site, onde o usuário pode realizar o login para acessar a "Receita Secreta do Monstro do Cookie".

**Análise Inicial**

Ao digitar um nome de usuário e senha qualquer, o site nos mostra a seguinte mensagem:

![image](https://github.com/user-attachments/assets/6e49b70a-5677-468c-a25a-58a97cf48e81)

O que nos leva a checar as permissões dos cookies desse site.

**Solução**

Para solucionar o desafio e encontrar a receita secreta, basta vasculhar os cookies do site.

![image](https://github.com/user-attachments/assets/d31fe1b7-c4f7-4269-8abb-7b2f86ace24d)

Inspecionando o conteúdo de `secret_recipe`, obtemos um código completo: *cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzX0E5NjRBMTM0fQ%3D%3D* e ao tentar decodificar através do CyberChef utilizando a Base64, ele nos dá a seguinte flag:

>`picoCTF{c00k1e_m0nster_l0ves_c00kies_A964A134}`

