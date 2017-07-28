# Dotfiles-and-Configs
Various dotfiles and other documentation for my Arch Linux Installation(s)

## Descriptions

- `.Xmodmap` ... Contains keyboard mappings which enable umlaut keys when pressing capslock + specific key (capslock + a = ä)
- `.aliases` ... Shell aliases
- `install.sh` ... Executes all necessary scripts and creates all symlinks in order to properly set up a machine with these files
- `maxima_tempfiles.sh` ... Adds maxima config file so that it stops leaving files in the home directory
- `fix_unknown_media_type.sh` ... Fixes an annoying error that stems from KDE Cachegrind and it's dependencies and always prints messages when using pacman.
- `font_fontrendering.sh` ... Enables smooth font rendering.
- `htmlWatchdog.sh` ... Takes a given url and downloads the html page. It then saves the page and creates a checksum which can be compared at later stages to check if the site has changed. Is primarily used in an automated Jenkins Job which checks semi-dead sites for updates.

## Notes

### General

- After adding a user to a new group simply execute `newgrp <group name>` to update the entry.
- Don't forget to enable the following options in your `/etc/pacman.conf`: `Color`, `ILoveCandy`, `VerbosePkgLists`
- Add `Defaults insults` to the `/etc/sudoers` file for a more interesting administration experience! Use `visudo` to edit this file safely.

### Nvidia/Xorg/Tearing

[This tutorial](https://www.gloriouseggroll.tv/2016-arch-linux-nvidia-get-rid-of-screen-tearing-and-stuttering/) shows how to get rid of tearing under Arch when using the proprietary Nvidia driver. Here is a quick summary of the process:

- Generate an `xorg.conf` file with the `nvidia-xconfig` command.
- Append the following two lines to the `Device` section of the `/etc/X11/xorg.conf` file.
```
Option         "RegistryDwords" "PerfLevelSrc=0x3322; PowerMizerDefaultAC=0x1"
Option         "TripleBuffer" "True"
```
- Remove any mentions of `RegistryDwords` or `TripleBuffer` from the `Screen` section in case there are any.
- Append the following line to the `Screen` section.
```
Option "metamodes" "nvidia-auto-select +0+0 { ForceFullCompositionPipeline = On }"
```
- Add this option to `/etc/profile.d/profile.sh` (the tutorial has a typo in this instruction!).
```
export __GL_THREADED_OPTIMIZATIONS=1
```
- In the `nvidia-settings` in the OpenGL section enable `Sync to VBlank` and `Allow Flipping`. In the `XServer XVideo Settings` use the mode `Auto`.
- Install the package `compton` and create config with the [following content](https://github.com/jjungreithmeir-tgm/Dotfiles-and-Configs/blob/master/resources/compton.conf) in `~/.config/compton.conf`
- Add compton to the auto-startup manager of your choice (XFCE4-autostart in my case) via
```
compton --config ~/.config/compton.conf -b
```

If you have multiple monitors then you also need to execute the following line in order for the `ForceFullComposition` to be applied on both devices:

```
nvidia-settings --assign CurrentMetaMode="$(xrandr | sed -nr '/(\S+) connected (primary )?[0-9]+x[0-9]+(\+\S+).*/{ s//\1: nvidia-auto-select \3 { ForceFullCompositionPipeline = On }, /; H }; ${ g; s/\n//g; s/, $//; p }')"
```

### GPG

#### export private key
`gpg -ao <file name> --export-secret-key "email address"`

#### import private key
`gpg --import <file name>`

### Symlinks

Always use absolute paths!

`ln -s <source> <target>`

### CUPS

Add your user to the `lp` and the `sys` group otherwise you will encounter an authentication problem when accessing the web interface via `localhost:631` or `127.0.0.1:631`.

If your printers are not showing up in some applications (e.g. evince, firefox) then simply install `gtk3-print-backends`.

### Teamspeak 3

Essential files which should be included in a backup:

- `ts3server.sqlitedb` - contains channels, settings, user, permissions
- `ts3server.ini`
- `query_ip_whitelist.txt`
- `query_ip_blacklist.txt`
- `files/internal` - all the icon logos, etc.

Teamspeak also offers the possibility to [format text with BB codes](http://forum.teamspeak.com/misc.php?do=bbcode).

### Gaming

#### Steam

If all source games fail to properly update and/or download with the error message `corrupt update files` and the steam folder lies on an NTFS formatted drive then you need to change the mounting options in `/etc/fstab` to `defaults,exec,uid=1000,gid=1000`.

To check if any libraries for steam are missing simply execute:

```
cd ~/.local/share/Steam/ubuntu12_32

LD_LIBRARY_PATH=".:${LD_LIBRARY_PATH}" ldd $(file *|sed '/ELF/!d;s/:.*//g')|grep 'not found'|sort|uniq
```

#### Minecraft

To install shaderpacks with the Feed the Beast Launcher, simply download the Shader Core Mod and place it in the appropriate `.../minecraft/mods/` folder. Then startup the game, it automatically creates a `shaderpacks` folder in the `minecraft` folder. There you have to put your shaderpacks, this can be even done at runtime.

If you get an error saying `[Shaders] Error : Invalid program composite1` then you need to manually modify the shader code as it seems to contain an error. Just edit the contents of the zip archive with the editor of your choice. Find the following line in the `shaders/composite1.fsh` file, in my case it was line 959.

```
vec4 	ComputeFakeSkyReflection(in SurfaceStruct surface) {
```

There you need to change the type of the parameter from `in` to `inout`.

#### Feral Interactive Ports

- https://forum.manjaro.org/t/problems-fixes-for-steam-apps-and-new-openssl-currently-in-testing-only/23214/3

## Useful links

### Latex

- [Bibtex entries for RFC specifications](http://notesofaprogrammer.blogspot.co.at/2014/11/bibtex-entries-for-ietf-rfcs-and.html)

## Contributions

### AUR packages

- [texlive-tikz-uml](https://aur.archlinux.org/packages/texlive-tikz-uml/)
- [texlive-coffee-stains](https://aur.archlinux.org/packages/texlive-coffee-stains/)
- [ttf-impallari-dosis](https://aur.archlinux.org/packages/ttf-impallari-dosis/)
