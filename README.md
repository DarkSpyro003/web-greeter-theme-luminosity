# web-greeter-theme-luminosity

## Overview

Customizable LightDM Web Greeter theme based on the design of [luminos](https://github.com/muhammadsayuti/lightdm-webkit-theme-luminos) by (Muhammad Sayuti) which is based on the [official LightDM Webkit Greeter theme](https://github.com/Antergos/lightdm-webkit-theme-antergos) of [Antergos Linux](http://antergos.com)

This dark theme was built for use on Linux Workstations at the [Department of Physics ETH Zurich](https://www.phys.ethz.ch/), because there was no suitable theme available that would allow us to use it in combination with a central authentication provider like LDAP or Kerberos. Other themes present a list of users to select from, as it is done with the default LightDM configuration, which does just not scale with our currently active 1728 LDAP user accounts :)

## Credits

Many thanks to the following guys for their support on this project.

- Claude Becker ([upekkha](https://github.com/upekkha))
- Anastassios Martakos ([realmar](https://github.com/realmar))
- Christian Herzog ([daduke](https://github.com/daduke))
- Alex Myczko ([alexmyczko](https://github.com/alexmyczko))
- Janosch Bühler ([WitteShadovv](https://github.com/WitteShadovv))

## Features

- Username promt (no userlist)
- Customizable configuration (see `config.json`)
- Uses updated [JavaScript API](https://doclets.io/Antergos/web-greeter/stable) of [Web Greeter for LightDM](https://github.com/Antergos/web-greeter)
- Shows the hostname
- Shows currently active lighdm user sessions (when locked)
- Shows useful static information
- Short feedback after authenticating (success/failure)
- On screen logging (only in debug mode)
- Drop-down list for session selection
- Wallpaper changer

## Screenshots

<img src="img/Screenshot1.png" alt="screenshot1" />
<img src="img/Screenshot2.png" alt="screenshot2" />

## Prerequisites

- `lightdm`
- `lightdm-webkit2-greeter`

Installation packages of `lightdm-webkit2-greeter` for Ubuntu (and other distros) can be found [here](https://github.com/Antergos/web-greeter).

### Package installation

For example to install it from their apt repo (Also working for `Bionic`), run the following commands as `root`:

```sh
apt-key adv --fetch-keys https://download.opensuse.org/repositories/home:antergos/xUbuntu_17.10/Release.key
echo "deb http://download.opensuse.org/repositories/home:/antergos/xUbuntu_17.10/ /" > /etc/apt/sources.list.d/antergos.list
echo "deb-src http://download.opensuse.org/repositories/home:/antergos/xUbuntu_17.10/ /" >> /etc/apt/sources.list.d/antergos.list
apt update
apt install lightdm lightdm-webkit2-greeter
```

### Build instructions

You may also build it from source:

```sh
git clone https://github.com/Antergos/web-greeter.git /tmp/web-greeter
cd /tmp/web-greeter/build
git tag
# checkout the latest stable tag
git checkout 2.2.5
apt install meson gcc build-essential pkg-config libdbus-glib-1-dev liblightdm-gobject-1-dev libwebkit2gtk-4.0-dev libxml2-utils
meson --prefix=/usr --libdir=lib ..
ninja
ninja install
```

### Configuration

Enable `lightdm-webkit2-greeter` by editing `/etc/lightdm/lightdm.conf` and setting `greeter-session` property to `lightdm-webkit2-greeter`:

```
[Seat:*]
greeter-session=lightdm-webkit2-greeter
greeter-show-manual-login=true
greeter-hide-users=true
user-session=ubuntu
allow-guest=false
```

To select another screen for the lightdm greeter in a multimonitor configuration, add the following line:

```
display-setup-script=xrandr --output <id> --primary
```

while `<id>` is the screen, use `xrandr` to list your screen ids.

## Installation

Clone the theme to `/usr/share/lightdm-webkit/themes/luminosity`:

```sh
cd /usr/share/lightdm-webkit/themes
git clone https://github.com/rda0/web-greeter-theme-luminosity.git luminosity
```

To select luminosity as default theme just change the `webkit-theme` property in `/etc/lightdm/lightdm-webkit2-greeeter.conf` to `luminosity`:

```
# Example configuration

[greeter]
debug_mode          = false
detect_theme_errors = true
screensaver_timeout = 300
secure_mode         = true
time_format         = LT
time_language       = auto
webkit_theme        = luminosity
```

Now restart `lightdm`:

```
systemctl restart lightdm
```

## Configuration

The theme configuration is read from `config.json` or `config.json.local` (optional) and the background image metadata is stored in `background.json` or `background.json.local` (optional), while the respective local config file takes precedence if present.

To configure the theme make a copy of the json config:

```sh
cp config.json config.json.local
```

and edit the local json config.

## Wallpapers

To add more background wallpapers, copy the `jpg` files to `img/wallpapers` and run the script `generate-images.py`. This will generate thumbnails in `img/thumbs` and the metadata in `background.json.local`.

## Using light-locker

You may also want to install `light-locker` to replace `gnome-screensaver` to show the Web Greeter lock screen after automatic session lock.

### Installation

```sh
apt purge gnome-screensaver
apt install python-gi python3-pyside
apt install light-locker light-locker-settings
```

### Configuration

Remove the line starting with `NotShowIn=` in `light-locker.desktop` to start `light-locker` also in gnome:

```sh
sed -i '/NotShowIn=/d' /etc/xdg/autostart/light-locker.desktop
```

Disable gnome screensaver proxy:

```
cat > /etc/xdg/autostart/org.gnome.SettingsDaemon.ScreensaverProxy.desktop << EOF
[Desktop Entry]
Hidden=false
EOF
```

Now kill the screensaver (if it is running) and then restart `lightdm`.

### User configuration

The user can configure `light-locker` with the settings panel `light-locker-settings`.

To check if `light-locker` works:

```sh
$ light-locker-command -q
The screensaver is inactive  (means: it is running)
$ light-locker-command -l
# this will lock the screen
```

You may also use `loginctl lock-session` to lock your screen.

## Uninstallation

To uninstall, simply restore the `greeter-session` property of the `/etc/lightdm/lightdm.conf` file and restart your computer (or at least lightdm).

You may also want to:

- Remove the folder `luminosity` which was created in `/usr/share/lightdm-webkit/themes/`
- Restore the `webkit-theme` property of the `/etc/lightdm/lightdm-webkit-greeter.conf` file
