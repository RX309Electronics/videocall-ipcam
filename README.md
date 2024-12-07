Sooooo, i bought a new product from aliexpress. This product is a videocall ip camera and has a built in display which makes it even more interesting. I saw it on sale for only 15 euros so i bought it inmideatly. It arrived pretty quickly in just 8 days!
This is the product.
![IMG20241207145552](https://github.com/user-attachments/assets/f4c826eb-b537-44ae-9c53-06d55671d8cf)

![IMG20241207145545](https://github.com/user-attachments/assets/88233d62-d0c9-409a-8efc-539c1fa33dba)

![IMG20241207145549](https://github.com/user-attachments/assets/0afc4b3e-8bf8-4da4-bc9b-35f6d8063038)

Now lets open it up!
![IMG20241207145600](https://github.com/user-attachments/assets/3fb8aa26-ebd5-4865-85c3-756bd1ff7bec)
To open it you first have to remove this screw which connectes the shaft of the steppermotor for left and right to the base and the device.

After thats undone you find 2 screws you also have to undo
![IMG20241207145620](https://github.com/user-attachments/assets/0fec1280-3088-403e-a756-800e7d909130)

And now its open
![IMG20241207145726](https://github.com/user-attachments/assets/922c42ec-83d9-4657-b949-b52952c62a6c)
![IMG20241207145742](https://github.com/user-attachments/assets/1f923392-02ef-4b4b-ba70-f672d2225901)

Here are some inside pics.
![IMG20241207145947](https://github.com/user-attachments/assets/2ce28c12-39ab-4d6c-817b-9347458ac302)
![IMG20241207150015](https://github.com/user-attachments/assets/93536d5e-f6c9-4687-9b63-623c91e9c4fd)
![IMG20241207150123](https://github.com/user-attachments/assets/ffc84c08-21dc-4077-b8da-5c65d1b95b12)
![IMG20241207150126](https://github.com/user-attachments/assets/cdebdcce-32da-4a96-b363-3f029e385e97)


Here we can see the main SOC. It seems to be a Goke gk7201. I never heard of this brand ever before... Might be some mips or arm cpu cause i cant really find any information about this model. I do find a datasheet for the gk7205 variant and the gk7202 and some information on this website: https://www.unifore.net/company-highlights/goke-hd-ip-camera-solution-gk7101-gk7102.html.
![IMG20241207153218](https://github.com/user-attachments/assets/eaee722f-25da-40fe-999b-d722ba90c0fe)

Whats also on this board is the wifi module which is a sv6158 which i have seen quite a lot in these chinese ipcameras. the flash is by XMC and is a 25QH64C. The 64 part describes the chip as 64 megaBIT (beware, this is a trap for young players cause you have to divide this by 8) aka a 8megaBYTE flash chip. This chip stores the Uboot bootloader, the kernel, rootfs and maybe some other stuff. 
![IMG20241207153229](https://github.com/user-attachments/assets/b6887910-925f-4ece-9c28-f7da6102689a)


Okay lets continue now that you have a better knowledge about the hardware on board.
As you can see i hooked up a serial cable to the 3 pads on the edge of the board which look quite suspiciously like a UART port for debugging. IF lucky they left it open and enabled, we'll see.
![IMG20241207154439](https://github.com/user-attachments/assets/813607c8-2dd4-4f2f-abbd-dda399ef99f8)

And, BINGO! We got output!
![IMG20241207151808](https://github.com/user-attachments/assets/51962b3a-a05a-42c0-b49b-b4b1fb252576)




