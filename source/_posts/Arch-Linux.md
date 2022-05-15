---
title: Arch Linux
top: false
cover: false
toc: true
date: 2022-05-15 20:40:03
password:
summary:
description: Arch Linux installation, Configuration
categories:
  - Programming
  - Linux
tags:
  - Arch Linux
---

# What is UEFI

## BIOS

1. BIOS (basic input/output system) is a program.
2. It is in the ROM (read only) disk. It is hard encoded on the computer
   motherboard.
3. It is used by a computer's microprocessor to start the computer system after
   it is powered on. It performs hardware initialization during the booting
   process (power-on startup).
4. It also manages data flow between the computer's operating system (OS) and
   attached devices, such as the hard disk, video adapter, keyboard, mouse and
   printer.

## UEFI

BIOS's popularity has waned in favor of a newer technology: UEFI (Unified
Extensible Firmware Interface). Intel announced a plan in 2017 to retire support
for legacy BIOS systems by 2020, replacing them with UEFI.

### The Usage of UEFI

UEFI launches EFI (Extensible Firmware Interface) applications, e.g., boot
loaders, boot managers, UEFI shell, etc. These applications are usually stored
as files in the EFI system partition. Each vendor can stores its files in the
EFI system partition under the `/EFI/vendor_name` directory. The applications
can be launched by adding a boot entry to the NVRAM (non-volatile random access
memory, an RAM can keep the data with power off) or from the UEFI shell.
