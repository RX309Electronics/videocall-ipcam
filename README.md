Sooooo, i bought a new product from AliExpress. This product is a videocall ip camera and has a built-in display which makes it even more interesting. I saw it on sale for only 15 euros so i bought it immediately. It arrived pretty quickly in just 8 days! 

This is the product. 

![IMG20241207145552](https://github.com/user-attachments/assets/f4c826eb-b537-44ae-9c53-06d55671d8cf) 

![IMG20241207145545](https://github.com/user-attachments/assets/88233d62-d0c9-409a-8efc-539c1fa33dba) 

![IMG20241207145549](https://github.com/user-attachments/assets/0afc4b3e-8bf8-4da4-bc9b-35f6d8063038) 

  

  

Now lets open it up! 

![IMG20241207145600](https://github.com/user-attachments/assets/3fb8aa26-ebd5-4865-85c3-756bd1ff7bec) 

To open it you first have to remove this screw which connects the shaft of the stepper motor for left and right to the base and the device. 

  

After that's undone you find 2 screws you also have to undo 

![IMG20241207145620](https://github.com/user-attachments/assets/0fec1280-3088-403e-a756-800e7d909130) 

  

And now its open 

![IMG20241207145726](https://github.com/user-attachments/assets/922c42ec-83d9-4657-b949-b52952c62a6c) 

![IMG20241207145742](https://github.com/user-attachments/assets/1f923392-02ef-4b4b-ba70-f672d2225901) 

  

Here are some inside pics. 

![IMG20241207145947](https://github.com/user-attachments/assets/2ce28c12-39ab-4d6c-817b-9347458ac302) 

![IMG20241207150015](https://github.com/user-attachments/assets/93536d5e-f6c9-4687-9b63-623c91e9c4fd) 

![IMG20241207150123](https://github.com/user-attachments/assets/ffc84c08-21dc-4077-b8da-5c65d1b95b12) 

![IMG20241207150126](https://github.com/user-attachments/assets/cdebdcce-32da-4a96-b363-3f029e385e97) 

  

  

Here we can see the main SOC. It seems to be a Goke gk7201. I never heard of this brand ever before... Might be some mips or arm CPU because I can't really find any information about this model. I do find a datasheet for the gk7205 variant and the gk7202 and some information on this website: https://www.unifore.net/company-highlights/goke-hd-ip-camera-solution-gk7101-gk7102.html. 

![IMG20241207153218](https://github.com/user-attachments/assets/eaee722f-25da-40fe-999b-d722ba90c0fe) 

  

What's also on this board is the Wi-Fi module which is a sv6158 which i have seen quite a lot in these Chinese Ip cameras. the flash is by XMC and is a 25QH64C. The 64 part describes the chip as 64 megaBIT (beware, this is a trap for young players cause you have to divide this by 8) aka a 8megaBYTE flash chip. This chip stores the Uboot bootloader, the kernel, rootfs and maybe some other stuff.  

![IMG20241207153229](https://github.com/user-attachments/assets/b6887910-925f-4ece-9c28-f7da6102689a) 

  

  

Okay let's continue now that you have a better knowledge about the hardware on board. 

As you can see i hooked up a serial cable to the 3 pads on the edge of the board which look quite suspiciously like a UART port for debugging. IF lucky they left it open and enabled, we'll see. 

![IMG20241207154439](https://github.com/user-attachments/assets/813607c8-2dd4-4f2f-abbd-dda399ef99f8) 

  

And, BINGO! We got output! It does seem that they have redirected the kernel output and system shell because after the starting kernel message i got nothing anymore, so they likely set the console to output to /dev/null or some other non existent tty. But we do get the information that this runs Uboot which i already guessed and it uses Linux kernel version 4.9.37 and now we know the cpu is Arm. 
![IMG20241207151808](https://github.com/user-attachments/assets/51962b3a-a05a-42c0-b49b-b4b1fb252576) 

  
Aw snap, its password protected...!! 
![IMG20241207155122](https://github.com/user-attachments/assets/3b1cfe96-6ac2-41d8-bc7d-4aa71714b61c) 

  

Well, I guess I either have to do some research or just glitch the flash chip to hopefully trigger an emergency fallback mechanism to get us into a non password protected bootloader shell. 

  

  

So I decided to take off the flash chip to allow me to read it without the soc interfering so i desoldered it. 
![IMG20241207161137](https://github.com/user-attachments/assets/6b5d4c8d-f8f7-4470-8de4-38983b1a5fb8) 

  

Plugged it into the ch341 and launched neoprogrammer and i got a full 8mb dump of the chip. Awesome! Now we can go further. I decided to resolder it to the board to restore functionality of the device cause now i got a copy of its firmware which i can mess with. I firstly ran it through Bin walk and i got a few dozens of files.  
![IMG20241207165719](https://github.com/user-attachments/assets/db1b0515-8029-4ea5-bf4f-b349ec529a77) 

  

  

Thats quite a lot of Jffs and squashfs folders.... Although most might also just be copies off each other. I might try to sort them out for you: 
![IMG20241207165719](https://github.com/user-attachments/assets/605d618d-b013-4bad-96d6-0ca628bf2b19)

  

'Squashfs-root'. This is the main squashfs filesystem and is just your generic Linux filesystem with /bin /dev /etc /sbin /root /proc /sys and so on. 

  

'squashfs-root-0'. This seems to be the folder that contains the app. It has 8 folders inside of it. a bin folder, etc folder, sbin folder, json folder, lib folder, res folder, share folder, xm-version folder. 
Inside bin I can find a main custom binary called 'app' which is 6.3mb in size but thats becasue its uncompressed by binwalk. This is the star of the show and runs the camera. We also have in this bin folder other binaries. a 'wpa_suplicant' binary which is just generic for Linux and it handles the wireless lan and stuff. Then we have some more custom binaries like 'XmEnv' which i guess does some environment variable stuff. a 'XmGpio' binary which handles the gpio like buttons and the motors and other things. a 'XmWlanDaemon' binary which i guess also handles the wireless lan. Further we have some file called 'productDefinition' which i guess defines the product. And we have a folder called Squirrel which has 2 folders inside. A folder called 'ptz' which has some .nut files in it. And a folder called rs485 which also has some .nut files inside.  
Then we have an 'etc' folder inside this squashfs folder which has 2 scripts inside. A script called 'loadmod' which loads some custom modules. And we have a 'loadpublic' script which loads public libraries.  
We also have a 'json' folder inside this squahsfs folder which contains some json files. We also have a 'lib' folder which contains libraries and kernel modules. Than we have a ‘sbin’ folder which has a script called ‘a 
AppRun.sh’ which seems quite interesting.... And we have a few other scripts. ‘media’, ‘mnt’, ‘run_loop’, ‘udhcp.script’ which i am not going to be talking about much.  
We also have a ‘share’ folder which has a ‘music’ folder inside of it and this is where all the sounds are stored in .pcm format which i could play using aplay! 

Okay, finally we move on from this biiiiiig app partition. 2 Squashfs folders to go! 

‘squashfs-root-1'. This folder seems to contain some css and javascript which i think is for the ui or for a webserver. 

‘squashfs-root-2'. This folder seems to contain some miscellaneous files and folders. We have 4 folders. ‘CustomConfig’ which has some .custom files inside of it, ‘data’ which has 3 folders inside: ‘Fonts’, ‘LVGL’, Strings. The ‘LVGL’ folder contains the UI elements that are on the display. ‘Fonts’ contains the system fonts and ‘strings’ the text. 

  

These are all the important squashfs folders that binwalk managed to extract. In this extracted directory with all the folders we also have a Uboot.bin file which i think is the bootloader. Now i can inspect this file in a hex editor and maybe find some password strings. 

Well, i gave up on digging through the binary after 2 hours and decided to do some research, and what seems? They all use the same Uboot password and thanks to this issue on github i got the password: https://github.com/OpenIPC/firmware/issues/1494. And after typing it in: Bingo! We got into the shell and now have bootloader access! I uploaded a file that has every environmental variable inside of it and i provided a file for the original bootargs and original bootcmd so if you softbrick it but the bootloader is still working you can reset the variable. 

  

  

  

 

 
