# Fedora post installation guide

__*Update to Fedora 35.*__

Welcome to my personal Fedora post installation guide. In this document, you can see the steps I took after a Fedora 33 fresh installation.

Ideas and suggestions were collected from different sources and from my previous experience with Fedora.
Use this guide at your own risk.

## Index
<!-- TOC -->

- [Fedora post installation guide](#fedora-post-installation-guide)
    - [Index](#index)
    - [Update entire system](#update-entire-system)
        - [Enable Fedora Workstation repositories](#enable-fedora-workstation-repositories)
        - [DNF : Enable DeltaRPM and Faster Mirror plugins](#dnf--enable-deltarpm-and-faster-mirror-plugins)
        - [Enable RPM Fusion repositories](#enable-rpm-fusion-repositories)
        - [Install Nvidia drivers](#install-nvidia-drivers)
        - [Optional-Fedora 35 - Downgrade driver to version 470](#optional-fedora-35---downgrade-driver-to-version-470)
        - [Avoid screen tearing](#avoid-screen-tearing)
        - [Monitor system fans speed](#monitor-system-fans-speed)
        - [Update Firmware](#update-firmware)
    - [Customize Gnome Shell](#customize-gnome-shell)
        - [Install Gnome Tweaks](#install-gnome-tweaks)
        - [Install Gnome Extension App](#install-gnome-extension-app)
        - [Add bottom panel](#add-bottom-panel)
        - [Gnome-Shell extensions:](#gnome-shell-extensions)
        - [Places Status Indicator](#places-status-indicator)
        - [TopIcons Plus](#topicons-plus)
        - [Change system fonts](#change-system-fonts)
        - [Better font rendering in Fedora](#better-font-rendering-in-fedora)
        - [Enable minimize maximize buttons](#enable-minimize-maximize-buttons)
        - [Replace Nautilus with Nemo](#replace-nautilus-with-nemo)
        - [Use location entry in Nautilus](#use-location-entry-in-nautilus)
        - [Desktop icons extension](#desktop-icons-extension)
    - [Useful packages](#useful-packages)
        - [Codecs](#codecs)
            - [Multimedia: Music and movies](#multimedia-music-and-movies)
            - [OpenH264](#openh264)
            - [Hardware video acceleration](#hardware-video-acceleration)
        - [File manager](#file-manager)
            - [Midnight Commander](#midnight-commander)
        - [Editors](#editors)
            - [Atom](#atom)
            - [VSCodium](#vscodium)
        - [Graphic design editors](#graphic-design-editors)
            - [Gimp and Inkscape](#gimp-and-inkscape)
        - [Multimedia](#multimedia)
            - [HandBrake](#handbrake)
            - [MPV - Multimedia player](#mpv---multimedia-player)
        - [Important Firefox / Chrome addons](#important-firefox--chrome-addons)
        - [Games : Steam](#games--steam)
        - [Dropbox](#dropbox)
    - [Browser hardware acceleration](#browser-hardware-acceleration)
        - [Google Chrome](#google-chrome)
        - [Mozilla Firefox](#mozilla-firefox)
            - [Enable hardware acceleration](#enable-hardware-acceleration)
            - [Enable WebGL](#enable-webgl)
        - [Check if WebGL is available in any browser](#check-if-webgl-is-available-in-any-browser)
    - [Google Chrome: Enable Memory Saver to Reduce RAM/CPU Usage](#google-chrome-enable-memory-saver-to-reduce-ramcpu-usage)

<!-- /TOC -->

## Update entire system
After a clean Fedora install is recommended to update the entire system. This fixes potential bugs that could not be fixed for the initial distribution release. Open a terminal and type:

```bash
sudo dnf update
```
After the update, some new repositories could be enabled.

### Enable Fedora Workstation repositories
The [Third Party Software Repositories](https://fedoraproject.org/wiki/Workstation/Third_Party_Software_Repositories) is not enabled by default because Fedora promotes free and open source resources. These repositories include restricted softwares that are not free and open source but can be installed to improve usability. Some of the included software are Google Chrome, nVidia graphic drivers and Steam client.

These repositories can be managed in GNOME Software in the "Software Repositories" settings or using DNF.

```bash
sudo dnf install fedora-workstation-repositories
```
The repositories are disabled by default. To enable individual repositories, use the following command:
```bash
sudo dnf config-manager --set-enabled <name of repository from link above>
```
Similarly, individual repositories can be disabled using:
```bash
sudo dnf config-manager --set-disabled <name of repository from link above>
```
To remove all of these repositories, use the following command:
```bash
sudo dnf remove fedora-workstation-repositories
```
### DNF : Enable DeltaRPM and Faster Mirror plugins
With these two modules, you can speed up the download process.

DeltaRPM is a module that only downloads the differences between the current installed packages and the update available in the mirror. Once downloaded, rebuild the new package in your local computer.

Fastest Mirror is a plugin that determines the nearest mirror available to you.

To enable both plugins, append the next two lines into your ```/etc/dnf/dnf.conf``` file:
```bash
fastestmirror=true
deltarpm=true
```

### Enable RPM Fusion repositories

RPM Fusion is a repository of add-on packages for Fedora maintained by a group of volunteers. This repository distributes packages that have been deemed unacceptable to Fedora. This means that software encumbered with US patents cannot be included in Fedora. Fedora further only wants to ship software that is covered by Free and Open-Source-Software licenses.

**_Free repository_** : Software that uses a free license, but is not accepted in Fedora for various reasons.

```bash
sudo dnf install
https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
```

**_Nonfree repository_** : Software that uses a non free license, but is otherwise redistributable.

```bash
sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### Install Nvidia drivers
After installing RPMFusion, you can replace free Nouveau drivers with privative Nvidia drivers. More information in [RPMFusion website](https://rpmfusion.org/Howto/NVIDIA)

To determine which driver you need to install, you need to find your graphic card model.

```bash
sudo /sbin/lspci | grep -e VGA
```
With a relative new graphic card:
```bash
sudo dnf update -y # and reboot if you are not on the latest kernel
sudo dnf install akmod-nvidia # rhel/centos users can use kmod-nvidia instead
sudo dnf install xorg-x11-drv-nvidia-cuda #optional for cuda/nvdec/nvenc support
```
Once the module is built, ```modinfo -F version nvidia``` should outputs the version of the driver

### (Optional-Fedora 35) - Downgrade driver to version 470
__*This step is optional and only required if your GPU is not supported by NVidia-495 drivers.*__

Fedora 35 installs nVidia drivers version 495 but this version don't support cards from 600 and 700 series. In this cards, the graphic driver will fallback to Nouveau. To use the nVidia drivers should uninstall 495 version and install the 470 version.
```bash
sudo dnf remove akmod-nvidia*
sudo dnf remove *nvidia*
```
Install the nNvida-470 drivers:

```bash
dnf install akmod-nvidia-470xx.x86_64 nvidia-settings-470xx.x86_64 xorg-x11-drv-nvidia-470xx.x86_64 xorg-x11-drv-nvidia-470xx-power.x86_64 xorg-x11-drv-nvidia-470xx-cuda.x86_64 xorg-x11-drv-nvidia-470xx-cuda-libs.i686 xorg-x11-drv-nvidia-470xx-cuda-libs.x86_64 xorg-x11-drv-nvidia-470xx-devel.x86_64 xorg-x11-drv-nvidia-470xx-kmodsrc.x86_64 xorg-x11-drv-nvidia-470xx-libs.i686 xorg-x11-drv-nvidia-470xx-libs.x86_64
```

### Avoid screen tearing
This option reduces the image tearing when reproducing videos but may reduce the performance of some OpenGL applications and may produce issues in WebGL.

If you have problems with screen tearing and want to try this solution, open (or create) the file ```/etc/X11/xorg.conf``` and add  this line in the section Screen:
```bash
Section "Screen"
...
	Option "metamodes" "nvidia-auto-select +0+0 { ForceCompositionPipeline = On }"
...
EndSection
```
If the file doesn't exists can be created using ```nvidia-settings```.![nvidia-settings](/images/nvidia-triple-buffer.png)



### Monitor system fans speed
I like to use [GKrellM](http://members.dslextreme.com/users/billw/gkrellm/gkrellm.html) to monitor multiple system variables. But to monitor fan speed, you need to execute sensors-detect to determine which kernel modules need to load to use lm_sensors with my motherboard.

```bash
sudo sensors-detect
```
And I have this result with detected sensors, the right kernel module and the option to overwrite ```/etc/sysconfig/lm_sensors``` file.
```bash
Now follows a summary of the probes I have just done.
Just press ENTER to continue:

Driver `nct6775':
  * ISA bus, address 0xa20
    Chip `Nuvoton NCT6797D Super IO Sensors' (confidence: 9)

Driver `k10temp' (autoloaded):
  * Chip `AMD Family 17h thermal sensors' (confidence: 9)

Do you want to overwrite /etc/sysconfig/lm_sensors? (YES/no):
```
### Update Firmware
Some manufacturers support firmware and UEFI updates on Linux. The following commands will find devices with firmware, search for possible firmware updates, and install the updates.
```bash
sudo fwupdmgr get-devices
sudo fwupdmgr refresh --force
sudo fwupdmgr get-updates
sudo fwupdmgr update
```

## Customize Gnome Shell

### Install Gnome Tweaks
Gnome Tweaks is used in conjunction with the Gnome Shell to modify its interface.
```bash
sudo dnf install gnome-tweaks
```
### Install Gnome Extension App
Starting with Fedora 35, Gnome Tweaks is not used anymore to enable or configure Gnome Extensions. You must install Gnome Extensions App, an standalone app and not installed by default.
```bash
sudo dnf install gnome-extensions-app
```

### Add bottom panel
**_Tint 2_** : I like to have a bottom panel that shows the current open windows. Also shows automatically when i move the cursor over it and hides automatically.
```bash
sudo dnf install tint2
```
My tint2 config files are available in the [dotfiles](dotfiles) directory.

### Gnome-Shell extensions:
In Gnome Shell you can install some extensions to improve Gnome usability and productivity. These extensions can be donwloaded from [Gnome extensions](https://extensions.gnome.org/) or can be installed with DNF.
After install any extension you can enable it using Gnome Tweaks (in Fedora 34 o lower) or Gnome Extensions App (from Fedora 35).

### Places Status Indicator
This extension add a menu for quickly navigating places in the system.
![extension_places](/images/extension_places.png)

If you bookmark yout most useful locacionts on your filesystem with Nautilus, it will appear in this extension.

This extension can be downloaded from [Gnome Extensions Download](https://extensions.gnome.org/extension/8/places-status-indicator/) or can be installed with DNF.

```bash
sudo dnf install gnome-shell-extension-places-menu
```

### TopIcons Plus
This extension moves legacy tray icons (bottom left of Gnome Shell) to the top panel.
Not all applicacions integrate with Gnome (for example, Liferea, Dropbox...), and still use the classic implementation. With this extension those tray icons will be moved to the top right side of the panel.
This extension can be downloaded from [Gnome Extensions Download](https://extensions.gnome.org/extension/1031/topicons/) or can be installed with DNF.

```bash
sudo dnf install gnome-shell-extension-topicons-plus
```

### Change system fonts
This is a matter of taste, but I prefer Ubuntu fonts than the Fedora default fonts.

Download Ubuntu fonts from [Google Fonts](https://fonts.google.com/specimen/Ubuntu).

Check if the directory ```/usr/share/fonts``` exists. If the fonts directory do not exist,you can create yourself.
```bash
sudo mkdir -p /usr/share/fonts
```
Now unzip the downloaded Ubuntu fonts file and copy ```ubuntu-font-family-0.83``` directory into ```/usr/share/fonts/```

Now run the fc-cache command to rebuild font information.If you do not get any error then the fonts are successfully installed.
```bash
sudo fc-cache -fv
```

Open Gnome Tweaks and select the Ubuntu font and set hintting and subpixel antialiasing. I restart my session, so changes can be applied.

![Ubuntu fonts](/images/fontsubuntu_eng.png)

### Better font rendering in Fedora
This repository provides free substitutions for popular proprietary fonts from Microsoft and Apple operating systems. This replacement makes web pages appear closer to what they do on Windows and not using the same font in every webpage.

Subpixel hinting, as it turns out, is patented by Microsoft so most Linux distributions don’t ship with it enabled. Optionally you can enable subpixel (rgb) antialiasing.

Enable COPR repository with packages from this repo:
```bash
sudo -s dnf copr enable dawid/better_fonts
```
Install packages:
```bash
sudo -s dnf install fontconfig-font-replacements
```

Enable subpixel (rgb) antialiasing:
```bash
sudo -s dnf install fontconfig-enhanced-defaults
```
Log out and log in again or restart computer.

### Enable minimize maximize buttons
I like to have the minimize and maximize buttons in the windows title bar. And can be enabled using GNOME Tweaks:
![Ubuntu fonts](/images/windowButtons_eng.png)

### Replace Nautilus with Nemo
I don't like Nautilus as a file manager, so I usually replace it with a Nemo file manager.

```bash
sudo dnf install nemo nemo-extensions nemo-fileroller
```
Then I copy ```nemo.desktop``` file from ```/usr/share/applications/``` to ```~/.local/share/applications/``` and  remove the line `"OnlyShowIn=X-Cinnamon;"`.

### Use location entry in Nautilus
When I use Nautilus, I prefer to always have a text navigation bar. Can be changed temporary using ```Ctrl + L``` or permanently in a terminal:

```bash
$ gsettings set org.gnome.nautilus.preferences always-use-location-entry true
```
*Navigation bar before:*
![before](/images/nautilus_bar_before.png)

*Navigation bar after:*
![after](/images/nautilus_bar_after.png)

### Desktop icons extension
Desktop Icons NG is an extension for Gnome Shell that adds icons to the desktop. Also adds:

* Drag'n'Drop, both inside the desktop, between desktop and applications, and Nautilus/Nemo windows
* Allows to use "Open with..." option with several files.

To manually install the extension follow this steps:

Download the extension [Desktop Icons NG](https://extensions.gnome.org/extension/2087/desktop-icons-ng-ding/) from Gnome Extensions. Select an appropriate gnome shell version and extensions version.

Unzip the downloaded file and open ```metadata.json``` file. Copy the UUID of your extension (the UUID is: ding@rastersoft.com) and create a new directory into which we will unzip the content of the previusly downloaded gnome extension. The directory name should be the UUID of this Gnome extension.

```bash
$ mkdir -p ~/.local/share/gnome-shell/extensions/ding@rastersoft.com
```
Unzip Gnome extension into previously created directory. And enable the newly installed extension using ```gnome-extensions-app```.

Finally restart gnome-shell (exit from current session or pressing Alt-F2 and writing "r" in the opened window).

## Useful packages
These are some packages I like to install in my Fedora machine. Just personal taste.

### Codecs
#### Multimedia: Music and movies
Fedora distributions don't install some codecs because they are not open source or are privative or covered by software patents. But can be installed using RPMFusion.
```bash
sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel
sudo dnf install lame\* --exclude=lame-devel
sudo dnf group upgrade --with-optional Multimedia
```

#### OpenH264
Cisco provides an OpenH264 codec (as a source and a binary), which is their implementation of H.264 codec, and they cover all licensing fees for all parties using their binary. This codec allows you to use H.264 in WebRTC with gstreamer and Firefox. It does not enable generic H.264 playback, only WebRTC
More information and instructions [https://docs.fedoraproject.org/](https://docs.fedoraproject.org/en-US/quick-docs/openh264/)


#### Hardware video acceleration
Hardware video acceleration makes it possible for the video card to decode/encode video, thus offloading the CPU and saving power.
```bash
dnf install libva-utils libva-vdpau-driver vdpauinfo
```

### File manager
#### Midnight Commander
```bash
sudo dnf install mc
```

### Editors

#### Atom

Download the RPM package from [www.atom.io](www.atom.io) and install using the RPM command tool.
```bash
sudo rpm -Uvh atom.rpm
```
Add this Community Packages:
```bash
├── atom-beautify@0.33.4
├── atom-ide-ui@0.13.0
├── delete-lines@0.5.0
├── file-icons@2.1.43
├── ide-css@0.3.5
├── ide-html@0.6.2
├── ide-json@0.2.1
├── ide-python@1.5.0
├── markdown-preview-enhanced@0.18.5
├── markdown-toc@0.4.2
└── minimap@4.29.9
```
### Graphic design editors
#### Gimp and Inkscape
```bash
sudo dnf install inkscape gimp
```

### Multimedia
#### HandBrake
```bash
sudo dnf -y install handbrake-gui
```

#### MPV - Multimedia player
```bash
sudo dnf -y install mpv
```

### Important Firefox / Chrome addons
[HTTPS Everywhere](https://www.eff.org/es/https-everywhere): Force the use of HTTPS protocol in all webpages (if available).

[uBlock Origin](https://github.com/gorhill/uBlock): The famous ad-blocker plugin.

[Privacy Badger](https://privacybadger.org/): Block tracking scripts and other 3rd-party online tracking software.

### Games : Steam
After you have enabled the non-free RPM Fusion repository, you can install Steam on Fedora using:

```bash
sudo dnf install steam
```
### Dropbox
```bash
sudo dnf install dropbox nautilus-dropbox nemo-dropbox
```

## Browser hardware acceleration
### Google Chrome
After installing Google Chrome, you can enable GPU hardware acceleration. Open new web and navigate to ```chrome:flags``` and and set to enabled these flags:
- ```Override software rendering list```
- ```GPU rasterization```
- ```Zero-copy rasterize```.

After restarting the navigator, in tab ```chrome://gpu/``` will show the Graphic Feature Status.

![nvidia-settings](/images/chromegpu_eng.jpg)

### Mozilla Firefox
#### Enable hardware acceleration
Open the menu in the top-right corner. From this menu, select **Preferences** and go to **General**. Scroll down to **Performance**, uncheck _"Use recommended performance settings"_ and check _"Use hardware acceleration when available"_
![firefox hardware acceleration](/images/firefox-hardware-accel.png)

#### Enable WebGL
In first place check the status of the graphic driver. In the address bar type _about:support_. In the new page, scroll down to the **Graphics** section. If WebGL is not enabled, you will see a message like this:

```bash
WebGL creation failed:
- WebglAllowWindowsNativeGl:false restricts context creation on this system.
- Exhausted GL driver options.
```
In the address bar, type _about:config_. On the new page, accept the warning button. Use the search box an find this two settings:

- **_webgl.force-enabled_**  - change from _false_ to _true_
- **_layers.acceleration.force-enabled_** - change from _false_ to _true_

Close Firefox and re-open it. Then navigate back to _about:support_ and find the Graphics section again. Now in the Graphics section you can see WebGL enabled.

![firefox WebGL](/images/firefox-webgl.png)

### Check if WebGL is available in any browser
First check if you are using accelerated graphics:

```bash
$ glxinfo | grep 'OpenGL version'
OpenGL version string: 4.6.0 NVIDIA 460.39
```
To test if the browser has WebGL enabled and working correctly you can go to these two pages.

**[https://get.webgl.org/](https://get.webgl.org/ "WebGL")** This web shows if your browser supports WebGL.
![firefox WebGL](/images/webgl-get.png)

**[https://webglreport.com](https://webglreport.com "WebGL report")** This is a web page that reports a browser's WebGL capabilities
![https://webglreport.com](/images/webgl-report.png)

## Google Chrome: Enable Memory Saver to Reduce RAM/CPU Usage
The latest versions of the Chrome browser (version 108.0.5359.124 or newer), frees up memory from inactive tabs. This gives active tabs and other apps more computer resources and keeps Chrome fast. The inactive tabs automatically become active again when you go back to them.


1. Open Chrome.
2. Access Chrome Flags with this address in the address bar:
```bash
chrome://flags/#high-efficiency-mode-available
```
3. Toggle the setting _"Enable the hight efficiency mode feature"_ to _“Enabled”_
	![settings](/images/chrome-memory-flag.jpg)
4. Restart the Chrome browser
5. Go to the address bar in Chrome and go to this address:
```bash
chrome://settings/performance
```
6. Locate “Memory Saver” and toggle the switch ON to enable the feature
![settings](/images/chrome-memory-saver.jpg)

7. Relaunch Chrome for the change to take full effect