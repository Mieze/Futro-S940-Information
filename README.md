# Futro S940 Information
Hacking the Futro S940

## 1. Get the Datasheets of the SoC
Detailed datasheets of the Intel Pentium Silver and Intel Celeron SoC's are available for download at Intel's website. Volume 1 describes the general architecture and the hardware interfaces:

[Intel Pentium Silver and Intel Celeron Processors - Datasheet Volume 1 of 2](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://cdrdv2-public.intel.com/336560/336560-glk-datasheet-volume1_rev005.pdf&ved=2ahUKEwiKvofb8oGVAxUQA9sEHQs9DP8QFnoECBoQAQ&usg=AOvVaw2OrarG-PAaiSxW3P3Zl4a8)

Volume 2 contains the detailed documentation of the SoCs internal registers and it's integrated PCIe devices:

[Intel Pentium Silver and Intel Celeron Processors - Datasheet Volume 2 of 2](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://cdrdv2-public.intel.com/336561/336561-glk-datasheet-volume2.pdf&ved=2ahUKEwj8lJzl4PqUAxWPX_EDHWhaAYwQFnoECBwQAQ&usg=AOvVaw2_Evcj3hlVqmmZjpHsN-nM)


## 2. Extract the UEFI Setup Variables
[Describes the procedure of extracting and decoding the UEFI setups variable section](https://github.com/Mieze/Futro-S940-Information/tree/main/Extract%20UEFI%20Setup%20Variables).
Todo

## 3. Decoded UEFI Setup Variables
[Here you'll find the extracted and decoded setup variable section of the Futro S940's UEFI](https://github.com/Mieze/Futro-S940-Information/tree/main/UEFI%20Setup%20Decode).

## 4. Enable ASPM
[This section contains instructions how to change UEFI setup variables to enable ASPM for individual PCIe ports](https://github.com/Mieze/Futro-S940-Information/tree/main/Enable%20ASPM).

## 5. Modify the UEFI
The Futro S940's UEFI can be modified to change the hardware configuration or to enable addition features. This [section](https://github.com/Mieze/Futro-S940-Information/tree/main/Modify%20UEFI) describes the procedure of extracting, modifying and flashing the modified UEFI back to your device.



