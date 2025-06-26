# You either know, XOR you don't
###### Solved by @Juh-s
>This is a CTF about Cryptography, XOR
## Sobre o desafio
I've encrypted the flag with my secret key, you'll never be able to guess it.

Hint: Remember the flag format and how it might help you in this challenge!

```
0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150104
```

Tradução:

Eu encriptei a flag com minha chave secreta, você nunca vai conseguir adivinhá-la.

Dica: Lembre-se do formato da bandeira e como isso pode te ajudar neste desafio!

## Solução
**Introdução**

Após resolver os exercícios anteriores a esse, conseguimos entender como funciona o operador [XOR](https://pt.wikipedia.org/wiki/Ou_exclusivo) e da mesma forma que os outros, precisamos descriptografar a bandeira utilizando esse operador.

**Análise Inicial**

Observando a dica dada no desafio, é evidente que a forma em que é escrita a flag, é importante para a resolução do exercício.

Como sabemos o funcionamento do operador XOR, é possível observar que a mesma chave que utilizamos para criptografar o texto, é usada para descriptografar o mesmo.

[Funcionamento da chave no XOR](https://credited.com.br/glossario/xor-operador-logico-exclusivo-tecnologia/).

Para encontrar o valor dessa chave, podemos tentar descriptografar o texto. Primeiro, ao digita-lo no site [dcode](https://www.dcode.fr/base-64-encoding) descobrimos que ele está em [hexadecimal](https://pt.wikipedia.org/wiki/Sistema_de_numera%C3%A7%C3%A3o_hexadecimal). A partir dessa informação, é possível utilizar a operação [XOR](https://www.dcode.fr/xor-cipher) do site para descobrir a chave através do próprio texto criptografado.

A ferramenta que realiza operações de XOR no site, testa possíveis valores para a chave ao comparar os resultados da descriptografia com o formato de texto esperado da flag. Além disso, sabemos que o modelo das flags do site segue o padrão: `crypto{...`.

**Análise Final**

Ao selecionar a opção `Use the ASCII key`, que irá permitir a inserção de uma parte da frase final da flag já conhecida, temos que:

![image](https://github.com/user-attachments/assets/505957ba-9dac-43a9-b920-24d830bd000b)

Dessa forma, descobrimos o começo da chave que foi usada para criptografar o texto completo: `myXORkey`.

![image](https://github.com/user-attachments/assets/77ad3ff9-50f3-4d99-8223-5474c0deddac)

Ao realizar novamente a operação, dessa vez com o valor direto da chave descoberta, obtemos a flag final.

![image](https://github.com/user-attachments/assets/b710722e-a979-49ef-9e4e-f425f375e5f5)

![image](https://github.com/user-attachments/assets/c2998a1a-1d03-4706-8673-461b6e9f2890)


**Solução final**

É importante entender que o texto só foi descriptografado diretamente, devido a ferramenta do site, que repete a chave ao longo de todos os caracteres do texto criptografado. Isso é essencial, visto que a chave encontrada possui apenas 8 bytes, diferente do texto que possui vários bytes. Dessa forma, a flag do desafio é:

>`crypto{1f_y0u_Kn0w_En0uGH_y0u_Kn0w_1t_4ll}`

**Aplicação no dia a dia**

Esse desafio nos mostra o quão frágil a criptografia com o operador XOR pode ser, visto que foi possível descriptografar o texto a partir de uma pequena parte conhecida do mesmo, o que torna o sistema muito vulnerável.

Além disso, como esse tipo de operador é muito utilizado para fazer a segurança de um sistema, entender quais são as fragilidades do próprio é extremamente importante para que a proteção seja feita de forma eficaz e segura.
