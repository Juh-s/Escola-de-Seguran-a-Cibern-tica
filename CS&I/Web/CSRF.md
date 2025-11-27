# CSRF
###### Written by @Juh-s
>This is an explanation of CSRF
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

## Outras técnicas adicionais de exploração

**Exploração via XMLHttpRequest (AJAX)**

Ataques CSRF também podem ser executados através de requisições [AJAX](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/Network_requests). O ataque engana o navegador para realizar ações em um site confiável, onde o usuário está logado, sem o conhecimento dele. Isso porque, mesmo com a existência da [Política de Mesma Origem (SOP)](https://web.dev/articles/same-origin-policy?hl=pt-br), que normalmente bloqueia requisições entre origens diferentes, é possível usar JavaScript para enviar dados e cabeçalhos personalizados de forma silenciosa, executando ações na conta da vítima.

**Política de Mesma Origem (SOP) e Compartilhamento de Recursos entre Origens (CORS) Bypass**

Configurações incorretas de [CORS](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/CORS) podem anular as proteções da SOP, permitindo o CSRF. Se a política CORS for muito permissiva, o servidor aceitará requisições vindas de sites controlados por atacantes. Além disso,  não é possível configurar o servidor para aceitar qualquer origem, o caractere `*` e, ao mesmo tempo, aceitar credenciais. Essa combinação é bloqueada por padrão nas especificações do CORS.

**Bypass do Cabeçalho Referer**

É importante ressaltar o perigo da validação do cabeçalho Referer, que indica a URL da página anterior, como medida de segurança, isso porque usar apenas a checagem do Referer é uma defesa fraca. Já que o cabeçalho não é confiável, pois pode ser alterado, omitido ou removido completamente por extensões de navegador, ferramentas de privacidade ou meta tags, fazendo com que a validação do servidor falhe ou seja contornada.

## Mecanismos de defesa

Existem diversas formas de defesa contra ataques CSRF, para Pentesters temos:
* Testes CSRF: Tentar forçar ações não autorizadas para ver se as defesas do site aguentam.
* Validação de limites: Verificar se o servidor realmente confere os dados enviados pelo usuário e se exige o token anti-CSRF.
* Análise de cabeçalhos de segurança: Analisar se as configurações de segurança do navegador (CORS e Referer) estão ativas e corretas.
* Testes de gerenciamento de sessão: Garantir que o cookie/token da sessão do usuário é criado e transportado de forma segura, sem chance de roubo.
* Cenários de exploração CSRF: Tentar métodos criativos de ataque, como esconder o link malicioso dentro de uma imagem ou usar partes confiáveis do site contra ele mesmo.

Para programadores seguros:
* Tokens Anti-CSRF: Colocar um código secreto e único em cada formulário, onde o servidor só aceitará a ação se o código estiver lá e for válido.
* Atributo de Cookies SameSite: Configurar os cookies como 'Strict' ou 'Lax'. Isso impede que o navegador envie os cookies da sessão quando o usuário clica em links vindos de outros sites.
* Política de Referenciador: Configurar o servidor para verificar de onde o usuário veio antes de aceitar a requisição, garantindo que a fonte é confiável.
* Política de Segurança de Conteúdo (CSP): Usar a Content Security Policy (CSP) para criar uma "lista branca" de fontes permitidas, impedindo que scripts maliciosos de fora rodem no site.
* Padrão de Duplo Envio de Cookie: Enviar o token em dois lugares ao mesmo tempo: no cookie e no formulário. O servidor comparará os dois, se não forem iguais, bloqueará.
* Implementação de CAPTCHAS: Em ações críticas, como login ou transferências, deve-se obrigar o usuário a resolver um desafio visual para provar que não é um ataque automático.

**Aplicação no dia a dia**

A exploração de CSRF apresentada neste curso demonstra que mecanismos de proteção isolados, como uma validação simples de Referer ou uma implementação falha de Double Submit Cookie, não são suficientes para garantir a integridade de uma aplicação web moderna.

No dia a dia do desenvolvimento de software, a compreensão profunda do CSRF é muito importante. Um simples descuido na validação de origem pode permitir que atacantes transformem a confiança do navegador, que envia cookies de sessão automaticamente, em um ataque contra o próprio usuário. Isso pode resultar em ações críticas não autorizadas, como alterações de senha, transações financeiras ou modificações em configurações de infraestrutura.

Portanto, a segurança eficaz contra CSRF no mundo real exige uma estratégia de defesa em profundidade. Não basta confiar em uma única camada, é necessário combinar o uso de Tokens Anti-CSRF que sejam fortes, a configuração correta de cookies com atributos SameSite (Strict/Lax) e, para ações sensíveis, a exigência de reautenticação ou CAPTCHAs. Dessa forma, os ataques de CSRF serão mais difíceis de acontecer. 
