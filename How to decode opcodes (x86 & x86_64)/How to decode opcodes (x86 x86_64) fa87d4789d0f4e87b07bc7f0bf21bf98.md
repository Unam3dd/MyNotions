# How to decode opcodes (x86 / x86_64) ?

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled.png)

---

This notion is intended to show you how to decode opcodes, this is a condensed version of the intel documentation that I will give as a reference link below.

[https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-instruction-set-reference-manual-325383.pdf](https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-instruction-set-reference-manual-325383.pdf)

## 1. Instruction format for intel 64 and IA-32 Architectures

---

Here are the instruction encodings for the Intel 64 and IA-32 architectures. Instructions consist :

- Optional instruction prefixes (in any order)
- Primary opcode bytes (up to three bytes)
- Addressing pattern specifier (if required) consisting of the ModR/M byte.
- Sometimes the Scale-Index-Base (SIB) byte.
- Displacement (if necessary).
- Immediate data field (if necessary).

Here is a table representing the architecture:

---

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%201.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%201.png)

Well before I start I will also explain you what is an opcode, a displacement and an immediate, of course we will see the other fields after a little later do not worry.

## What is a Opcode ?

---

First of all what is an "opcode", an opcode is an instruction in machine language that specifies the operation to be performed. (it is noted in hexadecimal we will see later) the opcodes are basically the assembly instructions that your processor will execute.

## Example of opcodes :

---

```wasm
000011cd <add>:
    11cd:       f3 0f 1e fb             endbr32
    11d1:       55                      push   ebp
    11d2:       89 e5                   mov    ebp,esp
    11d4:       e8 5b 00 00 00          call   1234 <__x86.get_pc_thunk.ax>
    11d9:       05 ff 2d 00 00          add    eax,0x2dff
    11de:       8b 55 08                mov    edx,DWORD PTR [ebp+0x8]
    11e1:       8b 45 0c                mov    eax,DWORD PTR [ebp+0xc]
    11e4:       01 d0                   add    eax,edx
    11e6:       5d                      pop    ebp
    11e7:       c3                      ret
```

The opcodes are the bytes noted in hexadecimal on the left (f3 0f 1e fb, etc...), finally one uses the assembly language because to write a program in machine language would be quite tedious.

### What is "displacement" ?

---

Now that you know what is an opcode I will tell you what is a "displacement" and an "immediate".

Finally it is very simple a "displacement" will take the value pointed at the address + bytes allowing a memory displacement for example the assembly instruction below is a "displacement.

---

```wasm
mov eax, DWORD [ebp + 0x8]
```

Here we notice that the instruction will take the value pointed to EBP + 0x8 and put it in the accumulator register eax.

### What is "immediate" ?

---

Now we'll see what an "immediate" is, an "immediate" is simply a direct value, it is not a value that is in a register.

---

```wasm
mov eax, 0x10
```

Here we will put 16 (0x10) in eax, so 0x10 is an immediate value.

## let's go to decode our first program !

---

Great now that you know that, we can start the serious stuff I'm going to show you how to decode your first instruction and then we're going to talk about different mod topics such as the "R/M" mod seen above and the SIB and also the prefixes. 

To decode opcodes we will need more than one table we will have to have an opcode table that will allow us to get the right mnemonic instructions, then for some opcodes we will need another table this table is called the addressing mode table.

I provide you the table of opcode I use this one but there are plenty on the internet.

---

- Opcode table (x86) : [http://ref.x86asm.net/coder32.html](http://ref.x86asm.net/coder32.html)
- Opcode table (x86_64) : [http://ref.x86asm.net/coder64.html](http://ref.x86asm.net/coder64.html)

Since the program we are going to decode is in 32 bits I will use the 32 bits table.

Here is the program, from the opcodes we will have to find the source code in assembler.

---

```wasm
55
89 e5
8b 55 08
8b 45 0c
01 d0
5d
c3
```

Let's start, here we see that the first opcode is 0x55 so we will take our table "opcode table" then we will look for the opcode 55 in the table to retrieve.

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%202.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%202.png)

here we can see that the opcode 55 is not present but we have a base which is "50+r" finally of account 50 is push and the second figure is the register because as we can notice it takes in argument a register 16 bits or 32 bits, the registers are noted from 0 to 7 if we refer to the addressing table we can see that "5" is the register ebp which means that the opcode 55 is "PUSH EBP" if the opcode would have been 51 that would have been "PUSH ECX" because ECX is the register 1.

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%203.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%203.png)

Small precision to differentiate a register 16 bits or 32 bits the byte preceding the opcode is "0x66" this opcode says that the instruction is in mode 16 bits this opcode is also called operand size prefix.

In our case if instead of 0x55 we had "0x66 0x55" we would have had "PUSH BP" and not "EBP.

---

Now we know that 55 is PUSH EBP.

```wasm
55                   PUSH EBP
89 e5
8b 55 08
8b 45 0c
01 d0
5d
c3
```

