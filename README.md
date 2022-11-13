# Caravel_pcb

This git repo describes the whole process of PCB design for the Caravel_Board and verifying/testing the MPW_shuttle chips. <br>

# Firmware - Asmita/Aman/Ayyappa
- C code written(which gets concerted to hex along with bootcode) /flowchart in ppt/
- explanation of header file included in the C code (relating it to the efabless caravel documentation)
- C code sent to caravel through the python script through housekeeping flash
- explanation of the python script - caravel_hkflash.py /flowchart in ppt/


# Host to FT232 & FT232 to Host - Nandini/Oishi/Nisha

# Flash Working - Saketh/Yathin/Raj

# Bootcode Understanding - Mayank/Yashwant/Prabal

## Reset Flow:


## Bootcode flow:

The Booting flow during reset and start are almost the same.

- Initializing all the register file from x1 to x31 with value 0, except x2 (Stack pointer) which has been initialized by the reset itself (it is also initiated during start ).

- Copying the initialization value of .data section which involves moving the data from _sidata (start address for the initialization values of the .data section defined in linker script) to between  _sdata and _edata. (start & end address for the .data section defined in linker script respectively).

- Initializing the value present in .bss section (block start symbol) to zero. The .bss section address starts from _sbss to _ebss . 

NOTE : - The .data section and .bss section are present in RAM (as described in linker file) with the origin address of 0x01000000. 
       - The program code to run after reset and other data are present in the Flash with origin address 0x10000000. 
       - The _sidata of .data section is 0x10000210. which stated as _etext is the diassembly file. 

- Setting the CS (Chip Select) bit HIGH and IO0 as output through SPI control register at address 0x28000000 and enabling manual control of SPI.

- Sending optional WREN command through SPI. These command enable access to files, IO , networking etc

- Once the command have been send the SPI is returned to the previous mode (MEMIO). 

## Implementation: 

- The reset signal to the padframe "resetb" was activated as 1us. 
- This triggered the reset flow till the picorv32 "resetn" which was activated at 1.005us.
- The starting address of flash (x10000000) where the bootcode and rest of the programme are present got loaded in the CPU at 1.009us. 
- The instructon present in the (x10000000) got executed at 1.343us. 
- the instruction present in address (0x100000B8) marks the end of bootcode excecution.
- The entire bootcode was executed till 6.747us.
- The period for which the bootcode ran is approximately 5.738us.
- After that the .main section began o execute which included the firmware and blink program.

Note: 
In this implementation the WREN command were not transmitted. 

