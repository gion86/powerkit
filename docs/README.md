# PowerKit 2.0

powerkit is an lightweight independent full featured power manager, originally created for [Slackware](http://www.slackware.com/) for use with alternative desktop environments and window managers, like  [Fluxbox](http://fluxbox.org/), [Blackbox](https://en.wikipedia.org/wiki/Blackbox), [FVWM](http://www.fvwm.org/), [WindowMaker](https://www.windowmaker.org/), [Openbox](http://openbox.org/wiki/Main_Page), [Lumina](https://lumina-desktop.org/), [Draco](https://desktop.dracolinux.org/) and others.

## Features

  * Implements [org.freedesktop.ScreenSaver](https://people.freedesktop.org/~hadess/idle-inhibition-spec/re01.html)
  * Implements [org.freedesktop.PowerManagement ](https://www.freedesktop.org/wiki/Specifications/power-management-spec/)
 * Automatically suspend on idle
 * Automatically lock screen on idle
 * Automatically hibernate or shutdown on critical battery
 * Inhibit actions if external monitor(s) is connected
 * Enables applications to inhibit display and suspend
 * Advanced settings
 * Screen locking support
 * Laptop/Portable support
 * Display backlight support
 * Display hotplug support
 * RTC alarm support
 * CPU frequency scaling support


## Usage

powerkit is an daemon and should be started during the X11 startup session. In Lumina and Draco powerkit should automatically start, on other configurations you will need to add powerkit to your startup file (check the documentation included with your desktop environment or window manager).
**Do not run powerkit if your desktop environment already has power management support**.

 * In Fluxbox add ``powerkit &`` to the ``~/.fluxbox/startup`` file
 * In Openbox add ``powerkit &`` to the ``~/.config/openbox/autostart`` file.

## Configuration

Click on the powerkit system tray, or run the command ``` powerkit-conf``` to configure powerkit.

### Screen saver

powerkit depends on [XScreenSaver](https://www.jwz.org/xscreensaver/) to handle the screen locking feature, the default ([XScreenSaver](https://www.jwz.org/xscreensaver/)) settings may need to be adjusted. You can launch the ([XScreenSaver](https://www.jwz.org/xscreensaver/)) configuration GUI with the ``xscreensaver-demo`` command.

Recommended settings are:

* Mode: ``Blank Screen Only``
* Blank After: ``5 minutes``
* Lock Screen After: ``enabled + 0 minutes``
* Display Power Management: ``enabled``
  * Standby After: ``0 minutes``
  * Suspend After: ``0 minutes``
  * Off After: ``0 minutes``
  * Quick Power-off in Blank Only Mode: ``enabled``

Note that powerkit will start [XScreenSaver](https://www.jwz.org/xscreensaver/) during startup (unless [org.freedesktop.ScreenSaver](https://people.freedesktop.org/~hadess/idle-inhibition-spec/re01.html) is disabled).

### Backlight

The current display brightness (on laptops) can be adjusted with the mouse wheel on the system tray icon or through the system tray menu.

### Hibernate

If this "just works" depends on your system, worst case a swap partition (or file) is needed by the kernel to support hibernate, just add ``resume=<swap_partition>`` to the kernel commandline in the bootloader.
**Consult your system documentation regarding hibernation**.

## FAQ

### Slackware-only?

No, powerkit should work on any Linux system. However, powerkit is developed on/for [Slackware](http://www.slackware.com/) and sees minimal testing on other systems (user feedback/bugs for other systems are welcome).

### How does an application inhibit the screen saver?

The preferred way to inhibit the screen saver from an application is to use the [org.freedesktop.ScreenSaver](https://people.freedesktop.org/~hadess/idle-inhibition-spec/re01.html) specification. Any application that uses [org.freedesktop.ScreenSaver](https://people.freedesktop.org/~hadess/idle-inhibition-spec/re01.html) will work with powerkit. Note that powerkit also includes ``SimulateUserActivity`` for backwards compatibility.

Popular applications that uses this feature is Mozilla Firefox/Google Chrome (for audio/video), VideoLAN VLC and many more.

### How does an application inhibit suspend actions?

The prefered way to inhibit suspend actions from an application is to use the [org.freedesktop.PowerManagement](https://www.freedesktop.org/wiki/Specifications/power-management-spec/) specification. Any application that uses [org.freedesktop.PowerManagement](https://www.freedesktop.org/wiki/Specifications/power-management-spec/) will work with powerkit.

Common use cases are audio playback, downloading and more.

### Google Chrome/Chromium does not inhibit the screen saver!?

[Chrome](https://chrome.google.com) does not use [org.freedesktop.ScreenSaver](https://people.freedesktop.org/~hadess/idle-inhibition-spec/re01.html) until it detects KDE/Xfce. Add the following to ``~/.bashrc`` or the ``google-chrome`` launcher if you don't run a desktop environment:

```
export DESKTOP_SESSION=xfce
export XDG_CURRENT_DESKTOP=xfce
```

## Requirements

powerkit requires the following dependencies to work:

 * [X11](https://www.x.org)
 * [Xss](https://www.x.org/archive//X11R7.7/doc/man/man3/Xss.3.xhtml)
 * [Xrandr](https://www.x.org/wiki/libraries/libxrandr/)
 * [QtDBus](https://qt.io) 4.8+
 * [QtGui](https://qt.io) 4.8+
 * [QtCore](https://qt.io) 4.8+
 * [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/)
 * [ConsoleKit](https://www.freedesktop.org/wiki/Software/ConsoleKit/) or [logind](https://www.freedesktop.org/wiki/Software/systemd/logind/) (will work without, but with limited functions)
 * [UPower](https://upower.freedesktop.org/) 0.9.23(+)
 * [XScreenSaver](https://www.jwz.org/xscreensaver/)
 * [adwaita-icon-theme](https://github.com/GNOME/adwaita-icon-theme)

### Icons

powerkit will use the existing icon theme from the running DE/WM, else check the GTK settings then fallback to Adwaita if no theme was found. You should have (a proper version) of Adwaita installed as a fallback.

You can override the icon theme in the `~/.config/powerkit/powerkit.conf` file, see ``icon_theme=<theme_name>``.

## Build

First make sure you have the required dependencies installed, then review the build options:

 * ``CMAKE_INSTALL_PREFIX=</usr/local>`` - Install target
 * ``CMAKE_BUILD_TYPE=<Release/Debug>`` - Build type
 * ``USE_QT5=<ON>`` - Build against Qt5 (disable if you want Qt4)
 * ``POWERKITD_USER=<root>`` - Run powerkitd as user with access to /sys
 * ``POWERKITD_GROUP=<users>`` - Group that have access to the powerkitd service, this should be any desktop user

```
cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release ..
make
make DESTDIR=<package> install
```