The official postmarketOS wiki entry is outdated, the linked pull request is also stale. Development is currently happening [here](https://github.com/pipa-project) and [here](https://gitlab.com/pipa-mainline).

## Setup Steps

Ready-to-use builds are available from [https://github.com/pipa-project/postmarketos-builds](https://github.com/pipa-project/postmarketos-builds). Go to Actions and download the build.

Flash it with fastboot (this will wipe all data):
- `fastboot erase dtbo_a`
- `fastboot flash boot_a <file>`
- `fastboot flash userdata <file>`
- `fastboot set_active a`

> [!WARNING]
> These builds do not use Full Disk Encryption (FDE) and no systemd>

## Post-Installation
These are the general config changes i did

### adjust zram config
[official documentation](https://wiki.alpinelinux.org/wiki/Zram)

increase zram size to 8GB: `nano /etc/conf.d/zram-init` and set `size0=8192`

optionally choose a higher swappiness: `sysctl vm.swappiness=80` and add `vm.swappiness=80` to `/etc/sysctl.conf`. With the default of 60 i had experienced OOM kills while there was still free swap available.