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
Ele armazena a senha em `local_b8` após realizar algumas operações, contando com uma outra função no processo (ppeuler_3()).

**Análise Inicial**

Já que o valor é armazenado nessa variável `local_b8`, podemos acessar o endereço de memória em que esse `local_b8` está para encontrar o conteúdo presente nele.

**Solução**

Feito isso, conseguimos obter a senha:

>`00sGo4M0`

**Aplicação**


