# CSRF
###### Solved by @Juh-s
>This is a CTF about Web Exploitation and SSTI
## Sobre o curso

O objetivo deste curso é ensinar como funciona a vulnerabilidade CSRF (Cross-Site Request Forgery), que traduzindo para o português seria: Falsificação de Solicitação Cruzada. Com isso, os quatro tópicos desta sala do [TryHackMe](https://tryhackme.com/room/csrfV2) são:

* Funcionamento da CSRF
* Tipos de ataque CSRF
* Payloads avançados para a exploração
* Mecanismos de defesa

## Definição

O ataque de CSRF explora a confiança que um site tem no navegador do usuário, onde o atacante cria uma requisição maliciosa e engana o usuário para que ela seja enviada ao site alvo. Desse modo, algumas formas de ataque são através de: Links maliciosos, formulários escondidos, requisições automáticas em recursos e ataques contra contas administrativas.

Dito isso, algumas das ações que podem ser exploradas são:

* Alterar senha ou e-mail da conta
* Realizar transferências bancárias
* Publicar conteúdo em redes sociais
* Comprar produtos em lojas online

Além de tudo, é importante mencionar que o atacante já conhece o formato dos pedidos da aplicação web para realizar uma tarefa específica e através disso, envia um link malicioso ao usuário.

## Tipos de ataque

**Traditional CSRF**

O Traditional CSRF, ou CSRF tradicional em português, acontece porque o navegador sempre envia informações de autenticação ou sessão junto com a requisição, sem perguntar ao usuário. Essas informações podem ser:

* Cookies de sessão (o caso mais comum).
* Parâmetros de URL (quando o site usa tokens ou identificadores na própria URL).
* Campos ocultos em formulários (hidden inputs que carregam dados da conta).
* Cabeçalhos HTTP automáticos (como Authorization em alguns sistemas antigos).

Um exemplo citado no curso é de que a vítima já está logada no site do banco dele e então os atacantes criam um link malicioso elaborado e o enviam por e-mail para a vítima. Com isso, a vítima abre o e-mail no mesmo navegador. Uma vez clicado, o link malicioso permite a transferência automática do valor do navegador da vítima para a conta bancária do atacante.

**XMLHttpRequest CSRF**

É o mesmo ataque CSRF tradicional, mas em vez de usar formulários normais, o invasor usa requisições assíncronas (AJAX, Fetch, XMLHttpRequest). Isso acontece em sites modernos que atualizam dados sem recarregar a página. O exemplo dado é o seguinte:

Um cliente de e-mail online, onde os usuários podem alterar suas preferências de e-mail sem recarregar a página. Se esse aplicativo online for vulnerável ao CSRF, um hacker pode criar uma falsa requisição HTTP assíncrona, geralmente uma solicitação POST, e alterar as preferências de e-mail da vítima, encaminhando toda a correspondência para um endereço malicioso.

Você está logada e pode mudar suas preferências sem recarregar a página. Ou seja:

* Um hacker cria um site malicioso com um script escondido.
* Esse script envia uma requisição AJAX para o servidor do e-mail, pedindo para alterar o encaminhamento das mensagens.
* Como o navegador já tem o cookie de sessão, o servidor acredita que foi o próprio usuário quem fez a alteração.
* Resultado: todos os e-mails passam a ser encaminhados para o endereço do atacante.

**Flash-based CSRF**

É um tipo de ataque CSRF que explorava falhas no Adobe Flash Player. O Flash era muito usado para vídeos, animações e conteúdo interativo, mas tinha várias vulnerabilidades que permitiam ataques. Essa técnica funcionava da seguinte forma:

* O atacante criava um arquivo Flash malicioso (.swf).
* Esse arquivo, quando carregado no navegador da vítima, podia enviar requisições não autorizadas para outros sites.
* Se a vítima estivesse logada em algum serviço, o Flash usava os cookies de sessão ou outros dados para enganar o servidor, igual ao CSRF tradicional.

## Técnicas de CSRF

Além dos tipos de ataque, o curso também mostra alguns tipos de exploração a partir de CSRF.

**Hidden Link/Image Exploitation**



**Aplicação no dia a dia**

