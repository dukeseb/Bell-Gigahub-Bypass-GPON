# How to Bypass Gigahub for Bell Aliant
**Purchase SFP Module Alcatel Lucent G-010S-P**
> https://www.ebay.ca/itm/156370657359

**Media Converter (Not required but I find it useful during configuration)**
> https://www.amazon.ca/10Gtek-Ethernet-Converter-1000Base-Tx-Certified/dp/B09TR4357R/ref=sr_1_4_sspa?crid=2ALES8F8EAMXW&dib=eyJ2IjoiMSJ9.WCFH4cqkAAthxCTUZwBxcHZ7b0XX8w9bD2ngbj9iqYfH7KtFGgYYeriqgZC7q9epHLUDK5QvxcswnE8TRmQBRsB5DnQGKfFpsHG9kBsp1iN9JhoKZRH3eyTgHeKsEblj9nERNNc-bek4dy-ouwh52j9txHvU5G8s175kJ0CclU3ONQ0SLrlh8Eepqsq9u0Pa3C1d0G4zV8SbA_6hKwsps9IAOJ4sZAnAoBu_lqzlPu5tv9nXJznmkGVeHaT8IrJeKkAnXxQk0uEwlbap8AHGUy8Wafcg5C1jPtkDukSe3bQ.6sRbncDfw83JukYxHINyETu9yuaF6RfniMCzH4gkSvg&dib_tag=se&keywords=fiber+media+converter&qid=1726950726&sprefix=fiber+media+converter%2Caps%2C106&sr=8-4-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1

**Retrieve ONT Serial Number from Back of Gigahub. Begins with “SMB”**

**Login to Gigahub interface > Advanced Tools & Settings > WAN**
*Verify Mode GPON*
<img src="https://i.imgur.com/DIXJxvk.png">

**Download / Extract Firmware**
> https://mega.nz/file/lckmXYrA#tvu0ZuUYufVdpO_Ewi1foxz9vhaLSnx-e4ebpN7p_LQ
> - Duplicate the following file `alcatel-g010sp_new_busybox_theme-squashfs.image`
> - Rename `alcatel-g010sp_new_busybox_theme-squashfs.image` to `firmware.image`

**Secure Copy Firmware to SFP**
*From Linux / Mac open Terminal*

`scp -O -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-dss firmware.img ONTUSER@192.168.1.10:/tmp/firmware.img` 

*Password* `SUGAR2A041`

**Configure SFP over SSH**
`ssh -oHostKeyAlgorithms=+ssh-rsa -oPubkeyAcceptedKeyTypes=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group14-sha1 ONTUSER@192.168.1.10`

**Verify image currently being used**
`fw_printenv committed_image`

> *If on Zero image for 1*
>         `mtd -e image1 write /tmp/firmware.img image1`
>         `fw_setenv image1_version firmware`
>         `fw_setenv image1_is_valid 1`
>         `fw_setenv target oem-generic`
>         `fw_setenv committed_image 1`
>         
> *If on One image for 0*
>         `mtd -e image0 write /tmp/firmware.img image0`
>         `fw_setenv image0_version firmware`
>         `fw_setenv image0_is_valid 1`
>         `fw_setenv target oem-generic`
>         `fw_setenv committed_image 0`


**Switch to Custom mibs file**
`uci set gpon.onu.mib_file='/etc/mibs/data_1v_8q.ini'`
`uci commit`
`reboot`

**Access Web Interface (192.168.1.10) [Firefox/Safari may cause problems]**
*Login root/SUGAR2A041*

**Expand the Cog**
<img src="https://i.imgur.com/n8Zv3xL.png">

**Select First Menu Item**
<img src="https://i.imgur.com/PG3ySHQ.png">

**Select the 3rd tab and change your language to English     - Scroll down to save** (you will have to do the apply twice for it to take effect)
<img src="https://i.imgur.com/AnKdgug.png">

**Select the GPON Tab and add your SMB serial number**
<img src="https://i.imgur.com/FADAdnl.png">

**Scroll down and ensure this box it checked for custom mibs**
<img src="https://i.imgur.com/DLhBaNE.png">
*Scroll down to save*

**Click the unsaved/changes link**
Click Save/Apply
<img src="https://i.imgur.com/2DuWsMw.png">

**Remove SFP and Place in Router**
*Configure Router with VLAN 35*
<img src="https://i.imgur.com/xl34tRv.png">

---

> ***Note After comitting to a new image your SSH key and Login will change you can access the SFP to do further configuration with the following (you may have to delete your stored key from your known keys file)***
> `ssh -oHostKeyAlgorithms=+ssh-rsa -oPubkeyAcceptedKeyTypes=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group14-sha1 root@192.168.1.10`


---

Thank you to the 8311 Discord Community for helping sort this out

https://github.com/rajkosto/alcatel_lucent-lantiq_falcon/tree/master
