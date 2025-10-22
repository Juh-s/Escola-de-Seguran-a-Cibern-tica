# x86 Assembly Crash Course
###### Written by @Juh-s
>This is a crash course in x86 Assembly for training in malware reverse engineering.
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

O [desmontador](https://pt.wikipedia.org/wiki/Desmontador) funciona com esses opcodes para desmontar um programa, traduzindo-os em instruções de montagem para torná-los legíveis por humanos.

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

**Instrução MOV**

Usada para mover dados de uma origem para um destino: `mov destino, origem`. Os exemplos dados foram:

* `mov eax, 0x5f` que move o valor 0x5f para o registrador eax.
* `mov ebx, eax` copia o valor de eax para ebx.
* `mov eax, [0x5fc53e]` move o valor localizado no endereço de memória 0x5fc53e para eax. 
Colchetes indicam que estamos acessando o conteúdo da memória naquele endereço.

**Instrução LEA**

Essa instrução "carrega o endereço efetivo" de uma expressão e não o conteúdo dela. O exemplo dado foi: `lea eax, [ebp+4]`, onde `eax` recebe o valor de `[ebp+4]` e não o valor armazenado na memória.

O LEA é frequentemente usado para operações aritméticas rápidas, pois permite somar e multiplicar registradores em uma única instrução sem acessar a memória.

**Instrução NOP**

A instrução NOP (no operation) não faz nada, apenas consome um ciclo de CPU.

Ela é usada para sincronização, alinhamento e na aplicação de malware, para criar regiões preenchidas com `nop` que garantem que o shellcode possa ser executado mesmo que o salto não aponte exatamente para o início. 

**Instrução Shift**

As instruções de deslocamento (shift) movem os bits de um registrador para a direita ou para a esquerda. Elas são úteis para operações aritméticas rápidas, especialmente multiplicação e divisão por potência de dois.

* `shr destino, count   ; shift right`
* `shl destino, count   ; shift left`

`Count` indica quantas posições os bits devem ser deslocados e quando um bit "sai" de um lado, zero é adicionado do outro. Além disso, durante esse processo o `Carry Flag (CF)` (conteúdo do próximo tópico que foi abordado) recebe o último bit que foi deslocado para fora.

**Instrução Rotate**

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

**Instruções de incremento e decremento**

Essas operações incrementam ou decrementam uma unidade do `eax`.
* `inc eax` soma 1.
* `dec eax` subtrai 1.

## Instruções lógicas

Essas instruções operam bit a bit, como portas lógicas digitais.

**Instrução AND**

Executa uma operação `AND` bit a bit nos operandos e só retorna uma saída 1 se ambas as entradas forem 1, caso contrário, ela retornará 0.

**Instrução OR**

A instrução `OR` executa uma operação `OR` bit a bit e retornará 1 se pelo menos um dos operandos for 1 e se nenhum operando for 1, retornará 0.

**Instrução NOT**

Essa instrução inverte os bits, de 0 para 1 e de 1 para 0.

**Instrução XOR**

A operação `XOR` retorna 1 se as entradas forem opostas, caso sejam iguais, retornará 0.


## Condicionais e ramificações

**Condicionais**

Uma CPU deve determinar se dois valores são iguais, maiores ou menores que o outro. Para executar essas operações, a CPU usa algumas instruções condicionais.

**Instrução TEST**

Faz uma operação `AND` bit a bit, mas não salva o resultado, só atualiza as flags.

* `test destino, fonte` faz `destino AND fonte`, se o resultado for 0, define `ZF = 1`, caso contrário `ZF = 0`.

**Instrução CMP**

A `cmp` compara dois valores, sem alterar nenhum deles. Internamente ela funciona de forma semelhante a uma instrução de subtração. 

* `cmp destino, fonte` define `ZF = 1` se os dois valores forem iguais, `CF = 1` se houve empréstimo (destino < fonte), `ZF = 0` e `CF = 0` se destino > fonte.

**Ramificação**

Quando não há ramificação, o ponteiro de instrução vai de uma para outra na ordem em que são colocadas na memória. Esse fluxo permanece em linha reta, a menos que haja uma operação de remificação.

**Instrução JMP**

Essa instrução faz com que o fluxo de controle salte para um local especificado e tem a seguinte sintaxe: `jmp destino`, onde o operando de localização será movido para o ponteiro de instrução, tornando-o o endereço onde a próxima instrução será buscada para execução.

**Saltos condicionais**

Como não existe o operador `if` na linguagem Assembly, é possível utilizar os saltos condicionais, que operam baseadas nas flags.

| Instrução | Explicação | 
|----------|----------|
| jz  | Salta se `ZF = 1` | 
| jnz | Salta se `ZF = 0`   | 
| je   | Igual a `jz`, usado após `cmp`   | 
| jne | Igual a `jnz`  |  
| jg  | Salta se o destino > fonte (comparação com sinal) |
| jl   | Salta se destino < fonte (com sinal)  | 
| jge  | Salta se destino > ou = fonte (com sinal)  | 
| jle  | Salta se destino < ou = fonte (com sinal)   | 
| ja  | Igual a `jg`, mas sem sinal   | 
| jb   | Igual a `jl`, mas sem sinal | 
| jae  | Igual a `jge`, mas sem sinal| 
| jbe  | Igual a `jle`, mas sem sinal  | 

## Pilha

A pilha é uma memória [LIFO](https://phoenixnap.pt/gloss%C3%A1rio/lifo-%C3%BAltimo-a-entrar-primeiro-a-sair), isso significa que a última variável enviada para a pilha é a primeira a sair. Essas operações push e pop são executadas seguindo as instruções na linguagem Assembly. 

**Instrução PUSH**

Essa instrução `push` coloca (empilha) um valor no topo da stack (pilha).

* `push fonte` o valor do operando é salvo na memória apontada por `ESP` e depois, o `ESP` é decrementado, pois a pilha cresce para baixo na memória.

**Aplicação no dia a dia**

**Aplicação no dia a dia**

Essa vulnerabilidade é facilmente confundida com ataques de XSS, então nem sempre existe uma proteção contra ela. 

Além disso, para que não exista esse tipo de leitura de arquivos ou exploração da vulnerabilidade, algumas práticas podem ser realizadas:

* Nunca renderizar templates a partir de strings controladas pelo usuário.
* Não expor objetos inteiros ao contexto do template.
* Usar sandboxing do engine quando possível.

Feito isso, o servidor ficará menos vulnerável à esse tipo de ataque.
