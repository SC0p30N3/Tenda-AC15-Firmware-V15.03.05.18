# Tenda-AC15-Firmware-V15.03.05.18
Want to give credit to rampageX for his amazing firmware mod kit scripts found here https://github.com/rampageX/firmware-mod-kit
As well as all the work done by https://github.com/devttys0 in the Reverse Engineering world.

I became curious about my Tenda AC15 when I noticed a telnet daemon running on it, despite disabling remote management.
I came accross information about the 2013 backdoor here https://ea.github.io/blog/2013/10/18/tenda-backdoor/ and http://www.devttys0.com/2013/10/from-china-with-love/

I decided to poke around at Tenda's latest firware to see what else maybe lurking. This began with cursory passes with 'binwalk' and 'strings -a', but once decompiled using rapageX's 'extract-firmware.sh', I could explore the entire filesystem. After manual directory exploration and mapping came more 'strings -a'. Greping for strings such as 'telnet' 'admin' 'pass'.

It was a grep for 'passwd' that shed the light on a still existing problem with Tenda's firmware.

under 'rootfs/etc_ro/passwd' the following accounts and password hashes are found.

root:$1$nalENqL8$jnRFwb1x5S.ygN.3nwTbG1:0:0:root:/:/bin/sh
admin:6HgsSsJIEOc2U:0:0:Administrator:/:/bin/sh
support:Ead09Ca6IhzZY:0:0:Technical Support:/:/bin/sh
user:tGqcT.qjxbEik:0:0:Normal User:/:/bin/sh
nobody:VBcCXSNG7zBAY:0:0:nobody for ftp:/:/bin/sh

The user and support accounts both share a clear text password of '1234'. From a local network, these accounts can access a root shell over telnet. Confirmed as of 12/17/17 on this firmware on my AC 15. When logged in, I attemped to close the hole by stopping busybox's 'telnetd' daemon. While this drops the connection and stops the service temporarily, eventually the port shows up as open again from the LAN network.

Other areas of interest in discovered:
"tendauploadcfe"
"alibaba_update"
These are part of the update mechanism for the device. Interested in hosting/validation.

"p910nd"
"ZeroClipboard.swf"
"3322ip"

"HP_P1008.img"
This file contained hardcoded domains and IPs which I wasn't sure of the origin, but made note of anyway. (Code documentation seems decent, but my Chinese is not, however.)

Domains found in HP_P1008.img:
9158.com
info.client.xunlei.com
xl7.xunlei.com
tfile.gw.com.cn
index.10jqka.com.cn
dl.baofeng.com
202.108.14.212
treexml.baofeng.net
igw2.sdo.com
9158.com
info.client.xunlei.com
xl7.xunlei.com
tfile.gw.com.cn
index.10jqka.com.cn
treexml.baofeng.net
igw2.sdo.com

