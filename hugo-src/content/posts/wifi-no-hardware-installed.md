---
title: "“Wi-Fi: No Hardware Installed“ - What to do?"
date: 2020-04-24T21:01:26+02:00
draft: false
tags: ["Tech Tricks"]
---

Over the past couple of weeks I had been getting an error with the Wi-fi on my Macbook Air (2017). There was an ‘x’ in the Wi-fi symbol and on clicking the icon, the message read “Wi-Fi: No Hardware Installed”.

Since then, I have been researching and trying a whole range of things which might be useful to you if you are facing the same problem.

Firstly, if you are anything like me and are racking your brain for what you did wrong to instigate this error, take a step back and a load off your shoulders. There is a big chance that there was nothing you did (unless you dropped your laptop from a 5 ft height or drowned it in a bucket of water). A whole range of Mac users are facing this problem for the past 8-9 years. I am suprised I hadn’t come across this issue while researching about Apple laptops before buying one.

Anyway, this error has been attributed to both software and hardware problems. And there is no way to find out which one it is at the onset of the problem. In my case, the frequency of this message increased by the day, until last Thursday, when my laptop absolutely refused to connect to the Wi-fi. Based on my experience, it is a slow degenerative ‘disease’ and may manifest in various forms by hampering the function of different aspects of the system (in my case, it was Bluetooth).

Here are a few things I tried:

  * **Resetting the System Management Controller (SMC)**



The SMC is responsible for controlling the low-level functions like thermal, battery, lighting and so on. The functions, symptoms signifying need for SMC reset and detailed steps for reset are described [here](https://support.apple.com/en-in/HT201295).

For laptops with non-removable batteries:

  1. Shut down Mac
  2. Hold down the keys **Shift** \+ **Control** \+ **Option** \+ **Power** button for 10 seconds
  3. Release all the keys simultaneously
  4. Press Power button to start your your Mac


  * **Resetting Nonvolatile random-access memory (NVRAM) or Parameter RAM (PRAM)**



NVRAM and PRAM are a sort of cache which stores information about certain laptop settings to access them easily. More information about its functions and steps for resetting can be found [here](https://support.apple.com/en-in/HT204063).

The steps for reset of NVRAM and PRAM are the same and is as follows:

  1. Shut down Mac
  2. Press **Power** button and immediately hold down the keys **Option** \+ **Command** \+ **P** \+ **R**
  3. Release the buttons simultaneously after hearing the second startup sound from your laptop (about 20 seconds)



Executing these two steps gave me some relief for a brief period and I was desperate to get things back to normal permanently. I visited an Apple Authorised Service Provider and explained my problem to them with a detailed explanation of things I had tried. I explained that I condunted a hardware diagnostic test as mentioned [here](https://support.apple.com/en-in/HT201257) (i.e., Shut down Mac; Turn on and press _D_ immediately till the Apple Hardware Test icon appears). They told me that it would cost INR 3000 for diagnostics and it would take 24-48 hours for this. Depending on what they find, if it is a hardware problem, they may replace the Airport card which may cost north of INR 10000 and could take upto 1-2 weeks. It had been two years since I bought this laptop so it was out-of-warranty. Based on the clueless and under-confident response I received, I choose to give myself some time to figure out a solution for this problem before paying a hefty cost upfront.

I found one solution in this [article](https://medium.com/@ArpadKun/os-x-mojave-wifi-no-hardware-installed-69783ee2e180) where a couple of files from SystemConfiguration were deleted and rebooted. This did not work for me. Alternatively, I tried if any other system configuration file had been corrupted by running **ls -l /etc/sysctl.conf** in the Terminal app. This file did not exist for me so it hinted that the problem was arising from somewhere else. In case you find a file on running the above command check out the steps mentioned [here](https://macreports.com/macos-wifi-no-hardware-installed-error-fix/).

Furthermore, I found that there were four ways that I could connect to internet on my device by going to _System Preferences_ -> _Network_. These methods were via ‘Wi-fi’, ‘Bluetooth PAN’, ‘Thunderbolt Ethernet’ and ‘Thunderbolt Bridge’. The normal way my device connected to the wireless internet connection I had at home was via ‘Wi-fi’. However, in view of recent events, after the resets conducted above, it intermittently connected to the wireless internet via ‘Bluetooth PAN’. It automatically picked up the internet connected but dropped often due to the unpredictable nature of the Bluetooth connection. Based on this, I found a relation between Wi-fi and Bluetooth and the interference of these two networks can cause issues for either of these two connections. To resolve this issue, I alternatively activated and inactivated the four methods in various permutations and combinations to diagnose the issue. Each service could be activated/inactivated by clicking the ‘Settings’ icon at the bottom of the table and clicking ‘Make Service Inactive’. The changes are incorporated once ‘Apply’ is pressed and the laptop is rebooted. I also tried resetting the network preferences. Steps for these have been well-explained [here](https://macreports.com/macos-wifi-no-hardware-installed-error-fix/) and [here](https://www.chriswrites.com/how-to-fix-wifi-connection-problems-in-mac-os-x-lion/).

These were all again temporary fixes so I started looking for ways I could by-pass the laptop’s internal Wi-Fi adapter by perhaps using an external one. I came across such third party Wi-Fi USB dongles when I came across this [article](https://www.macworld.com/article/3187363/edimax-ac1200-dual-band-wi-fi-usb-adapter-review-solve-your-macs-broken-wi-fi-hardware-problem.html) and started looking for available options. The EDiMAX adapter comes in the range of INR 500 - 5000 online. I went to a nearby electronics shop and found a Wireless Nano USB Adapter TL-WN725N by tp-link. It costed INR 550 and is compatible with Windows, Linux and MacOS. Moreover, this adapter has a 3-year warranty. More information about this adapter and a tutorial for setting it up is found [here](https://www.techsupportall.com/tp-link-150mbps-wireless-n-nano-usb-adapter-driver-download-tl-wn725n/). This solution has been working well for me and I am really enjoying the speed and convenience of using this external adapter as opposed to opening up my laptop to run a bunch of diagnostics and have someone tinkering with its insides.

Soon after the internet was back to normal and working flawlessly, I installed a supplementary software patch that was available for macOS Mojave and shut it down. The next morning when I turned on my Mac, the internal Wi-fi adapter connected normally and the ‘Wi-Fi: No Hardware Installed’ disappeared. Despite the connection still being eratic, I alternate my use between the internal and external adapter. I am not sure how the internal adapter magically started working again but it is highly possible that it is linked with the recent Mojave update.

If this entire experience has taught me one thing, I would advice one to ‘INSTALL SOFTWARE UPDATES’ and not postpone it infinitely using the infamous ‘Remind me later’ option. Nevertheless, I am just happy the problem is now permanently fixed one way or the other.

Edit : Make sure that the external adaptor has an updated version of the driver before installing an update for the operating system.

Once I learned to install software updates, I made sure to immediately install macOS Catalina as soon as it was out. It didn’t strike me at the time that the driver I was using for the external adaptor was not compatible with macOS Catalina and was available only till macOS Mojave. Long story short, the internal wifi adaptor was unstable and external adaptor did not work. So I had to reformat my Macbook Air and reinstall macOS Mojave to have access to internet.
