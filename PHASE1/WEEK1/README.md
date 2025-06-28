
# 🛠️ RISC-V Toolchain Setup Guide

This guide explains how to unpack the RISC-V toolchain, configure your environment, and verify that everything is working correctly.

---

## 📦 1. Unpack the Toolchain

Open a terminal and run:

```bash
tar -xvzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz
```

This will extract a directory, typically named `opt` or `riscv-toolchain`, containing the toolchain files.

---

## 📁 2. Locate the bin/ Directory

Navigate to the extracted directory:

```bash
cd path/to/extracted/folder/opt/riscv/bin
```

Replace `path/to/extracted/folder` with the actual path where the toolchain was extracted.

Then list the contents:

```bash
ls
```

You should see binaries like:

- `riscv32-unknown-elf-gcc`
- `riscv32-unknown-elf-objdump`
- `riscv32-unknown-elf-gdb`

---

## 🌐 3. Add Toolchain to Your PATH

### 🔹 Temporary (for current terminal session):

```bash
export PATH="$PWD:$PATH"
```

### 🔹 Permanent (recommended):

To make the change permanent:

**For Bash users:**

```bash
echo 'export PATH="/home/sakthi/Desktop/VSD/opt/riscv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**For Zsh users:**

```bash
echo 'export PATH="/home/sakthi/Desktop/VSD/opt/riscv/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

---

## ✅ 4. Verify Installation

Run the following commands to ensure the toolchain is working:

```bash
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-objdump --version
riscv32-unknown-elf-gdb --version
```

## 🖥️ Compile "Hello, RISC-V"

This section shows how to write and compile a simple RISC-V program using the `riscv32-unknown-elf-gcc` toolchain for the `rv32imc` target.



### 📄 Step 1: Create the C Source File

Create a file called `hello.c` with the following content:
````markdown



#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
````

---

### ⚙️ Step 2: Compile for RV32IMC

Use the following command to compile the code into a RISC-V ELF binary:

```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```

---

### 🔍 Step 3: Verify the ELF File

Check that the output ELF file is for 32-bit RISC-V:

```bash
file hello.elf
```

Expected output should include:

```
ELF 32-bit LSB executable, UCB RISC-V, RVC, soft-float ABI, ...
```

This confirms that the binary was successfully compiled for the RV32IMC architecture.

---

```

```

Here's a clean and structured **Markdown** version of your instructions with the `.s` file generation and explanation request:

---

##5.🛠️ Generate Assembly and Understand Function Structure

This section explains how to generate the assembly (`.s`) file for a simple C program targeting RISC-V (`rv32imc`), and what the **function prologue** and **epilogue** mean.

---

### 📄 Step 1: Source File – `hello.c`

```c
#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```

---

### ⚙️ Step 2: Generate Assembly

Use the following command to generate the assembly file (`hello.s`):

```bash
riscv32-unknown-elf-gcc -S -O0 hello.c
```

* `-S` tells GCC to output assembly instead of object code.
* `-O0` disables optimizations so you can see the raw function structure.

This creates a file named `hello.s`.

---

### 🧩 Step 3: Understand the Prologue and Epilogue

Open `hello.s` and look for lines like these inside the `main` function:

```assembly
addi    sp,sp,-16       # Allocate 16 bytes on the stack
sw      ra,12(sp)       # Save return address (ra) at offset 12
```

These lines are part of the **function prologue** — setup code that:

* Adjusts the stack pointer (`sp`)
* Saves important registers (like `ra`, the return address) to the stack

At the end of the function, you’ll see the **epilogue**:

```assembly
lw      ra,12(sp)       # Restore return address
addi    sp,sp,16        # Deallocate stack space
ret                     # Return to caller
```

These reverse the prologue steps, restoring the original state before returning.

---


---

##🧰 Convert and Disassemble RISC-V ELF

This guide shows how to convert your compiled ELF binary into a raw hex file and disassemble it for analysis.

---

### 🔄 Step 1: Disassemble the ELF File

```bash
riscv32-unknown-elf-objdump -d hello.elf > hello.dump
```

This generates a human-readable disassembly in `hello.dump`.

---

### 🔃 Step 2: Generate Intel HEX Format

```bash
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```

This converts the ELF into an Intel HEX format file (`hello.hex`), often used for flashing embedded devices.

---

### 🧐 Understanding the Disassembly Output

A line in the disassembly typically looks like this:

```
00000000 <main>:
   0:  1141        addi   sp,sp,-16
   2:  c606        sw     ra,12(sp)
   ...
```

Each column represents:

