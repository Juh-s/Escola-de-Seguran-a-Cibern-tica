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

Essa técnica conhecida como "exploração de link/imagem oculta" é feita da seguinte forma:

* O atacante insere uma imagem de 0x0 pixels ou um link em uma página web que é quase indetectável pelo usuário.
* O elemento da imagem é definido para uma URL de destino feita para agir em nome do usuário sem que o próprio perceba.
* Um exemplo do que pode ser feito a partir disso é o roubo de cookies.

**Double Submit Cookie Bypass**

O servidor gera um token aleatório e o envia ao cliente de duas formas simultâneas:
`Como um Cookie` e `dentro de um campo oculto (hidden field) no formulário HTML`. Com isso, quando o usuário envia o formulário, o servidor compara o valor do cookie com o valor do campo do formulário. Se forem idênticos, a requisição é aceita, caso contrário, é rejeitada.

Apesar de ser uma defesa eficiente, existem alguns cenários onde essa técnica pode ser quebrada:

* Man in the Middle (Sequestro de Sessão): Se a conexão não for segura, um atacante pode interceptar o tráfego e roubar o token.
* Subversão da "Same-Origin Policy": Se o atacante controlar um subdomínio do site alvo, ele pode conseguir definir cookies para o domínio principal, enganando a validação.
* Exploração de XSS (Cross-Site Scripting): Se o site tiver falhas de XSS, o atacante pode usar scripts maliciosos para ler o token do cookie ou da página e forjar uma requisição válida.
* Previsibilidade do Token: Se o algoritmo que gera o token for fraco, o atacante pode adivinhar o próximo token ou alterar o processo de geração.
* Injeção de Cookie via Subdomínio: Técnica onde um subdomínio comprometido injeta um cookie falso no navegador do usuário, fazendo o servidor principal acreditar que ele é autêntico.

**Samesite Cookie Bypass**

O curso introduz o atributo [SameSite](https://web.dev/articles/samesite-cookies-explained?hl=pt-br) nos cookies como uma defesa crucial contra vazamento de dados entre origens (cross-origin), CSRF e XSS.

Esse atributo instrui o navegador sobre quando ele deve, ou não, enviar o cookie, dependendo do contexto da requisição, ou seja, se ela vem do mesmo site ou de um site externo. Com isso, existem três configurações possíveis: Strict, Lax e None.

* Lax (O Vizinho Amigável): Tem um nível de proteção moderado, geralmente é o padrão dos navegadores modernos. Permite o envio de cookies em navegações de "nível superior" (como clicar em um link para ir ao site) e métodos HTTP seguros (GET, HEAD, OPTIONS). A defesa é feita pelo bloqueio do envio de cookies em métodos perigosos vindos de fora, como POST. Isso mitiga muitos ataques CSRF, pois impede que um formulário malicioso externo use o cookie da sessão.

* Strict (O Guarda Vigilante): Tem o nível de proteção máximo. O cookie nunca é enviado em requisições feitas por outros sites (cross-site). Ele só é transmitido se a requisição se originar exatamente do mesmo site que definiu o cookie. Ele é ideal para dados muito sensíveis, garantindo isolamento total.

* None (O Viajante Despreocupado): Tem o nível de proteção mínimo. O cookie é enviado em todas as requisições, tanto as de originais que definiram o cookie quanto as cross-site. É usado quando serviços precisam funcionar em diferentes domínios. Para usar SameSite=None, é obrigatório usar também o atributo [Secure](https://owasp.org/www-community/controls/SecureCookieAttribute), para evitar interceptações.

**Aplicação no dia a dia**

