# XOR Starter
###### Solved by @Juh-s
>This is a CTF about Cryptography, Base64, Caesar
## Sobre o desafio
XOR is a bitwise operator which returns 0 if the bits are the same, and 1 otherwise. In textbooks the XOR operator is denoted by ⊕, but in most challenges and programming languages you will see the caret `^` used instead.

![image](https://github.com/user-attachments/assets/f67c9e28-b472-4ca1-9dd2-75902b7f44fe)

For longer binary numbers we XOR bit by bit: `0110 ^ 1010 = 1100`. We can XOR integers by first converting the integer from decimal to binary. We can XOR strings by first converting each character to the integer representing the Unicode character.

Given the string `label`, XOR each character with the integer `13`. Convert these integers back to a string and submit the flag as `crypto{new_string}`.

Tradução:
