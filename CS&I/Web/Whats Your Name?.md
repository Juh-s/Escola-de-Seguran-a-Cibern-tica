# Whats Your Name?
###### Solved by @Juh-s
>This is a challenge about Web Exploitation, XSS and CSRF
## Sobre o desafio

This [challenge](https://tryhackme.com/room/whatsyourname) will test client-side exploitation skills, from inspecting Javascript to manipulating cookies to launching CSRF/XSS attacks. To start the VM, click the Start Machine button at the top right of the task.

You will find all the necessary tools to complete the challenge, like Nmap, PHP shells, and many more on the AttackBox. Also, please add the hostname `worldwap.thm` in the `hosts` file.

_"Never click on links received from unknown sources. Can you capture the flags and get admin access to the web app?"_

What is the flag value after accessing the moderator account?

What is the flag value after accessing the admin panel?

**Tradução**

Este [desafio](https://tryhackme.com/room/whatsyourname) testará habilidades de exploração do lado do cliente, desde a inspeção de Javascript até a manipulação de cookies e o lançamento de ataques CSRF/XSS. Para iniciar a VM, clique no botão Iniciar Máquina no canto superior direito da tarefa.

Você encontrará todas as ferramentas necessárias para completar o desafio, como Nmap, shells PHP e muitas outras na AttackBox. Além disso, por favor, adicione o hostname `worldwap.thm` no arquivo `hosts`.

_"Nunca clique em links recebidos de fontes desconhecidas. Você consegue capturar as flags e obter acesso de administrador ao aplicativo web?"_

Qual é o valor da flag após acessar a conta de moderador?

Qual é o valor da flag após acessar o painel de administração?

## Introdução

Ao acessar a VM, a primeira coisa que precisa ser feita é adicionar o hostname `worldwap.thm` no arquivo `hosts` de acordo com as instruções. Para isso, basta entrar no terminal e digitar os comandos: `nano /etc/hosts` em seguida adicionar uma linha no final do arquivo com o IP da máquina alvo e o nome do domínio `10.65.169.52 worldwap.thm`.

<img width="820" height="543" alt="image" src="https://github.com/user-attachments/assets/77c72f4e-40c5-48e2-bebb-b1f21c53d447" />

Feito isso, basta acessar o site no navegador `http://worldwap.thm` em vez do IP numérico.

Ao acessar o site, essa é sua aparência:

<img width="1900" height="820" alt="image" src="https://github.com/user-attachments/assets/20d99199-99da-45a5-aec9-12b7496035b5" />

Podemos observar um botão no canto superior direito escrito `Register` e ao clicá-lo temos essa interface:

<img width="1902" height="770" alt="image" src="https://github.com/user-attachments/assets/39f528d3-f54b-4068-b212-86afa5372f79" />

É importante perceber a mensagem que está sendo informada: `You can now pre-register! Your details will be reviewed by the site moderator.` Ou seja, nossos dados de cadastro serão consultados pelo moderador do site.

**Análise Inicial**

Partindo para a interação com o site, devemos preencher o registro:

<img width="1901" height="767" alt="image" src="https://github.com/user-attachments/assets/9679dcac-4ca9-4382-96a7-de2dc0f1cabb" />

Logo em seguida ele pede para logarmos e clicando no botão `OK` somos redirecionados para essa página:

<img width="1898" height="773" alt="image" src="https://github.com/user-attachments/assets/5de5624d-b2de-41a6-aa68-e00c06756ccf" />

Porém, ao preenchermos os dados o site nos traz um aviso, mostrando que não é possível fazer o login, visto que não somos usuários verificados, assim como a mensagem na parte do registro nos informou.

<img width="1317" height="443" alt="image" src="https://github.com/user-attachments/assets/17bb6a04-c48d-4472-ac48-77e7b362b220" />

Além disso, o site nos trouxe uma informação importante, que é a existência de um subdomínio chamado `login.worldwap.thm`, porque ele também precisa ser adicionado no arquivo `/etc/hosts` para ser reconhecido.

Ao fazer esse procedimento novamente, é possível acessar o subdomínio.

Porém, ao acessar o endereço, o retorno é apenas uma página em branco. Dessa forma, uma ação válida em desafios web é analisar o código-fonte da página visto que muitas das vezes os desenvolvedores deixam links ou comentários no HTML que revelam arquivos ocultos.

Feito isso, obtemos a resposta para a página em branco:

<img width="1908" height="277" alt="image" src="https://github.com/user-attachments/assets/5602a248-7c7c-452f-b946-563c680cd8d7" />

O desenvolvedor deixou um comentário que diz: "login.php deve ser atualizado até segunda-feira para o redirecionamento correto." Ou seja, é necessário ajustar o endereço para que seja possível acessar a página corretamente.

Com isso, chegamos a essa interface:

<img width="1907" height="772" alt="image" src="https://github.com/user-attachments/assets/d0a4fce5-e85b-4da6-a4be-2dd93287369a" />

**Análise da primeira flag**

Ao chegarmos na página de login, precisaremos de um usuário válido para podermos acessar, mas além disso precisamos que o usuário seja o moderador ou o administrador para encontrarmos as flags que o desafio pede.

O que acontece é: O moderador vai abrir uma página administrativa para ver a lista de novos usuários (incluindo o usuário que registrei sendo "juh"). Se colocarmos um código malicioso no cadastro, quando o moderador olhar para ele, o navegador dele vai executar o código e nos enviar o cookie dele.

## Solução



**Aplicação no dia a dia**
