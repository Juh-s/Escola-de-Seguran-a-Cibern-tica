# x86 Assembly Crash Course
###### Written by @Juh-s
>This is a crash course in x86 assembly for training in malware reverse engineering.
## Objetivo do curso

A linguagem [Assembly](https://pt.wikipedia.org/wiki/Linguagem_assembly) é usada para se comunicar diretamente com o hardware de um computador, sendo uma linguagem de programação de baixo nível, mas também o nível mais alto de linguagem no qual um binário poder ser descompilado de forma confiável.

É importante entender os fundamentos dessa linguagem, visto que ao analisar uma amostra de malware, é mais provável que seja um binário compilado. Para entender o seu funcionamento, é possível descompilar o código, porém algumas informações podem ser perdidas durante esse processo. Com isso, a maneira mais correta que temos para um binário compilado, é o seu código Assembly.

Alguns tópicos que foram abordados no curso:

* Opcodes e operandos
* Instruções gerais de montagem
* Instruções aritméticas e lógicas
* Condicionais
* Instruções de ramificação

## Opcodes e operandos
**Explicação geral**

O código de um programa compreendido pela CPU, está no formato binário, uma sequência de números "1" e "0". Para torná-lo compreensível, é prático agruparmos uma série de 8 bits em dígitos hexadecimais, que são os bytes.

Dessa forma, as instruções que o computador está executando serão apenas números aleatórios em hexadecimal para um humano. Entre essas informações, existem opcodes e operandos.

**Opcodes**

Cada processador tem seu próprio conjunto de instruções e o Assembly é uma representação legível dessas instruções.

O desmontador [https://pt.wikipedia.org/wiki/Desmontador] funciona com esses opcodes para desmontar um programa, traduzindo-os em instruções de montagem para torná-los legíveis por humanos.

O exemplo dado no curso é: `mov eax, 0x5f`, que em um desmontador, se torna `40000:    b8 5f 00 00 00    mov eax, 0x5f`. 

* `040000:` corresponde ao endereço onde a instrução está localizada.
* `b8` refere-se ao opcode da instrução `mov eax`.
* `5F 00 00 00` indica outro operando `0x5f`.

**Tipos de operandos**

No geral, existem três tipos de operandos na linguagem Assembly.

* Immediate operands (Operandos imediatos): São valores fixos, que podem ser considerados constantes, diretamente definidos no código.
* Register operands (Operandos de Registros): Utilizam registradores da CPU para armazenar dados temporários.
* Memory operands (Operandos de memória): Referenciam endereços de memória, indicados por colchetes.

## Instruções gerais
**Explicação geral**

As instruções indicam à CPU qual operação deve ser executada, manipulando operandos vindos de registradores, memória ou valores imediatos e armazenam os resultados novamente em registradores ou na memória.

**MOV instruction**

Usada para mover dados de uma origem para um destino: `mov destino, origem`. Os exemplos dados foram:

* `mov eax, 0x5f` que move o valor 0x5f para o registrador eax.
* `mov ebx, eax` copia o valor de eax para ebx.
* `mov eax, [0x5fc53e]` move o valor localizado no endereço de memória 0x5fc53e para eax. 
Colchetes indicam que estamos acessando o conteúdo da memória naquele endereço.

**LEA instruction**

Essa instrução "carrega o endereço efetivo" de uma expressão e não o conteúdo dela. O exemplo dado foi: `lea eax, [ebp+4]`, onde `eax` recebe o valor de `[ebp+4]` e não o valor armazenado na memória.

O LEA é frequentemente usado para operações aritméticas rápidas, pois permite somar e multiplicar registradores em uma única instrução sem acessar a memória.

**NOP instruction**

A instrução NOP (no operation) não faz nada, apenas consome um ciclo de CPU.

Ela é usada para sincronização, alinhamento e na aplicação de malware, para criar regiões preenchidas com `nop` que garantem que o shellcode possa ser executado mesmo que o salto não aponte exatamente para o início. 

**Shift instruction**

As instruções de deslocamento (shift) movem os bits de um registrador para a direita ou para a esquerda. Elas são úteis para operações aritméticas rápidas, especialmente multiplicação e divisão por potência de dois.

* `shr destino, count   ; shift right`
* `shl destino, count   ; shift left`

`Count` indica quantas posições os bits devem ser deslocados e quando um bit "sai" de um lado, zero é adicionado do outro. Além disso, durante esse processo o `Carry Flag (CF)` (conteúdo do próximo tópico que foi abordado) recebe o último bit que foi deslocado para fora.

**Rotate instruction**

Funciona de forma semelhante a anterior, mas com uma diferença importante: Os bits que saem de um lado retornam do outro, ao invés de serem substituídos por zeros.

* `ror destino, count   ; rotate right`
* `rol destino, count   ; rotate left`

`ror` move todos os bits para a direita e o `rol` move todos os bits para a esquerda.

## Flags

A CPU mantém um registrador especial chamado `EFLAGS`, que contém bits de status atualizados após certas operações. Cada flag representa uma condição específica ou, um resultado da mais recente operação lógica ou aritmética.

O curso nos apresenta essa tabela para entendermos o funcionamento das flags mais comuns:

| Flag | Abreviação | Explicação |
|----------|----------|----------|
| Carry  | CF | Indica carry ou borrow em operações aritméticas  |
| Parity | PF   | Marca se há um número par de bits 1 no resultado   |
| Auxiliary   | AF   | Usado em aritmética BCD   |
| Zero | ZF  | Indica que o resultado foi zero   |
| Sign  | SF   | Indica resultado negativo (bit mais significativo = 1)  |
| Overflow   | OF   | Detecta overflow em operações com sinal   |
| Direction  | DF   | Define direção de leitura de strings   |
| Interrupt Enable   | IF   | Habilita ou desabilita interrupções mascaráveis  |

Essas flags são usadas para o controle de fluxo, por exemplo, as instruções de salto dependem do estado das flags.

## Operações aritméticas

Operações aritméticas são performadas por uma CPU usando instruções aritméticas.

**Instruções de adição e subtração**

* `add destino, valor` soma o valor ao destino e guarda o resultado no próprio destino.
* `sub destino, valor` subtrai o valor do destino e guarda o resultado nele mesmo.
Duas flags importantes são: `ZF` (é 1 se o resultado for zero) e `CF` (é 1 se o destino é menor que o valor subtraído).

**Instruções de multiplicação e divisão**

Essas operações usam registros especiais porque o resultado pode ser maior que 32 bits.

* `mul valor` faz `eax * valor = resultado em edx:eax`. Então se o resultado for muito grande, o pedaço mais significativo vai para edx.
* `div valor` divide o número de 64 bits formado por `edx:eax` por `valor`. Com isso o quociente é `eax` e o resto `edx`.

**Solução**

Ao acessar `ls` descobrimos que existe um arquivos chamado `flag` nesse mesmo diretório, e então basta ler esse arquivo para obtermos a flag.

Para solucionar o problema, usamos o mesmo payload modificando apenas o `.popen('ls')` para `.popen('cat flag')` que irá ler o arquivo `flag` no sistema do site e retornar o conteúdo:

`{{request.application.__globals__.__builtins__.__import__('os').popen('cat flag').read()}}`

<img width="937" height="147" alt="image" src="https://github.com/user-attachments/assets/674f3272-6c92-407f-95f6-7e929c3cfceb" />

Feito isso, conseguimos obter a flag:

>`picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_09365533}`

**Aplicação no dia a dia**

Essa vulnerabilidade é facilmente confundida com ataques de XSS, então nem sempre existe uma proteção contra ela. 

Além disso, para que não exista esse tipo de leitura de arquivos ou exploração da vulnerabilidade, algumas práticas podem ser realizadas:

* Nunca renderizar templates a partir de strings controladas pelo usuário.
* Não expor objetos inteiros ao contexto do template.
* Usar sandboxing do engine quando possível.

Feito isso, o servidor ficará menos vulnerável à esse tipo de ataque.
