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
two issues: different APs with same name and same AP

## ❌ OS keyring not available (kwallet not running)

## ❌ Camera not working
According to this issue it is working with gnome so it should be possible to get it working on Plasma too, but the camera has bad quality in gnome.

## ❌ onscreen touch keyboard
The default one is only appearing unreliably and appears also in unwanted cases

## ✅ Webdav (Nextcloud) shares not working in Dolphin
Dolphin is not asking for password and an account added via "online accounts" is not used. "The file or folder does not exist".

Solution: `apk add kio-kwallet`

Note: Your IP address might already be throttled/blocked because of the unauthenticated requests (no login request or empty folder shown). Try with another IP.

Note: I decided to use rclone instead as the performance with kio was bad. (rclone config as user rand entry in fstab -> mountable via dolphin)

## ❌ Boot and initial login are slow
Boot until login screen is shown takes around 2-3 minutes and login 1-2. Thats way too slow.

## ❌ login screen will never appear if user logs out (not session lock, logout)

# Minimal


## No screen rotate

## No auto brightness