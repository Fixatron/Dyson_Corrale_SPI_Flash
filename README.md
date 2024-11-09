<h3 align="center">Reading SPI Flash from Dyson Corrale</h3>
## About The Project
I'm familiar with lithium battery software lockouts. Someone presented me with a Dyson Corrale hair straightener with some flashing light issue. Knowing what the Dyson vacuum batteries are capable of doing I started investigating the circuitboard of the wireless hair straightener.
The batteries read 3.6, 3.6, 3.5 and 3.3 volts, approximately (there are 4 lithium cells). This is not surprising that Dyson would lock out the battery if the difference (delta) of voltage between the cells is more than 0.1 V. 
I carefully removed the circuitboard and inspected how it works. 
There are a few chip, but the ones that interest me are the main sytem on chip and the flash memory chip.
Often times, the lockout will be stored in the flash chip and clearing the flag will unlock the battery. Assuming the cells have been rebalanced.
I've tried simply clearing the flash chip in the vacuum battery circuitboards with no success. Just leads to a different lockout.
So, if I could get a dump of the flash chip from a working Dyson Corrale I might be able to write it to the chip on the one that's not working. Or at the very least, see what the differences are and maybe just edit out the battery permanent lockout flag.
There is the possibility that the lockout is stored in the SoC, which also needs to be cleared.
The flash chip is an <a href="https://www.digikey.ca/en/products/detail/issi-integrated-silicon-solution-inc/IS25LP064-JKLE/5319688">IS25LP064</a> SPI flash memory chip, which I read using <a href="https://www.flashrom.org/">flashrom</a> on a RPi3+.
I attempted to read the flash while the chip was on the motherboard, but it gave me an error. Similar to the vacuum batteries, I had to remove it in order to get a read of the chip. This chip is manufactured by ISSI and has been discontinued. 
I've included the dump of the flash chip. If someone can contribute with a working flash chip dump, maybe we can crack the protocol for other dyson devices that lock out the battery.
In the flash_dump.hex file, important information seems to be stored every 0x1000 byted of data. For example, 0x0 has a block of data, which seems to be repeated at 0x4000. Also, 0x1000 seems to have some more data. The older version (i'm assuming) is stored at 0x3000.
Likely its this chunk of data at 0x2000 that needs to be edited to remove the lockout. Also, the backup/previously stored data at 0x3000 likely needs to be changed, too, in order to remove the lockout. Likely, it will see the current data and reference the backup (from experience).
It looks like i may not have gotten a full dump of the flash chip, but I got the important stuff, like what addresses are used, whats repeating and whats not. Theres enough info here to go on if I can manage to get a dump of a working Dyson Corrale.

## Other Chips
There is a <a href="https://www.ti.com/product/TPS55340-Q1">TPS55340-Q1 boost regulator</a> by Texas Instruments.
The SoC is made by Freescale Semiconductors and is also discontinued. <a href="https://www.szcomponents.com/product-detail/nxp/mkl17z256vft4r/435590">NPX MKL17Z256VFT4R</a>. Its a 32bit MCU with 256kb flash. <a href="https://www.nxp.com/docs/en/data-sheet/KL17P64M48SF6.pdf">datasheet</a>

## Setup
I've used a RPi3B and included a pic from a <a href="https://www.youtube.com/watch?v=KNy-_ZzMnG0">YT video</a> for the GPIO pinouts.
Its possible i tied the hold pin to Vcc, but I forget. Sorry.
Did a few attempts before I noticed my version of flashrom was old. Did an update and things read smoothly.
I'm reading the dump file with HxD on Windows or Bliss on Linux.