| Column           | Meaning                                                                     |
| ---------------- | --------------------------------------------------------------------------- |
| `0:`             | **Address offset** within the function (e.g., 0 bytes from start of `main`) |
| `1141`           | **Raw machine code** (hex representation of the instruction)                |
| `addi sp,sp,-16` | **Mnemonic + operands** — the actual instruction being executed             |

---

### 🧪 Example Instruction Breakdown

**Instruction:**

```
   0:  1141        addi   sp,sp,-16
```

* **Address**: `0:` → First instruction in `main`
* **Opcode**: `1141` → Binary encoding of the instruction
* **Mnemonic**: `addi` → Add Immediate
* **Operands**: `sp, sp, -16` → Subtracts 16 from the stack pointer (`sp`), creating stack space (prologue setup)

---


## 🧠 RV32 Integer Registers & Calling Convention

### 📋 Register Table

| Register | ABI Name | Description / Role                            |
| -------- | -------- | --------------------------------------------- |
| x0       | zero     | Constant 0                                    |
| x1       | ra       | Return address                                |
| x2       | sp       | Stack pointer                                 |
| x3       | gp       | Global pointer                                |
| x4       | tp       | Thread pointer                                |
| x5       | t0       | Temporary register (caller-saved)             |
| x6       | t1       | Temporary register (caller-saved)             |
| x7       | t2       | Temporary register (caller-saved)             |
| x8       | s0/fp    | Saved register / frame pointer (callee-saved) |
| x9       | s1       | Saved register (callee-saved)                 |
| x10      | a0       | Function argument / return value              |
| x11      | a1       | Function argument / return value              |
| x12      | a2       | Function argument                             |
| x13      | a3       | Function argument                             |
| x14      | a4       | Function argument                             |
| x15      | a5       | Function argument                             |
| x16      | a6       | Function argument                             |
| x17      | a7       | Function argument                             |
| x18      | s2       | Saved register (callee-saved)                 |
| x19      | s3       | Saved register (callee-saved)                 |
| x20      | s4       | Saved register (callee-saved)                 |
| x21      | s5       | Saved register (callee-saved)                 |
| x22      | s6       | Saved register (callee-saved)                 |
| x23      | s7       | Saved register (callee-saved)                 |
| x24      | s8       | Saved register (callee-saved)                 |
| x25      | s9       | Saved register (callee-saved)                 |
| x26      | s10      | Saved register (callee-saved)                 |
| x27      | s11      | Saved register (callee-saved)                 |
| x28      | t3       | Temporary register (caller-saved)             |
| x29      | t4       | Temporary register (caller-saved)             |
| x30      | t5       | Temporary register (caller-saved)             |
| x31      | t6       | Temporary register (caller-saved)             |

---

### 📚 Calling Convention Summary

* **`a0–a7`**: Used for function **arguments and return values**.
* **`s0–s11`**: **Callee-saved**: if a function uses these, it must restore them before returning.
* **`t0–t6`**: **Caller-saved**: not preserved across function calls.

  


### ✅ 6. Stepping with GDB

**Command to launch GDB:**

```
riscv64-unknown-elf-gdb hello.elf`
```

**Inside GDB:**

```gdb
(gdb) target sim                # Use the built-in RISC-V simulator
(gdb) load                      # Load the ELF into simulated memory
(gdb) break main                # Set breakpoint at main()
(gdb) run                       # Start execution
(gdb) info registers            # Inspect all general-purpose registers
(gdb) disassemble               # View disassembly at current PC
(gdb) stepi                     # Step one instruction
(gdb) continue                  # Resume execution until the end
(gdb) quit                      # Exit GDB
```

**Expected Output:**

```
Breakpoint 1, main () at hello.c:4
4       printf("Hello, RISC-V!\n");
```

* `load` is essential to load your program into memory.
* If `break main` fails, use the address shown by disassembly:

  ```gdb
  (gdb) break *0x10118
  ```

  

### ✅ Running Under an Emulator

**Minimal `hello.c` for bare-metal QEMU run:**

```c
int main() {
    while (1);  // Infinite loop — prevents program from exiting
    return 0;
}
```

---

**Compile without standard libraries:**

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -nostdlib -o hello.elf hello.c
```

> `-nostdlib` ensures the binary is truly bare-metal with no standard C runtime.

---

**Run using QEMU (no BIOS):**

```bash
qemu-system-riscv32 -nographic -machine sifive_e -kernel hello.elf -bios none
```

---

**Explanation of flags:**

* `-nographic` → sends all output to terminal (no GUI).
* `-machine sifive_e` → emulates SiFive E-class RISC-V board.
* `-kernel hello.elf` → loads your compiled ELF.
* `-bios none` → skips firmware (runs your ELF directly).

---

**Expected Behavior:**

