# Crack
###### Solved by @Juh-s
>This is a challenge about Reverse Engineering
## Sobre o desafio

Esse desafio consiste em encontrar a senha correta de um arquivo executável.

Rodando o programa no terminal, podemos observar seu funcionamento:

<img width="424" height="261" alt="Screenshot 2025-10-28 172446" src="https://github.com/user-attachments/assets/c538125e-9c0f-4fa2-9644-505f81c9d507" />

## Solução
**Introdução**

Uma ferramenta extramamente importante para a resolução desse desafio é o Ghidra, isso porque com ele é possível fazer uma análise estática do código que o programa usa para funcionar.

Analisando esse executável pelo Ghidra temos na sua função principal o seguinte:

```
undefined8 main(void)

{
  byte bVar1;
  ssize_t sVar2;
  long lVar3;
  int iVar4;
  long in_FS_OFFSET;
  double dVar5;
  undefined1 local_be;
  byte local_bd;
  uint local_bc;
  long local_b8;
  undefined8 local_b0;
  undefined8 local_a8 [4];
  undefined8 local_88;
  undefined8 uStack_80;
  undefined8 local_78;
  char local_68 [72];
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  local_b8 = ppeuler_3();
  dVar5 = cbrt((double)local_b8);
  local_b8 = (long)dVar5;
  local_b8 = factorial(local_b8);
  lVar3 = 0;
  do {
    bVar1 = *(byte *)((long)&local_b8 + lVar3);
    if ((0x19 < (byte)((bVar1 & 0xdf) + 0xbf)) && (9 < (byte)(bVar1 - 0x30))) {
      bVar1 = bVar1 % 0x3e;
      if ((byte)(bVar1 - 10) < 0x1a) {
        *(byte *)((long)&local_b8 + lVar3) = bVar1 + 0x37;
      }
      else if ((byte)(bVar1 + 0x30) < 0x54) {
        *(byte *)((long)&local_b8 + lVar3) = bVar1 + 0x30;
      }
      else {
        *(byte *)((long)&local_b8 + lVar3) = bVar1 + 0x3d;
      }
    }
    lVar3 = lVar3 + 1;
  } while (lVar3 != 8);
  local_88 = 1;
  uStack_80 = 1;
  local_78 = 1;
  local_a8[0] = 0x6874207265746e65;
  local_a8[1] = 0x2074686769722065;
  local_a8[2] = 0x64726f7773736170;
  iVar4 = 0;
  do {
    local_b0 = local_a8[iVar4];
    write(1,&local_b0,8);
    iVar4 = iVar4 + 1;
  } while (iVar4 != 3);
  local_be = 10;
  write(1,&local_be,1);
  sVar2 = read(0,local_68,0x40);
  lVar3 = sVar2 + -1;
  local_bd = 1;
  local_bc = 0;
  if (0 < lVar3) {
    do {
      if (7 < local_bc) break;
      if (local_68[(int)local_bc] != *(char *)((long)&local_b8 + (long)(int)local_bc)) {
        local_bd = 0;
      }
      local_bc = local_bc + 1;
    } while ((int)local_bc < lVar3);
  }
  local_bd = lVar3 == 8 & local_bd;
  if (local_bd == 0) {
    puts("bad kitty!");
  }
  else {
    puts("good kitty!");
  }
  if (local_20 == *(long *)(in_FS_OFFSET + 0x28)) {
    return 0;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail();
}
```

Ao observar o que o código está fazendo, nessa exata parte:
```
  local_b8 = ppeuler_3();
  dVar5 = cbrt((double)local_b8);
  local_b8 = (long)dVar5;
  local_b8 = factorial(local_b8);
```
Ele armazena a senha em `local_b8` após realizar algumas operações, contando com uma outra função no processo (`ppeuler_3()`). Ou seja, a senha correta é gerada através de `factorial(floor(cbrt(ppeuler_3())))`.

**Análise Inicial**

O próximo passo é abrir o GDB no binário para confirmar os valores exatos em tempo de execução, ou seja, ler diretamente os bytes que o programa gerou na pilha, evitando re-implementar o algoritmo manualmente e possíveis erros de interpretação.

No GDB, serão utilizados os seguintes comandos:

* `set pagination off` para desativar a paginação automática, ou seja, não pede confirmação a cada página.
* `disassemble main` serve para mostrar o código em Assembly da função main. É útil para entendermos como o compilador traduziu nosso código em instruções de baixo nível que o processador executa.
* `break *main+194` esse comando para a execução do programa nesse exato ponto, ele foi criado nessa linha (main+194) porque é o ponto exato em que a senha está completa e os 8 bytes foram escritos na memória.
* `run` roda o programa desde o início parando no breakpoint criado anteriormente.
* `x/8cb $rsp+0x10`: `x` vai examinar o conteúdo da memória, `/8cb` mostra 8 unidades no formato de char e byte e `$rsp+0x10` é o endereço base que possui o ponteiro da stack (rsp) mais um deslocamento de 16 bytes.

Algumas considerações importantes sobre o uso de cada comando:

O breakpoint só foi criado no endereço `main+194` porque na disassembly da função main, há um loop que escreve os 8 caracteres da senha gerada para os endereços, começando por `0x10(%rsp)`. Esse loop incrementa `%rdx` de 0 até 7 e a instrução que marca o fim desse processo é: `0x0000000000001327 <+123>: je 0x136e <main+194>`.

Além disso, sabemos que o endereço base é o `$rsp+0x10` porque é possível ver, por exemplo, na linha ` 0x000000000000131b <+111>:   mov    %al,0x10(%rsp,%rdx,1)` onde os bytes de 0 a 7 estão sendo escritos a partir do endereço `rsp+0x10`.

**Solução**

Ao seguir os comandos anteriores, temos:

<img width="663" height="205" alt="image" src="https://github.com/user-attachments/assets/84907cdc-6262-45df-8b48-c40647a8b34d" />

Feito isso, basta reescrever a palavra usando os caracteres que encontramos: `48 '0'  48 '0'  115 's' 71 'G'  111 'o' 52 '4'  77 'M'  48 '0'` e assim conseguimos obter a senha:

>`00sGo4M0`

**Conclusão**

Este desafio proporcionou uma aplicação prática e estratégica do uso do GDB para engenharia reversa de binários. Através da análise estática e dinâmica, foi possível compreender como o programa gera internamente uma senha secreta e como essa senha é comparada com a entrada do usuário. Ao identificar o ponto exato onde os bytes da senha são escritos na stack e inspecionar a memória com comandos específicos, conseguimos extrair a resposta correta sem precisar reimplementar toda a lógica do programa.
