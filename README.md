# Futro S940 Information
Hacking the Futro S940

## Extract the UEFI Setup Variables
Todo

The information provided below applies to version V5.0.0.13 R1.14.0 for the D3543-A1x board, which is used in the Fujitsu Futro S940 thin clients. Other versions may have different setup variables. Before you change any variable, make sure that you are the same version, because you might render your board unbootable if you change the wrong variable.

There is a tool to read/write UEFI setup variables called setup_var.efi, which is a rewritten version of the modified grub shell used to change UEFi variables in the past. Please see https://github.com/datasone/setup_var.efi to download the toll and get instructions how to use it.

## Enable ASPM
There is a global switch to enable ASPM support, which has to be changed first before you may setup ASPM for individual PCIe ports.

|Name       |Variable|Value|Description          |
|-----------|--------|-----|---------------------|
|Native ASPM|   0x4E0|    0|ASPM support disabled|
|           |        |    1|ASPM support enabled |

Each PCIe root port has its own variable to configure ASPM support:

|PCI Express Root Port|Variable|lspci device|Description          |
|---------------------|--------|------------|---------------------|
|#1|0x303|00:14.0|unknown device|
|#2|0x304|00:14.1|unknown device|
|#3|0x305|00:13.0|PCIe x1 Slot|
|#4|0x306|00:13.1|M.2 Key E|
|#5|0x307|00:13.2|RTL8111 onboard LAN|
|#6|0x308|00:13.3|unknown device|

These variables can take one of the following values to setup ASPM support for the corresponding port:

|Name|Value|Description|
|----|-----|-----------|
|ASPM|0|ASPM disabled|
||1|L0s supported|
||2|L1 supported|
||3|L0s and L1 supported|
||4|Autoconfigure ASPM support|