* Program runs silently (infinite loop).
* No crash = ✅ success.
* To exit QEMU:
  Press `Ctrl + A`, then `X`.



### 7.✅ Exploring GCC Optimisation

**`hello.c` Source Code:**

```c
int add(int a, int b) {
    int result = a + b;
    return result;
}

int main() {
    int sum = add(5, 10);
    while (1);  // Infinite loop
    return 0;
}
```

---

**Compile to assembly with no optimization:**

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O0 -S -o hello_O0.s hello.c
```

**Compile to assembly with high optimization:**

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O2 -S -o hello_O2.s hello.c
```

---

**Compare output:**

```bash
code hello_O0.s hello_O2.s
```

or

```bash
diff hello_O0.s hello_O2.s
```

---

**Expected Differences:**

| Feature          | `-O0`                              | `-O2`                                  |
| ---------------- | ---------------------------------- | -------------------------------------- |
| Function `add()` | Separate function with call/return | Inlined into `main()`                  |
| Instructions     | More, direct 1-to-1 C translation  | Fewer, optimized                       |
| Stack usage      | Full stack frame setup             | May eliminate frame or reuse registers |
| Dead code        | Retained                           | Removed                                |

---

**Why It Matters:**

* `-O0` is ideal for debugging with GDB (preserves variables, structure).
* `-O2` is used in real firmware for speed and smaller code size.
Great catch, Naren — let me now **clearly explain** the three key optimizations mentioned in the task doc:

---

###8.🧠 GCC Optimisations Explained

#### ✅ 1. Dead-Code Elimination

> **What it is:** The compiler removes any code or variables that are never used or have no effect on program output.

**Example:**

```c
int unused = 42;     // Not used anywhere
return 0;
```

* With `-O0`: This line **stays** in the assembly output.
* With `-O2`: The `unused` variable is **completely removed** from the `.s` file.

🔍 **Why:** To reduce code size and improve performance.

---

#### ✅ 2. Register Allocation

> **What it is:** The compiler tries to **keep values in registers** (like `t0`, `a0`, etc.) instead of RAM/stack to speed up access.

**Example:**

```c
int result = a + b;
```

* With `-O0`: `a` and `b` may be loaded/stored using memory (e.g., stack).
* With `-O2`: `a` and `b` are often kept in registers throughout.

🔍 **Why:** Registers are faster than RAM — better performance.

---

#### ✅ 3. Function Inlining

> **What it is:** The compiler replaces a function call with the actual function code to avoid the cost of a `call` and `return`.

**Example:**

```c
int add(int a, int b) { return a + b; }

int main() {
    int sum = add(2, 3);
}
```

* With `-O0`: You'll see a `call add` in assembly.
* With `-O2`: The call to `add` disappears — it gets **inlined into `main()`**.

🔍 **Why:** Reduces function-call overhead and may allow further optimization.



### ✅ 9. Inline Assembly (Read `cycle` CSR)

**`hello.c`**

```c
#include <stdint.h>

int main() {
    uint32_t cycle;
    asm volatile ("csrr %0, cycle" : "=r"(cycle));
    while (1);
    return 0;
}
```

**Compile with debug info:**

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O0 -g -o hello.elf hello.c
```

**Launch GDB:**

```bash
riscv64-unknown-elf-gdb hello.elf
```

**Inside GDB:**

```gdb
(gdb) target sim
(gdb) load
(gdb) break main
(gdb) run
(gdb) x/w &cycle         # Inspect contents of the 'cycle' variable
(gdb) info registers     # Optional: see where it was stored
```

**Disassemble `main()` to view the generated instructions:**

```gdb
(gdb) disassemble main
```

or use `objdump` outside GDB:

```bash
riscv64-unknown-elf-objdump -d hello.elf > hello.dump
```



**Explanation:**

* `csrr %0, cycle` reads the current CPU cycle count into a register.
* The result is stored in the C variable `cycle` using inline assembly.
* `-g` is required for GDB to understand variable names like `cycle`.
* `x/w &cycle` shows the value of `cycle` in memory after the instruction executes.

  

### 10. MMIO GPIO Toggle Using Volatile Pointers

**Minimal Bare-Metal Snippet:**

```c
int main() {
    volatile uint32_t *gpio = (uint32_t *)0x10012000; *gpio = 0x1;
    while (1);
    return 0;
}
```



**Explanation:**

* `volatile` prevents the compiler from **optimizing away** the store to the GPIO memory address.
* The pointer cast `(uint32_t *)0x10012000` treats the MMIO base address as a pointer to a 32-bit register.
* Writing `*gpio = 0x1;` sets GPIO pin 0 high (toggle can be added later).



**Memory Alignment:**

* `0x10012000` is **4-byte aligned**, which is correct for a `uint32_t` (32-bit value).
* Unaligned memory access can cause **hardware exceptions** on RISC-V.
* MMIO registers must always be accessed with correctly sized and aligned operations.


## ✅ 11. GDB: Stepping Through the ELF File

Use GDB to run the program step-by-step and analyze each instruction executed on the RISC-V simulation.

### 🧰 Launch GDB

```bash
riscv64-unknown-elf-gdb hello.elf
```

### 🔍 Inside GDB

```gdb
(gdb) target sim                # Use RISC-V simulator
(gdb) load                      # Load the ELF file into memory
(gdb) break main                # Set breakpoint at main
(gdb) run                       # Start execution
(gdb) stepi                     # Step one instruction
(gdb) info registers            # Inspect all registers
(gdb) disassemble               # View assembly instructions
(gdb) continue                  # Continue program
(gdb) quit                      # Exit GDB
```

Expected output:

```
Breakpoint 1, main () at hello.c:4
4       printf("Hello, RISC-V!\n");
```

---

## ✅ 12. Run RISC-V ELF using QEMU

You can emulate your RISC-V bare-metal program using QEMU.

### 📝 hello.c (Bare Metal)

```c
int main() {
    while (1);  // Infinite loop
    return 0;
}
```

### ⚙️ Compile without standard library

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -nostdlib -o hello.elf hello.c
```

