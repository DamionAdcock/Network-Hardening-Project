# Network-Hardening-Project

3.5 hrs

Difficulty logging into web interface on first boot (need to check firmware version before flash), web interface on attempt of login (password: admin) flashes to web interface and then resets to login page. I believe this router requires internet connection + initial setup before flashing. I found success by simply plugging an ethernet cable from my router (shaw xb8) into the "internet" port on this router. Since I had already tried and failed to access the interface, I had to hold the reset button for 15 seconds to get the setup wizard to re-engage. I simply followed the prompts to enter SSIDs and passwords. When asked if I wanted to allow automatic updates I chose not to, because it doesn't matter as I'll be flashing this with openwrt anyway and I'd prefer to not be interrupted during the process with an update.

According to the next pop-up after refusing the update, my current firmware version is 1.0.01.10415

Since we're using a version older than 1.2, we can use the unsigned version of the openwrt firmware.

Digressing, the website I gathered my information and files from can be found here:
HTTPS://GitHub.com/dangowrt/owrt-ubi-installer
And here:
HTTPS://GitHub.com/dangowrt/owrt-ubi-installer/releases

And scroll down to Linksys E8450 / Belkin RT3200 V1.1.4

From this list of files under "Assets", since we have an older firmware we'll be downloading the "unsigned" version of the installer which is simply the version that *does not* have the "signed" label.

After multiple attempts I'm going to upgrade the firmware to 1.1.x, I'm still unable to log into the web interface after the initial setup so I can't actually flash with openwrt. This "login loop" is a known issue with 1.0.x firmware so I'm going to upgrade to 1.1 to get past it. I did have to wait about 30 min after resetting the router (factory reset again) and then enable automatic updates. Following those steps I was able to finally trigger the firmware update to recognize that there *is* a newer version and install it. The newer version installed in just a few minutes and I was immediately able to login to the web interface.

After that simply select "Configuration" along the top menu, then "Administration" along the side, then "Firmware Upgrade"
--------------

1hr

Auto-update upgraded us to v1.2, make sure to return to dangowrt's GitHub to download "signed" version of the openwrt firmware

 In web interface:
-under configuration | backup | Backup configurations 
      - Browser requests permission to allow file download, claiming this type of file may hurt PC. Click 3 dot menu on the file and "keep" to allow the file to download.

Now that backup is saved:
Configuration | Administration | Firmware Upgrade:
  -Turn off "Automatic Updates"

- "Select a file to upgrade" Choose file "openwrt-24.10.0-mediatek-linksys_e8450-ubi-initramfs-recovery-installer_signed.itb"
- select "start upgrade"
- wait 5 min, refresh 192.168.1.1 and login with username/password of "root" for both openwrt is now installed.

This is the "recovery" mode.

- At top menu click system | backup/flash firmware

- scroll down to"flash new firmware image and click "flash image" then, "browse" and then find the "sysupgrade" file from dangowrt and click upload

- deselect the "keep settings" option since there are currently no settings

Once it restarts again, LuCI will be fully installed and you're free to set password of your choosing
------
1.25

-network | Wireless
     
    - here you see two wireless networks on the router, these are your 2.4 and 5ghz networks that you can edit the SSIDs and passwords for. They will by default be SSID "openwrt" with no password, recommend at least a strong password.
    - Edit | Wireless security | encryption - set to at least WPA2-PSK
    - key - this is where you enter a strong password, keep note to pass on to client
    - click save
    
    - Enable both
    - for 5ghz:
    - edit | operating frequency | set mode to "ax"
    - General setup | ESSID | use different name from 2.4ghz *or* same name with (5g) at the end. Eg openwrt(5g)


-open CMD

-ssh into router
   ssh root@192.168.1.1

-Type "yes" , "y" will not suffice

-Enter password

- enter command opkg update 

- opkg install adguard home

-hop back to openwrt tab

-network | DHCP and DNS | Devices and Ports | DNS server port | change to 5353 to avoid conflict with adguard

- Forwards | DNS Forwards: enter 127.0.0.1#53 to direct traffic to port 53, where we'll set up adguard in a moment. Click the "+" next to the address.
- scroll down click save and apply


-Go to 192.168.1.1:3000
-click "get started"
-change "listening port" to 8080
-we can leave DNS server as 53, because we changed it on openwrt.
- click "next"
- enter a username and password for adguards web interface
- after signing into adguard, it's important to specify upstream DNS.
- settings | DNS settings | upstream DNS servers 
- enter 8.8.8.8
- Private reverse DNS Servers | enter: 127.0.0.1:5353
- click "Use private reverse DNS resolvers"
- scroll down and click "test upstreams" and then click "apply" when greeted with a green message.
- Filters | DNS Blocklist | add blocklist | choose from list 
- these are decent for ads and spam, however I find the blocklist to be lacking for adult content.
Here I found a blocklist for such content:

https://blocklistproject.github.io/Lists/adguard/porn-ags.txt

This would be added under "add a custom blocklist", the same team that made this specific blocklist also has others that block certain tiktok and even vape sites:

https://blocklistproject.github.io/Lists/

------

To install:
- plug in power and power on Linksys router
- plug in ethernet cable to current "ISP" router
- plug other end of cable to "internet" port on Linksys router
- navigate to ISP router web interface
- on shaw router, enable bridge mode under "General settings"
- on telus router navigate to LAN 1 and enable bridge mode. Ensure LAN 1 is the port used for Linksys router.

****This configuration currently does not allow connection to internet when bridged
