# Cookie Monster Secret Recipe
###### Solved by @Juh-s
>This is a CTF about Web Exploitation
## Sobre o desafio
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe?
You can access the Cookie Monster [here](http://verbal-sleep.picoctf.net:60118/) and good luck.

Tradução:

Cookie Monster escondeu sua receita ultrassecreta de biscoito em algum lugar de seu site. Como aspirante a detetive de biscoitos, sua missão é descobrir esse segredo delicioso. Você pode ser mais esperto que o Cookie Monster e encontrar a receita escondida?
Você pode acessar o Cookie Monster [aqui](http://verbal-sleep.picoctf.net:60118/) e boa sorte.

## Solução
**Introdução**

Esse desafio é resolvido através de um site, onde o usuário pode realizar o login para acessar a "Receita Secreta do Monstro do Cookie".

![image](https://github.com/user-attachments/assets/ac990945-185e-4958-9b9b-e13f7d986168)


**Análise Inicial**

Ao digitar um nome de usuário e senha qualquer, o site nos mostra a seguinte mensagem:

![image](https://github.com/user-attachments/assets/6e49b70a-5677-468c-a25a-58a97cf48e81)

A dica nos pergunta se já checamos os cookies. Logo, a primeira coisa lógica a se pensar, são os cookies do site. Dito isso, ao checar as permissões de cookies isso é mostrado:

![image](https://github.com/user-attachments/assets/90898d47-c15b-431a-9eb1-00b00e0ec5ea)


**Solução**

Para solucionar o desafio e encontrar a receita secreta, basta vasculhar os cookies do site.

![image](https://github.com/user-attachments/assets/d31fe1b7-c4f7-4269-8abb-7b2f86ace24d)

Inspecionando o conteúdo de `secret_recipe`, obtemos o código completo: 

**cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzX0E5NjRBMTM0fQ%3D%3D**

Ao tentar decodificar através do [CyberChef](https://cyberchef.io/) utilizando a cifra da [Base64](https://pt.wikipedia.org/wiki/Base64), ele nos dá a seguinte flag:

>`picoCTF{c00k1e_m0nster_l0ves_c00kies_A964A134}`