let's go to the next opcodes, here we see that we have two opcodes 89 e5, and it's here that the MOD will come into play we will see how to decode an instruction of this type.

as I said above some opcodes have what we call a mode, this mode is called R/M for "register/memory", to decode it we will take the first opcode look in the table if this opcode has the R/M mode.

---

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%204.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%204.png)

the small "r" means that the opcode supports an R/M mode so we will have to use the following opcode to decode the instruction, we already know that the mnemonic assembler instruction is a "MOV".

To decode an opcode supporting the R/M mod we will take the opcode which follows the first opcode and we will decompose it with this schema below

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%205.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%205.png)

we will take the binary value of e5 and then we will separate the bits into small packets to decode our instruction correctly using the addressing table.

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%206.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%206.png)

The image above is the byte of the R/M mode we will cut it like this.

---

```wasm

89 = 1000 1001
E5 = 1110 0101
                               if D_BIT = 0 -> REG is SRC
															 if D_BIT = 1 -> REG is DST
2 Bits 3 Bits  3 Bits
[MOD]  [REG]    [R/M]            D_BIT(89) = 0 (in our case REG is SRC)
 11     100      101
```

Before decoding the second opcode with the help of the addressing table we have to calculate the "D bit" the d bit is simply the last but one bit of the first opcode, so the one that allowed us to determine the mnemonics instructions.

now that we know the order of our registers we will be able to find the registers and assemble our instruction we will help us with the x86 32 bits addressing table for the R/M mod

---

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%207.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%207.png)

Here is our addressing table for the R/M mod if you want to have it in the intel manual it is on page 532, otherwise on the site where there is the opcode table it is present BE CAREFUL not to mix the tables

According to the table we notice that we have the mod "11" or 3 in decimal which means that the 3 bits R/M is a register.

According to the table, our opcode having the mod R/M would be this.

```wasm
89 = 1000 1001                
E5 = 1110 0101
															 MOD = 00 -> NO DISPLACEMENT -> [EAX]
															 MOD = 01 -> [REG]+disp8 ->     [EAX + 0x8]
															 MOD = 10 -> [REG]+disp32 ->    [EAX + 0xff8956]
															 MOD = 11 -> REG          ->     EAX

                               if D_BIT = 0 -> REG is SRC
															 if D_BIT = 1 -> REG is DST
2 Bits   3 Bits  3 Bits
[MOD]    [REG]    [R/M]            D_BIT(89) = 0 (in our case REG is SRC)
 11       100      101
IS REG    ESP      EBP          (don't care operand size prefix)

the final instruction is : MOV EBP, ESP (because) REG is source
```

well we have just decoded our second opcode having the mod R/M.

---

```wasm
55                   PUSH EBP
89 e5                MOV EBP, ESP
8b 55 08             
8b 45 0c
01 d0
5d
c3
```

we are going to pass to the next opcodes, I do not redo all the procedure I will directly pass to the stage of the decoding.

---

```wasm
8b = 10001011         -> D_BIT = 1 : REG -> DST

     MOD   REG   R/M
55 = 01    010   101
   DISP+8  EDX   EBP

the final instruction is : MOV EDX, [EBP + 0x8] <- 0x8 is displacement byte
```

it is the same for the following opcodes "8b 45 0c".

---

```jsx
55                   PUSH EBP
89 e5                MOV EBP, ESP
8b 55 08             MOV EDX, DWORD [EBP + 0x8]
8b 45 0c             MOV EAX, DWORD [EBP + 0xC] 
01 d0                
5d
c3
```

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%208.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%208.png)

For the following opcode, we can see that it has the "MOD R/M" which means that we can decode it, we will decode it the same way as the previous opcodes.

opcode 0x01 has the mnemonic instruction "ADD", it has two operands "r/m16/32" and "r16/32

---

```jsx
01 = 0000 0001   get the d_bit -> 0 REG is src
d0 = 1101 0000

MOD       REG    R/M
11        010    000
IS_REG    EDX    EAX  final instruction is -> ADD EAX, EDX
```

---

```jsx
55                   PUSH EBP
89 e5                MOV EBP, ESP
8b 55 08             MOV EDX, DWORD [EBP + 0x8]
8b 45 0c             MOV EAX, DWORD [EBP + 0xC]
01 d0                ADD EAX, EDX
5d                   POP EBP
c3                   RET
```

---

There is another example that I want to note, there are several mnemonics instructions for the same opcode

```jsx
c0 e0 0
```

![How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%209.png](How%20to%20decode%20opcodes%20(x86%20x86_64)%20fa87d4789d0f4e87b07bc7f0bf21bf98/Untitled%209.png)

---

Here we see that there are several instructions for the same opcode C0 to determine which mnemonic instruction to take we can apply the following formula "((0xe0 << 2) & 0xFF) >> 0x5"

the formula returns 4 which means that we are going to look at the column 4 we see that the mnemonics instruction is SHL

```jsx
SHL r/m8 imm8 -> SHL AL, 0x7
e0 = 000 -> EAX -> AL
```

here we have succeeded in decoding the instruction