### ▶️ Run using QEMU

```bash
qemu-system-riscv32 -nographic -machine sifive_e -kernel hello.elf -bios none
```

🔸 `-nographic`: Use terminal output  
🔸 `-machine sifive_e`: Use SiFive E-class core  
🔸 `-bios none`: Skip built-in firmware  

➡️ To exit: `Ctrl + A`, then press `x`

---

## ✅ 13. Optimisation Impact: -O0 vs -O2

### 🔧 Code (hello.c):

```c
int add(int a, int b) {
    int result = a + b;
    return result;
}

int main() {
    int sum = add(5, 10);
    while (1);
    return 0;
}
```

### Compile with no optimization:

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O0 -S -o hello_O0.s hello.c
```

### Compile with -O2 (optimized):

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O2 -S -o hello_O2.s hello.c
```

### Compare outputs:

```bash
diff hello_O0.s hello_O2.s
```

---

## ✅ 14. GCC Optimisation Effects

| Feature          | `-O0`                              | `-O2`                                  |
| ---------------- | ---------------------------------- | -------------------------------------- |
| Function `add()` | Call with prologue/epilogue       | Inlined into `main()`                  |
| Instructions     | More, direct mapping               | Fewer, optimized                       |
| Dead code        | Retained                          | Removed                                |
| Stack use        | Always created                     | Often eliminated                       |

---

## ✅ 15. RV32 Register Summary

| Register | ABI Name | Description / Role                            |
| -------- | -------- | --------------------------------------------- |
| x0       | zero     | Constant 0                                    |
| x1       | ra       | Return address                                |
| x2       | sp       | Stack pointer                                 |
| x10–x17  | a0–a7    | Function arguments / return values            |
| x5–x7    | t0–t2    | Temporaries (caller-saved)                    |
| x8–x9    | s0/fp, s1| Callee-saved                                  |
| x18–x27  | s2–s11   | Callee-saved registers                        |

---

## ✅ 16. Inline Assembly Example: Reading `cycle` CSR

### 🔧 Code:

```c
#include <stdint.h>

int main() {
    uint32_t cycle;
    asm volatile ("csrr %0, cycle" : "=r"(cycle));
    while (1);
    return 0;
}
```

### 🛠️ Compile with debug info:

```bash
riscv64-unknown-elf-gcc -march=rv32imac -mabi=ilp32 -O0 -g -o hello.elf hello.c
```

### 📟 Run in GDB:

```bash
riscv64-unknown-elf-gdb hello.elf
(gdb) target sim
(gdb) load
(gdb) break main
(gdb) run
(gdb) x/w &cycle         # Check value of cycle
(gdb) info registers     # See register mapping
```

---

## ✅ 17. MMIO GPIO Toggle Using Volatile Pointers

### 🔧 Code:

```c
int main() {
    volatile uint32_t *gpio = (uint32_t *)0x10012000;
    *gpio = 0x1;
    while (1);
    return 0;
}
```

### 📘 Explanation:

- `volatile`: prevents compiler from optimizing out the store.
- `0x10012000`: is MMIO base for GPIO.
- Ensures write is performed on real hardware.

🔍 Alignment is important: `0x10012000` is 4-byte aligned for `uint32_t`.

---
