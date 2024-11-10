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

## Analysis
After analysing a few bad dumps I thought I was onto something. Once I tied the Hold pin to Vcc, I finally got full clean reads.
<br>
There seems to be clear separation of data, but theres too much of it to make any sense of.
<br>
If I had a dump from a working one I presume I could flash it to the battery-locked device and it would work again.
<br>
I dont feel theres much point to dig much deeper until I get a dump from a working device.

## Other Chips
There is a <a href="https://www.ti.com/product/TPS55340-Q1">TPS55340-Q1 boost regulator</a> by Texas Instruments.
<br>
The SoC is made by Freescale Semiconductors and is also discontinued. <a href="https://www.szcomponents.com/product-detail/nxp/mkl17z256vft4r/435590">NPX MKL17Z256VFT4R</a>. Its a 32bit MCU with 256kb flash. <a href="https://www.nxp.com/docs/en/data-sheet/KL17P64M48SF6.pdf">datasheet</a>
<br>

## Setup
I've used a RPi3B and included a pic from a <a href="https://www.youtube.com/watch?v=KNy-_ZzMnG0">YT video</a> for the GPIO pinouts.
<br>
I had to tie the hold pin to high or the chip would reset during the read
<br>
Did a few attempts before I noticed my version of flashrom was old. Did an update and things read smoothly.
<br>
I'm reading the dump file with HxD on Windows or Bliss on Linux.