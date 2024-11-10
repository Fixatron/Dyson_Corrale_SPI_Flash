<h3 align="center">Reading SPI Flash from Dyson Corrale</h3>
## About The Project
I'm familiar with lithium battery software lockouts. Someone presented me with a Dyson Corrale hair straightener with some flashing light issue. Knowing what the Dyson vacuum batteries are capable of doing I started investigating the circuitboard of the wireless hair straightener.
<br>
The batteries read 3.6, 3.6, 3.5 and 3.3 volts, approximately (there are 4 lithium cells). This is not surprising that Dyson would lock out the battery if the difference (delta) of voltage between the cells is more than 0.1 V. 
<br>
I carefully removed the circuitboard and inspected how it works. 
<br>
There are a few chip, but the ones that interest me are the main sytem on chip and the flash memory chip.
<br>
Often times, the lockout will be stored in the flash chip and clearing the flag will unlock the battery. Assuming the cells have been rebalanced.
<br>
I've tried simply clearing the flash chip in the vacuum battery circuitboards with no success. Just leads to a different lockout.
<br>
So, if I could get a dump of the flash chip from a working Dyson Corrale I might be able to write it to the chip on the one that's not working. Or at the very least, see what the differences are and maybe just edit out the battery permanent lockout flag.
<br>
There is the possibility that the lockout is stored in the SoC, which also needs to be cleared.
<br>
The flash chip is an <a href="https://www.digikey.ca/en/products/detail/issi-integrated-silicon-solution-inc/IS25LP064-JKLE/5319688">IS25LP064</a> SPI flash memory chip, which I read using <a href="https://www.flashrom.org/">flashrom</a> on a RPi3+.
<br>
I attempted to read the flash while the chip was on the motherboard, but it gave me an error. Similar to the vacuum batteries, I had to remove it in order to get a read of the chip. This chip is manufactured by ISSI and has been discontinued. 
<br>
I've included the dump of the flash chip. If someone can contribute with a working flash chip dump, maybe we can crack the protocol for other dyson devices that lock out the battery.
<br>
In the flash_dump.hex file, important information seems to be stored every 0x1000 byted of data.
<br>
Likely its this chunk of data at 0x2000 that needs to be edited to remove the lockout. Also, the backup/previously stored data at 0x3000 likely needs to be changed, too, in order to remove the lockout. Likely, it will see the current data and reference the backup (from experience).
<br>
It looks like i may not have gotten a full dump of the flash chip, but I got the important stuff, like what addresses are used, whats repeating and whats not. Theres enough info here to go on if I can manage to get a dump of a working Dyson Corrale.
<br>

## Analysis
Data is written every 0x1000 bytes (ie. 0x0, 0x1000, 0x2000, 0x3000, 0x4000, etc.)
<br>
Chunck at address 0x0 to 0x115 is repeated at 0x4000 to 0x4115
<br>
Section at address 0x3000 to 0x3013 starts similar to chunk at 0x0/0x4000. They all start with 5B 6B B6 (first 3 bytes), the following byte is $A5, whereas 0x03 is $55, making me think that these are bit flags. Up to 0x300F are 0s and 1s, likely bit flags. The final 4 bytes at 0x3010 to 0x3013 also seem significant. 
<br>
Address 0x1000 and 0x2000 start with the same 4 bytes. Not much to make sense of.
<br>

