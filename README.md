# ElegooFix
A tutorial for how to use serial to force update you Elegoo Neptune 4 Max 3D-printer.
(should also work for others)

I've had some problems with my Neptune 4 Max. Other than the bed being a nightmare to level and some factory hot glue getting into the mainboard fan, the updates have been a real pain in the ass too.
The Elegoo documentation will tell you to download the new firmware folder, unzip it and copy the 'ELEGOO_UPDATE_DIR' folder onto a flash drive formatted in fat32. The drive also has to be USB 2.0 (no USB 3.0) and have a capacity of 32GB at most.
Then the documentation tells you to simply plug your drive in the front USB-A port of your printer, go to 'Setting'->'about this printer' and click the up-arrow.

Let me preface this with saying that this has worked for me before. You should definetely try this first. 
What I'm about to describe should be your last course of action. Not because it is dangerous or voids your warranty, but because it's fairly technically difficult and should only be attempted by those who don't mind f*cking around.

One day when I was feeling in the mood, I decided to check the Elegoo Download Centre and check if there where any new firmware files. Sure enough, there where.
I tried to update my printer using them but as soon as I did, it didn't recognise its components anymore. It wouldn't extrude, move or heat. The touchscreen would work to some extent and the lights would turn on when i turned on the printer but nothing else.
I flashed an older firmware and emailed Elegoo, but the issue persisted.
Elegoo decided to send me a new mainboard, EMMC-module (we'll get to this later) and screen.

Now, my screen firmware update always worked so I wasn't worried about fixing that, but when the hardware finally arrived I disassembled my printer and installed the new mainboard with its new EMMC-module.
I turned on the printer and everything worked but it was running an old firmware.

Since I had informed Elegoo of their faulty firmware downloads that brick printers and weeks had gone by since then I assumed that the firmware files on their website would be fixed. So I downloaded a recent one and tried to flash it.

The printer crashed immediately. As soon as I plugged in the flash drive, Klipper crashed.

Now, for those who don't know, an EMMC-module is basically a different formfactor for the same storage technology as a SD-card. I ordered a cheap EMMC-flasher with overnight shipping and decided to flash the module directly.
This, however, can't be done with regular firmware update files. You need a .img-file and all I could find were from two years ago (November 2023). I tried it anyway and the flashing worked. In case you want to try it, here's how I did it:

1. Plug the module into the flasher and connect it to your computer.
2. Download the Raspberry Pi imager.
3. Select the EMMC-module as the target storage and the .img-file under 'custom image'.
4. Click flash and agree to everything. This will wipe the module so be aware of that.
5. When it's done plug the module back into your printer and start it up.

The flashing worked but the problem persisted so this probably won't work for you either and I haven't provided the files or links to the files for that reason. What I've done is taking the second EMMC-module I had, plugging into my printer and connecting over serial.

Serial is a protocol usually used for industrial applications, but for our use, it's similar to SSH except it doesn't use a network connection.

Linux (I use Arch btw):
1. Prepare a USB-drive with firmware update files as usual and check what the .deb-file is called. If you're on a very old firmware check if the fix pack is newer. If yes flash it first before you flash the most recent firmware.
2. Connect your computer to the USB-C port on the front of your printer. Make sure your cable can transmit data. Many phone charging cables can't.
3. Open up the terminal and type 'sudo dmesg | grep tty'
4. There should be some device called CR-something-or-other. Remember which tty it's connected to.
5. Install 'screen' with 'sudo pacman -S screen', 'sudo apt install screen' or 'sudo dnf install screen'.
6. Type 'sudo screen *your TTY (mine was /dev/ttyUSB0)* 115200'. 115200 is your baud rate which is the speed your printer communicates at over serial.
7. Type enter until you reach a login screen. The user name is 'mks' and the password is 'makerbase'.
8. Now connect your USB-drive to the printer.
9. Type 'ls' and make sure there's a 'mks' folder. That's where the drive is mounted.
10. Now type 'sudo nano update.sh' and edit the command in it so that the .deb-file name is correct.
11. 'ctr+X' to close, 'y' to save.
12. Run 'sudo ./update.sh' and wait for it to finish.
13. Restart your printer and check if everything works.

This worked for me. Let me know if you have any questions.



