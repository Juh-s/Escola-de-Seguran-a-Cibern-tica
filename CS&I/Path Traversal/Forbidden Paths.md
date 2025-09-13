# Forbidden Paths
###### Solved by @Juh-s
>This is a CTF about Web Exploitation
## Sobre o desafio

Can you get the flag?

We know that the website files live in /usr/share/nginx/html/ and the flag is at /flag.txt but the website is filtering absolute file paths. Can you get past the filter to read the flag?

Here's the [website](http://saturn.picoctf.net:64894/).

**Tradução**:

Você pode pegar a flag?

Sabemos que os arquivos do site estão localizados em __/usr/share/nginx/html/__ e a flag está em __/flag.txt__, mas o site está filtrando caminhos de arquivos absolutos. Você consegue passar pelo filtro para ler a flag?

Aqui está o [site](http://saturn.picoctf.net:64894/).

## Solução
**Introdução**

Ao acessar o site informado:

<img width="508" height="401" alt="image" src="https://github.com/user-attachments/assets/1a32bfef-71dd-4767-94fa-1de7483511ef" />

Esse desafio envolve explorar caminhos de arquivos para acessar o arquivo `/flag.txt` sabendo que o site filtra caminhos de arquivos absolutos. Além disso, ele nos oferece alguns arquivos que podem ser acessados, sendo um deles o arquivo `/divine-comedy.txt`:

<img width="1437" height="967" alt="image" src="https://github.com/user-attachments/assets/ed6e532e-5927-45b7-977b-dbe631f9be28" />

Como o próprio exercício informa que a flag está em `/flag.txt`, ao digitar isso no campo `Read`:

<img width="477" height="393" alt="image" src="https://github.com/user-attachments/assets/c291d845-91a0-430f-8641-f22b770466e2" />

E ao clicar no botão, o site retorna:

<img width="513" height="155" alt="image" src="https://github.com/user-attachments/assets/304cd33c-9bd9-4011-906f-de003d3a2444" />

Isso porque existem esses filtros mencionados anteriormente.

**Análise Inicial**

Com a informação de que não é possível acessar o arquivo da flag de forma direta, será necessário usar a técnica de [Path Traversal](https://portswigger.net/web-security/file-path-traversal).

Para entendermos melhor o funcionamento dos diretórios, temos que:

* O caminho declarado na descrição é `/usr/share/nginx/html`.
* Ele é composto por 4 pastas abaixo da raiz.
* Elas são `usr`-`share`-`nginx`-`html`.
* 1ª = .. → /usr/share/nginx
* 2ª = .. → /usr/share
* 3ª = .. → /usr
* 4ª = .. → /

Dessa forma, o caminho relativo para acessarmos esse arquivo é `../../../../flag.txt`. O uso desse tipo de caminho é explicado [aqui](https://portswigger.net/web-security/file-path-traversal).

**Solução**

Para solucionar o desafio e encontrar a flag, é necessário digitar exatamente o caminho apresentado anteriormente: `../../../../flag.txt`

<img width="255" height="337" alt="image" src="https://github.com/user-attachments/assets/217646c9-bbad-4f39-aed5-7f2bc015c589" />

Portanto, basta clicar no botão `read` que o site retornará a seguinte informação:

<img width="506" height="73" alt="image" src="https://github.com/user-attachments/assets/3fb5e1bb-025e-43c5-a412-0265f1c8959b" />

Feito isso, conseguimos obter a flag:

>`picoCTF{7h3_p47h_70_5ucc355_e5fe3d4d}`

**Aplicação no dia a dia**

Esse tipo de acesso aos arquivos de um site é extremamente perigoso, visto que um usuário mal intencionado poderia acessar dados sensíveis, como informações pessoais ou do próprio servidor. Portanto, algumas técnicas que poderiam ser adotadas para evitar que isso aconteça são: 

* Validar o input do usuário antes de processá-lo, comparando-a com uma whitelist de valores permitidos.
* Usar API's que controlam o acesso a ficheiros.

Dessa forma, é possível realizar uma segurança mais avançada no ambiente em que esse ataque poderia ser realizado.
