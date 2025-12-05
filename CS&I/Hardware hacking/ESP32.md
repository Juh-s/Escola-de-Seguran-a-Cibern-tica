# CTF Hardware Hacking
###### Solved by @Juh-s
>This is a CTF about Hardware Hacking
## Sobre o desafio

O desafio consiste em obter acesso a um dispositivo desconhecido com um chip escrito "ESP32" conectado via USB.

A intenção é encontrar uma flag no formato "DWCT{...}", onde o acesso inicial era apenas físico, sem credenciais de rede ou documentação.

## Introdução

Ao conectar o dispositivo na porta USB, realizei a identificação inicial para confirmar o driver e o endereço da porta. 

<img width="887" height="198" alt="image" src="https://github.com/user-attachments/assets/b1a93a5c-8aa1-4947-a1f5-b134521f40a9" />

Depois disso, tentei estabelecer uma conexão serial para verificar os logs de inicialização:

<img width="926" height="526" alt="image" src="https://github.com/user-attachments/assets/6db7fcdf-044c-4f96-8f82-5e92b9f2def4" />

Pórem, como nada apareceu, tentei reiniciar o ESP32 e ver o que aparecia.

<img width="726" height="292" alt="image" src="https://github.com/user-attachments/assets/f5bb1a8f-9c9e-434b-94e2-fb53139dea19" />

O dispositivo reiniciou e exibiu logs que revelavam o início de um [Access Point (AP)](https://www.cisco.com/c/pt_br/solutions/small-business/resource-center/networking/what-is-access-point.html) com o SSID (Service Set Identifier):

```
SSID: Curso-Hardware-Hacking-1
IP do AP: 192.168.4.1
```

**Análise Inicial**

A primeira coisa útil a ser feita quando fazemos análise de dispositivos, é extrair o firmware. Para isso, usei a ferramenta `esptool.py`.

Usei o seguinte comando no terminal para a extração: `esptool.py --port /dev/ttyUSB0 --baud 115200 read_flash 0 0x400000 firmware_dump.bin`

<img width="937" height="312" alt="image" src="https://github.com/user-attachments/assets/281efbb5-5c3c-4bfe-ab50-7810fc7847e7" />

O próximo passo foi tentar extrair a string `DWCT` desse firmware extraído, porém não houve nenhum retorno.

Com isso, utilizei o seguinte comando: `strings -n 8 firmware_dump.bin > analise_completa.txt`. Isso porque o arquivo firmware baixado é um arquivo binário, fazendo com que seja possível ver apenas símbolos ou letras ilegíveis no bloco de notas para que seja feita uma análise.

* O comando `strings` varre esse arquivo binário procurando sequências de caracteres imprimíveis.
* `-n 8` É um filtro que mostra apenas palavras que tenham no mínimo 8 letras, excluindo possíveis "lixos" que possam aparecer.
* `> analise_completa.txt` Salva o resultado num arquivo de texto para analisarmos.

Depois disso, ao acessar o arquivo de texto, buscando por `html`, temos:

<img width="946" height="821" alt="image" src="https://github.com/user-attachments/assets/fa1655be-e283-415b-adbc-a211b449c836" />

O motivo de buscarmos por html é porque o arquivo de dump é muito grande e confuso, logo, a senha precisava estar armazenada perto do código que pediria essa senha.

Encontrando a página de login no texto, basta olhar as linhas anteriores para encontrar as credencias.

<img width="650" height="112" alt="image" src="https://github.com/user-attachments/assets/dd0f2083-9da7-4a43-a1b4-82325ecd3ef7" />

* `a588c7988a58fe468490384fc0019ccb93bbf3f88305911ed2ac232c9e39ab898280b7a48c` O hash do usuário.
* `e1df84ccf136ce76e6cf481df05ef4ff` O hash da senha do site.
*  `administrator` O nome de usuário para login.
*  `G7pA93tLxQv2` A senha para conectar no Wi-Fi.

Ao conectar ao Wi-Fi pela senha informada:

<img width="442" height="142" alt="image" src="https://github.com/user-attachments/assets/6f7a26ce-4ad7-46af-97da-f0fd1cf6fab8" />

Foi possível acessar o IP informado anteriormente: `192.168.4.1`. Porém ao testar a senha usada para conectar ao Wi-Fi não foi possível realizar o login. Então, analisando o hash do arquivo pedi para o [Gemini](https://gemini.google.com/app) identificar qual seria.

<img width="765" height="212" alt="image" src="https://github.com/user-attachments/assets/3e2af0dd-85d8-4416-a78d-e5af6abb8157" />

Usando o site [CrackStation](https://crackstation.net/), consegui quebrar o hash da senha do site:

<img width="1269" height="419" alt="image" src="https://github.com/user-attachments/assets/d76b61c9-ffcf-4acd-9ad8-a89d86f380e6" />

Digitando as credenciais: `administrator` no usuário e `preparo` na senha, conseguimos efetuar o login.

<img width="577" height="247" alt="image" src="https://github.com/user-attachments/assets/9e6f1f96-2c39-4447-97a4-8dc31edc252f" />

## Solução

A flag encontrada do desafio foi:

<img width="407" height="55" alt="image" src="https://github.com/user-attachments/assets/03fe9ab5-a084-4161-8b63-20d03750ae81" />

>`DWCT{n00b_pR0_h4rdw4r3_h4ck1ng_vc_3h}`

**Aplicação no dia a dia**

A exploração realizada neste desafio destacou algumas falhas comuns no desenvolvimento de firmware.
Para impedir que ataques como esse sejam feitos em um produto real, as seguintes camadas de segurança são indispensáveis:

* `Ativar o recurso de Flash Encryption (nativo no ESP32)`: Se um atacante fizer o dump físico, o arquivo resultante será aleatório e seria impossível de ser analisado com strings ou engenharia reversa.
*  `Utilizar provisionamento dinâmico e armazenamento seguro`: O dispositivo deve sair de fábrica sem senhas, essas credenciais são salvas na partição NVS (Non-Volatile Storage) que também deve estar criptografada.
*  `Utilizar algoritmos modernos com "Sal"`: Usar algoritmos lentos propositalmente e adicionar um "Salt" (dados aleatórios) a cada senha antes de gerar o hash, pois o hash seria único e levaria anos para ser quebrado por força bruta.
