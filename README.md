🛠️ RISC‑V Toolchain Setup Guide (RV32IMAC)
This guide walks you through unpacking the RISC‑V toolchain, setting up your environment, and confirming your installation.

📦 1. Extract the Toolchain
Open your terminal and run:

bash
Copy code
tar -xvzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz
This command will unpack a folder—often named opt or riscv-toolchain—that contains all the toolchain components.

📁 2. Locate the bin/ Folder
Change to the extracted directory:

bash
Copy code
cd /path/to/extracted/opt/riscv/bin
Adjust the path as needed. Then list the contents:

bash
Copy code
ls
You should find executables such as:

riscv32-unknown-elf-gcc

riscv32-unknown-elf-objdump

riscv32-unknown-elf-gdb

🌐 3. Add the Toolchain to Your PATH
To use the tools immediately (temporary):

bash
Copy code
export PATH="$PWD:$PATH"
To make it permanent:

For Bash users:

bash
Copy code
echo 'export PATH="/home/sakthi/Desktop/VSD/opt/riscv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
For Zsh users:

bash
Copy code
echo 'export PATH="/home/sakthi/Desktop/VSD/opt/riscv/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
✅ 4. Confirm the Installation
Run these commands to verify everything is set up correctly:

bash
Copy code
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-objdump --version
riscv32-unknown-elf-gdb --version
Each should display version information, confirming the toolchain is installed and functional.
