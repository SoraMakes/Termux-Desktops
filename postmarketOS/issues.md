This is only about Plasma

Issues have one of these states
 ❌ Unsolved
 ⚠️ workaround
 ✅ fix


# Critical 



# Relevant

## ⚠️ glibc applications
Issue: Alpine uses Musl as c library. Most applications not available in the alpine repos will not run

Workaround: There are many workarounds available. 
- Flatpak: This is already integrated in alpine. All applications there should work (as long as they have arm builds)
- use compatibility layer: install the package gcompat. Optionally also libstdc++, libc6-compat, patchelf. I did not explore this way too much. Was not successfull this way.
- use a glibc chroot. See [Chroot and Containers sections in the alpine linux documentation](https://wiki.alpinelinux.org/wiki/Software_management). See [box64.md](./box64.md) file. Distrobox does not yet work without root (lots of password prompts)

## ✅ Flatpak not working
Initially there are errors when trying to install flatpaks from flathub.

Solution: 
1) Check system time
2) readd flathub repo: `flatpak remote-delete --force flathub && flatpak remote-add flathub https://flathub.org/repo/flathub.flatpakrepo`

## ⚠️ Wifi no auto connect
two issues: different APs with same name

## ❌ OS keyring not available (kwallet not running)

## ⚠️ Camera not working
Back Camera works (at least with some applications) but has bad quality. Front Camera (selfi cam) does not work.

## ✅ Webdav (Nextcloud) shares not working in Dolphin
Dolphin is not asking for password and an account added via "online accounts" is not used. "The file or folder does not exist".

Solution:
A package `kio-kwallet` was missing. It is already added in my custom build.

Note: I decided to use rclone instead as the performance with kio was bad. (rclone config as user and entry in fstab -> mountable via dolphin)

## ❌ Boot and initial login are slow
Boot until login screen is shown takes around 2-3 minutes and login 1-2. Thats way too slow.

## ❌ login screen will never appear if user logs out (not session lock, logout)

## ✅ external display does not list the correct resolution
I could not select 1920x1080 for my FHD display.

Solution: install xrandr (then reconnect display once)

## ✅ ssh agent (with keypassxc)
SSH Agent did not work here as I am used to. `eval $(ssh-agent -s)` runs the agent only for one terminal session without connection to other sessions. Just using `keychain` also did not fully work for me (not exactly sure anymore what the issue was, i think keepassxc was not working).

Solution:
1) `apk add bash`
2) change shell of the user to `/bin/bash` in `/etc/passwd`
3) add this to `$HOME/.profile`
```bash
eval $(keychain --eval)
```
4) reboot

## ✅ Printers not working
- add user to lpadmin group: `usermod -aG lpadmin user` -> reboot / log out and in again to apply this change
- enable / start cupsd service: `systemd enable --now cups`

>[NOTICE]
> Alpine docs tells to also do this:
> To manage printers from KDE Plasma Settings, it is required to add "root" to SystemGroup in /etc/cups/cups-files.conf.
> e.g. `SystemGroup root lpadmin`

The postmarketOS image was missing some dependencies (`cups cups-libs cups-pdf cups-client cups-filters hplip`). I added them in my custom build.

I did not find a driver for my printer, but it still worked adding it manually as an ipp printer.
- enter CUPS in Browser (http://localhost:631) and select "Administration" -> "Add printer" 
- provide information for "Internet Printing" (ipp) like printer IP make, model, etc
- add printer

For another (USB Samsung laser printer) i used the generic laser printer driver.

# Minimal


## No screen rotate

## No auto brightness