## Raw data
*0x0
<br>
5B 6B B6 55 25 00 01 00 00 00 14 00 00 00 00 00 <br>
25 00 00 00 00 00 24 00 00 00 00 00 00 00 00 00 <br>
00 00 00 00 00 00 00 02 00 00 00 00 00 03 01 00 <br>
00 00 00 09 00 00 00 00 00 0A 00 00 00 00 00 0B <br>
00 00 00 00 00 0C 00 00 00 00 00 0D 00 00 00 00 <br>
00 0E 00 00 00 00 00 0F 00 00 00 00 00 10 00 00 <br>
00 00 00 11 00 00 00 00 00 12 01 00 00 00 00 13 <br>
01 00 00 00 00 14 00 00 00 00 00 19 00 00 00 00 <br>
00 1A 00 00 00 00 00 1B 00 00 00 00 00 1C 00 00 <br>
00 00 00 1D 00 00 00 00 00 1E 00 00 00 00 00 1F <br>
00 00 00 00 00 20 00 00 00 00 00 21 01 00 00 00 <br>
00 24 08 00 00 00 01 25 00 00 00 00 00 26 00 00 <br>
00 00 00 27 00 00 00 00 00 28 00 00 00 00 00 29 <br>
00 00 00 00 00 2A 00 00 00 00 00 2B 00 00 00 00 <br>
00 2C 00 00 00 00 00 2D 00 00 00 00 00 2E 00 00 <br>
00 00 00 2F 00 00 00 00 00 30 00 00 00 00 00 31 <br>
00 00 00 00 00 32 00 00 00 00 00 33 00 00 00 00 <br>
00 80 38 59 A5 5E
<br>
*0x1000<br>
47 4F 4C 7F 46 04 00 00 D8 61 AA 3E 4C 00 00 00 <br>
00 00 00 00 01 00 00 00 01 00 00 00 A1 03 00 00 <br>
45 19 00 00 89 03 00 00 E7 36 D4 42 CC 14 9A 42 <br>
2F D6 E1 41 F6 66 52 41 3E 5B 8D 42 D4 88 AB 41 <br>
5D 04 D5 14<br>
*0x2000<br>
47 4F 4C 7F 10 00 01 C1 80 1A 06 00 34 A1 94 43 <br>
00 00 14 42 00 00 88 41 00 00 88 41 41 21 41 43 <br>
DC 2F 41 43 E5 38 00 00 93 63 E6 B8<br>
*0x3000<br>
5B 6B B6 A5 01 00 01 01 00 00 00 00 00 00 00 00 <br>
7D F5 C9 18<br>
*0x4000<br>
5B 6B B6 55 25 00 01 00 00 00 14 00 00 00 00 00 <br>
25 00 00 00 00 00 24 00 00 00 00 00 00 00 00 00 <br>
00 00 00 00 00 00 00 02 00 00 00 00 00 03 01 00 <br>
00 00 00 09 00 00 00 00 00 0A 00 00 00 00 00 0B <br>
00 00 00 00 00 0C 00 00 00 00 00 0D 00 00 00 00 <br>
00 0E 00 00 00 00 00 0F 00 00 00 00 00 10 00 00 <br>
00 00 00 11 00 00 00 00 00 12 01 00 00 00 00 13 <br>
01 00 00 00 00 14 00 00 00 00 00 19 00 00 00 00 <br>
00 1A 00 00 00 00 00 1B 00 00 00 00 00 1C 00 00 <br>
00 00 00 1D 00 00 00 00 00 1E 00 00 00 00 00 1F <br>
00 00 00 00 00 20 00 00 00 00 00 21 01 00 00 00 <br>
00 24 08 00 00 00 01 25 00 00 00 00 00 26 00 00 <br>
00 00 00 27 00 00 00 00 00 28 00 00 00 00 00 29 <br>
00 00 00 00 00 2A 00 00 00 00 00 2B 00 00 00 00 <br>
00 2C 00 00 00 00 00 2D 00 00 00 00 00 2E 00 00 <br>
00 00 00 2F 00 00 00 00 00 30 00 00 00 00 00 31 <br>
00 00 00 00 00 32 00 00 00 00 00 33 00 00 00 00 <br>
00 80 38 59 A5 5E<br>

## Other Chips
There is a <a href="https://www.ti.com/product/TPS55340-Q1">TPS55340-Q1 boost regulator</a> by Texas Instruments.
<br>
The SoC is made by Freescale Semiconductors and is also discontinued. <a href="https://www.szcomponents.com/product-detail/nxp/mkl17z256vft4r/435590">NPX MKL17Z256VFT4R</a>. Its a 32bit MCU with 256kb flash. <a href="https://www.nxp.com/docs/en/data-sheet/KL17P64M48SF6.pdf">datasheet</a>
<br>

## Setup
I've used a RPi3B and included a pic from a <a href="https://www.youtube.com/watch?v=KNy-_ZzMnG0">YT video</a> for the GPIO pinouts.
<br>
Its possible i tied the hold pin to Vcc, but I forget. Sorry.
<br>
Did a few attempts before I noticed my version of flashrom was old. Did an update and things read smoothly.
<br>
I'm reading the dump file with HxD on Windows or Bliss on Linux.