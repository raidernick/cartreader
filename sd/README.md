#### Copy these files to the root of your SD card.  

#### gba.txt  
This file stores the GBA database which is needed because the save type and rom size are not stored inside the rom. 

Example:  
A22J,08,3   
game id, size in MByte, savetype  

Savetypes:   
0 = Unknown or no save   
1 = 4k Eeprom   
2 = 64K Eeprom   
3 = 256K Sram   
4 = 512K Flash   
5 = 1024K Flash   
6 = 512K Sram   

#### n64.txt  
This file stores the N64 database which is needed because the save type and rom size are not stored inside the rom.  
The CRC32 checksum is used to verify a good dump.  

Example:  
002c3b2a,NO7P,32,0  
CRC32, game id, size in MByte, savetype  

Savetypes:  
0 = no save chip  
1 = SRAM  
4 = Flashram  
5 = 4K Eeprom  
6 = 16K Eeprom  

#### snes.txt  
This file is needed for odd sized SNES games like Final Fantasy (JAP), Super Metroid(US/JAP) or Tales of Symphonia. Without this file you will get overdumps and the checksum calculation will fail. There are still a lot games missing from this list.    

Example:  
A172,24,48  
checksum, size in Mbit, number of banks (lorom needs twice as many banks as hirom for the same rom size)  

#### hirom64.map  
This is a Nintendo Power SF Memory mapping file that changes the mapping to a single 4MB HiRom game with 64Kbit/8KByte save. The first byte is used to specify the mapping.  

Bit0-1 SRAM Size (0=2K, 1=8K, 2=32K, 3=None) ;ie. 2K SHL (N*2)  
Bit2-4 ROM Size (0=512K, 2=1.5M, 5=3M, 7=4M) ;ie. 512K*(N+1)  
Bit5 Zero (maybe MSB of ROM Size for carts with three FLASH chips) (set for HIROM:ALL)  
Bit6-7 Mode (0=Lorom, 1=Hirom, 2=Forced HIROM:MENU, 3=Forced HIROM:ALL)  
More info: http://problemkaputt.de/fullsnes.htm#snescartnintendopowerflashcard  

Example:  
0x5d = 0b 01 0 111 01  
01 -> Hirom  
0  
111 -> 4M  
01 -> 8K  

#### lorom256.map  
This is a Nintendo Power SF Memory mapping file that changes the mapping to a single 4MB LoRom game with 256Kbit/32 KByte save.  

Example:  
0x1e = 0b 00 0 111 10  
00 -> Lorom  
0  
0b111 -> 7 -> 4M  
0x10 -> 2 ->32k  

#### mbc5.map
This is a Nintendo Power GB Memory mapping file that changes the mapping to a single 1MB MBC5 game with 32 KByte save.  

GB Mapping Info (by skaman)   
Entries start at 0x0 divided into 3 Byte segments   
   
For example, let's look at the start of the mapping for a multi-game cart:     
A8 00 00 71 04 00 48 94 04 separates into   
A8 00 00 MENU    
71 04 00 GAME 1   
48 94 04 GAME 2   
   
Byte 00 of each entry contains the MBC Type, ROM Size, and SRAM Size (start).    
SRAM Size is 3 bits across Byte 00 and Byte 01.   
     MBC SZE SRAM    
A8 = 101 010 00  0 MBC5, size 2 = 128KB   
71 = 011 100 01  0 MBC3, size 4 = 512KB, SRAM 8KB   
48 = 010 010 00  1 MBC2, size 2 = 128KB, SRAM MBC2   
   
Byte 00: 1st 3 Bits = MBC Type   
        000 = MBC0   
        001 = MBC1   
        010 = MBC2   
        011 = MBC3   
        101 = MBC5   
    
Byte 00: 2nd 3 Bits = ROM Size (minimum size is 128KB due to block size)   
        010 = Size 2 - 128KB   
        011 = Size 3 - 256KB   
        100 = Size 4 - 512KB   
        101 = Size 5 - 1MB   
   
Byte 00: Last 2 Bits (bit1..bit0) + Byte 01: 1st Bit (bit7) = SRAM Size   
        00 0 = NONE   
        00 1 = SRAM MBC2    
        01 0 = SRAM 8KB  
        01 1 = SRAM 32KB   
   
Byte 01 contains the SRAM Size (end) and ROM Block in the Flash.   
ROM Blocks are 128KB (8 total)   
Byte 01: Last 7 Bits (bit6..bit0) = ROM Block   
			00 = ROM Block 0  start offset 0KB   
			04 = ROM Block 1  start offset 128KB   
			08 = ROM Block 2  start offset 256KB   
			0C = ROM Block 3  start offset 384KB   
			10 = ROM Block 4  start offset 512KB   
			14 = ROM Block 5  start offset 640KB   
			18 = ROM Block 6  start offset 768KB   
			1C = ROM Block 7  start offset 896KB   
   
Byte 02 contains the RAM Block in the SRAM.   
RAM Blocks are 8KB (16 total)   
Byte 02: RAM Block:       
			00 = RAM Block 0  start offset 0KB   
			04 = RAM Block 1  start offset 8KB   
			08 = RAM Block 2  start offset 16KB   
			0C = RAM Block 3  start offset 24KB			   
			and so on until RAM Block 15.   
   
Going back to our example cart:   
A8 00 00 MENU:  MBC5, size 2 = 128KB, ROM Block 0, RAM Block 0 (Ignored since SRAM is 000)   
71 04 00 GAME 1:  MBC3, size 4 = 512KB, SRAM 8KB, ROM Block 1, RAM Block 0   
48 94 04 GAME 2:  MBC2, size 2 = 128KB, SRAM MBC2, ROM Block 5, RAM Block 1   
