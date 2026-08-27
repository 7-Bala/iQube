
## Boot Process

Power ON
   ↓
Firmware (BIOS/UEFI)
   ↓
Bootloader : Find the operating system and load its kernel
   ↓
Kernel
   ↓
init/systemd
   ↓
Login / Desktop

## Boot Process

booting -> process of starting the computer and loading the OS

Power ON
↓
BIOS / UEFI
↓
Bootloader
↓
Kernel
↓
init / systemd
↓
Services
↓
Login
↓
Desktop / Shell

BIOS/UEFI -> initializes hardware and finds boot device

bootloader -> loads the OS kernel
example -> GRUB

kernel -> takes control and initializes CPU, memory, devices, filesystem etc.

systemd -> first userspace process on many Linux systems
PID -> 1

then services start and system becomes ready to use

