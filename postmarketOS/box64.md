create with init

first start: if too fast or something seems off : check internet connection

ggf modprobe binfmt_misc

sudo systemctl unmask systemd-binfmt
sudo mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc

# configure distrobox to run steam

Create Distro: 
`distrobox create --image ubuntu:latest --root --name steam`

Connect to Distro: `distrobox enter --root steam`

Script to install all dependencies and steam:
```bash
# ============================================
# Initial Setup - Multiarch and binfmt
# ============================================

# Enable 32-bit ARM architecture support
sudo dpkg --add-architecture armhf

# Setup binfmt_misc for binary format handling
sudo mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc

# ============================================
# Install box86 and box64
# Source: https://github.com/ryanfortner/box64-debs
# ============================================

# Add box86 repository
sudo wget https://ryanfortner.github.io/box86-debs/box86.list -O /etc/apt/sources.list.d/box86.list
sudo wget -qO- https://ryanfortner.github.io/box86-debs/KEY.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/box86-debs-archive-keyring.gpg

# Add box64 repository
sudo wget https://ryanfortner.github.io/box64-debs/box64.list -O /etc/apt/sources.list.d/box64.list
sudo wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/box64-debs-archive-keyring.gpg

sudo apt update

# Install box86 and box64
sudo apt install box86-sd845 box64 -y

# ============================================
# Install Required System Libraries
# ============================================

# Install utility tools (required by Steam)
# Note: pciutils (lspci), zenity, and libxss1 are needed based on Steam logs
sudo apt install -y \
    python3 \
    python3-apt \
    binutils \
    wget \
    pciutils \
    zenity \
    libxss1 \
    libcurl3-gnutls

# Install 32-bit ARM (armhf) libraries for Steam, based on dependency lists from postmarketOS guide and box64 steam script (from either box86 or box64, not sure)
sudo apt install -y \
    libc6:armhf \
    libsdl2-2.0-0:armhf \
    libsdl2-image-2.0-0:armhf \
    libsdl2-mixer-2.0-0:armhf \
    libsdl2-ttf-2.0-0:armhf \
    libopenal1:armhf \
    libpng16-16t64:armhf \
    libfontconfig1:armhf \
    libxcomposite1:armhf \
    libbz2-1.0:armhf \
    libxtst6:armhf \
    libsm6:armhf \
    libice6:armhf \
    libgl1:armhf \
    libxinerama1:armhf \
    libxdamage1:armhf \
    libibus-1.0-5 \
    libgl1-mesa-dri:armhf \
    libglu1-mesa:armhf \
    libvulkan1:armhf

# Install 64-bit ARM (arm64) libraries, based on steams complain during startup with python3-apt
sudo apt install -y \
    libc6:arm64 \
    libegl1:arm64 \
    libgbm1:arm64 \
    libgl1-mesa-dri:arm64 \
    libgl1:arm64 \
    libsdl2-2.0-0 \
    xdg-desktop-portal \
    xdg-desktop-portal-kde

# Install libraries required by steamwebhelper
# Note: These are critical for Steam's web interface (store, friends, etc.)
sudo apt install -y \
    libnss3 \
    libnspr4 \
    libva2 \
    libva-drm2


# ============================================
# Install Steam (Manual Method)
# Source: https://github.com/ptitSeb/box64/blob/main/install_steam.sh
# ============================================

# Create necessary directories
mkdir -p ~/steam
mkdir -p ~/steam/tmp
cd ~/steam/tmp

# Download latest Steam deb and unpack
wget https://cdn.cloudflare.steamstatic.com/client/installer/steam.deb
ar x steam.deb
tar xf data.tar.xz

# Remove deb archives (not needed anymore)
rm ./*.tar.xz ./steam.deb

# Move deb contents to steam folder
mv ./usr/* ../
cd ../ && rm -rf ./tmp/

# Create Steam launch script
cat > steam << 'EOF'
#!/bin/bash
export STEAMOS=1
export STEAM_RUNTIME=1
export PROTON_USE_WOW64=1
export DBUS_FATAL_WARNINGS=0
~/steam/bin/steam $@
EOF

# Make script executable and install system-wide
chmod +x steam
sudo mv steam /usr/local/bin/

echo "Setup complete. Run 'steam' to launch Steam."
```

## Launching steam
To launch steam, just run `distrobox enter --root steam -- steam`.




## wip rootless
```
echo "user:100000:65536" | sudo tee -a /etc/subuid /etc/subgid
podman system migrate
```
next issue is with cgroups. This issue is likely at least partially fixed whith systemd. So i wont pursue this further for now.