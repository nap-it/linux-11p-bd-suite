# Raspberry Pi 5 Tutorial

## Equipment
The following HATs have been tested and verified so far:
| Product Name     | Product Page / Retail Listing | Product Photo |
|------------------|-------------------------------|---------------|
| 52Pi M02 PCIe Express To Mini PCIe HAT For Raspberry Pi 5 | https://52pi.com/products/52pi-mini-pcie-hat-m02-for-rpi-5 |![52Pi M02 PCIe Express To Mini PCIe HAT For Raspberry Pi 5](/images/52Pi%20M02.JPEG)|
| Pineboards BMPC1L Hat mPCIe for Raspberry Pi 5 | https://www.welectron.com/Pineboards-BMPC1L-Hat-mPCIe-for-Raspberry-Pi-5|![Pineboards BMPC1L Hat mPCIe for Raspberry Pi 5](/images/Pineboards%20BMPC1L.JPEG)|

The following wireless network adapters have been tested and verified to work correctly:
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|Compex WLE200NX 7A |AR9280 |ath9k |None|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE200NX 7A](/images/WLE200NX%207A.JPEG)|
|Compex WLE900VX 7CA|QCA9890|ath10k|None|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-i-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7CA](/images/WLE900VX%207CA.JPEG)|

The following wireless network adapters have been tested but have limited or no functionality (see notes):
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|MikroTik R11e-5HnD |AR9580 |ath9k |Extremely low and inconsistent TX power (most likely related to adapter/slot power delivery)|https://www.mikrotik-store.eu/en/mikrotik-r11e-5hnd|![MikroTik R11e-5HnD](/images/R11e-5HnD.JPEG)|
|MikroTik R11e-5HacT|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX). Likely to suffer from the same TX power issues as its predecessor|https://mikrotik.com/product/R11e-5HacT|![MikroTik R11e-5HacT](/images/R11e-5HacT.JPEG)|
|Compex WLE900VX 7AA|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE900VX 7AA](/images/WLE900VX%207AA.JPEG)|

## Instructions

The following instructions are targetted toward recent Raspberry Pi OS versions (based on Debian 13). These instructions assume that Raspberry Pi OS has already been installed. While cross-compilation from a x86 host is possible, this guide assumes that all commands are executed directly on the target device. You can find the official Raspberry Pi kernel compilation guide [here](https://www.raspberrypi.com/documentation/computers/linux_kernel.html). Additionally, this guide assumes that you have cloned this repository into your home directory on the target device.

1. Power off the Raspberry Pi, then assemble the HAT following its instruction manual together with your chosen wireless adapter.
2. Turn on the Raspberry Pi and access its terminal via SSH or the built-in desktop environment.
3. Modify your APT sources so you can fetch package sources. You can do this by duplicating the entries in the `/etc/apt/sources.list.d/debian.sources` file, changing the type to `deb-src`, and then updating the apt package lists.
    ````
    sudo nano /etc/apt/sources.list.d/debian.sources 
    sudo apt update
    ````

    Your file should look something like this before running `apt update`:
    ````
    Types: deb
    URIs: http://deb.debian.org/debian/
    Suites: trixie trixie-updates
    Components: main contrib non-free non-free-firmware
    Signed-By: /usr/share/keyrings/debian-archive-keyring.pgp

    Types: deb-src
    URIs: http://deb.debian.org/debian/
    Suites: trixie trixie-updates
    Components: main contrib non-free non-free-firmware
    Signed-By: /usr/share/keyrings/debian-archive-keyring.pgp

    Types: deb
    URIs: http://deb.debian.org/debian-security/
    Suites: trixie-security
    Components: main contrib non-free non-free-firmware
    Signed-By: /usr/share/keyrings/debian-archive-keyring.pgp

    Types: deb-src
    URIs: http://deb.debian.org/debian-security/
    Suites: trixie-security
    Components: main contrib non-free non-free-firmware
    Signed-By: /usr/share/keyrings/debian-archive-keyring.pgp
    ````
4. Modify the wireless regulatory database to allow ITS-G5 channels.
    1. Fetch sources for the wireless regulatory database (note that your folder may have a slightly different name)
        ````
        apt source wireless-regdb
        cd wireless-regdb-2026.02.04
        ````
    2. Install required build dependencies
        ````
        sudo apt install build-essential python-is-python3 xxd python3-m2crypto -y
        ````
    3. Edit the `db.txt` file
        1. Find the regulatory listing that corresponds to your locale (eg: "DE: DFS-ETSI" for Germany, "PT: DFS-ETSI" for Portugal) and make sure your local regulations allow ITS-G5 operation in the 5850 - 5925 MHz band.
        2. Add the following lines to the listing:
            ````
            # ITS-G5
            (5850 - 5925 @ 20), (400 mW), wmmrule=ETSI
            ````
            Your entry should look something like this:
            ````
            country PT: DFS-ETSI
                (2400 - 2483.5 @ 40), (100 mW)
                (5150 - 5250 @ 80), (200 mW), NO-OUTDOOR, AUTO-BW, wmmrule=ETSI
                (5250 - 5350 @ 80), (100 mW), NO-OUTDOOR, DFS, AUTO-BW, wmmrule=ETSI
                (5470 - 5725 @ 160), (500 mW), DFS, wmmrule=ETSI
                # short range devices (ETSI EN 300 440-1)
                (5725 - 5875 @ 80), (25 mW)
                # ITS-G5
                (5850 - 5925 @ 20), (400 mW), wmmrule=ETSI
                # 6 GHz band
                (5945 - 6425 @ 320), (23), NO-OUTDOOR, wmmrule=ETSI
                # 60 GHz band channels 1-4 (ETSI EN 302 567)
                (57000 - 66000 @ 2160), (40)
            ````
    4. Build and install
        ````
        make ~/.wireless-regdb-$(whoami).key.priv.pem
        make
        sudo make install
        ````
    5. Get the key used to sign your regulatory database (auto generated on build-time) and place it on your home directory (you will need this later when you build the kernel).
        ````
        openssl x509 -outform der -in $(whoami).x509.pem -out certificate.der
        xxd -i certificate.der > certificate.hex
        sed '1d' certificate.hex | head -n -2 | sed '$ s/$/,/' > ~/$(whoami).hex
        rm certificate.der certificate.hex
        ````
5. Build a customized version of the kernel
    1. Install the required build dependencies
        ````
        sudo apt install git bc bison flex libssl-dev make libncurses-dev -y
        ````
    2. Get and unpack the kernel sources
        ````
        cd ~/
        git clone --branch rpi-6.12.y https://github.com/raspberrypi/linux rpi-linux
        ````
    3. Patch the source code
        ````
        cd ~/rpi-linux
        patch -p1 < ~/linux-11p-bd-suite/Raspberry\ Pi/Pi\ 5/patches/linux-6.12.x.patch
        ````
    4. Copy the certificated used to sign the wireless regulatory database into the kernel's wireless certificate datastore
        ````
        cp ~/$(whoami).hex ~/rpi-linux/net/wireless/certs/$(whoami).hex
        ````
    5. Get the Candela Technologies ath10k firmware (optional)
        ````
        cd /lib/firmware/ath10k/QCA988X/hw2.0/
        sudo wget https://www.candelatech.com/downloads/ath10k-fw-beta/firmware-2-ct-htt-mgt-community.bin
        ````
    6. Modify the default kernel config
        1. Include the wireless regulatory database into the kernel build. This is optional but it ensures that future updates can not override your custom regulatory database. Additionally, if you wish to reuse your kernel build in other boards, you do not need to repeat the database build process or copy the resulting files.
            ````
            cd ~/rpi-linux
            make bcm2712_defconfig
            scripts/config --set-str CONFIG_EXTRA_FIRMWARE "regulatory.db regulatory.db.p7s"
            scripts/config --set-str CONFIG_EXTRA_FIRMWARE_DIR "/lib/firmware"
            ````
        2. If using an ath10k device, you can also include the custom firmware into the kernel build
            ````
            scripts/config --set-str CONFIG_EXTRA_FIRMWARE "regulatory.db regulatory.db.p7s ath10k/QCA988X/hw2.0/firmware-2-ct-htt-mgt-community.bin"
            ````
        3. Enable `ath9k` and `ath10k` to be built as modules as well as enable their debug and tracing features.
            ````
            scripts/config --enable CONFIG_CFG80211_DEBUGFS
            scripts/config --enable CONFIG_MAC80211_DEBUGFS
            scripts/config --module CONFIG_ATH9K_HW
            scripts/config --module CONFIG_ATH9K_COMMON
            scripts/config --enable CONFIG_ATH9K_COMMON_DEBUG
            scripts/config --enable CONFIG_ATH9K_BTCOEX_SUPPORT
            scripts/config --module CONFIG_ATH9K
            scripts/config --enable CONFIG_ATH9K_PCI
            scripts/config --enable CONFIG_ATH9K_AHB
            scripts/config --enable CONFIG_ATH9K_DEBUGFS
            scripts/config --enable CONFIG_ATH9K_STATION_STATISTICS
            scripts/config --enable CONFIG_ATH9K_RFKILL
            scripts/config --enable CONFIG_ATH9K_PCOEM
            scripts/config --module CONFIG_ATH9K_PCI_NO_EEPROM
            scripts/config --enable CONFIG_ATH9K_COMMON_SPECTRAL

            scripts/config --module CONFIG_ATH10K
            scripts/config --enable CONFIG_ATH10K_CE
            scripts/config --module CONFIG_ATH10K_PCI
            scripts/config --enable CONFIG_ATH10K_AHB
            scripts/config --module CONFIG_ATH10K_SDIO
            scripts/config --module CONFIG_ATH10K_USB
            scripts/config --module CONFIG_ATH10K_SNOC
            scripts/config --enable CONFIG_ATH10K_DEBUG
            scripts/config --enable CONFIG_ATH10K_DEBUGFS
            scripts/config --enable CONFIG_ATH10K_SPECTRAL
            scripts/config --enable CONFIG_ATH10K_TRACING
            ````
    7. Build the kernel. We use a custom suffix `-nap` in the version to keep our kernel completely separate from the default one, along with a timestamp for easier version control.
        ````
        make olddefconfig
        make -j$(nproc) EXTRAVERSION=-nap LOCALVERSION=-$(date +%s) Image.gz modules dtbs
        ````
    8. Install the kernel in a dedicated directory to ensure that system updates do not interfere with it.
        ````
        sudo mkdir -p /boot/firmware/nap/overlays
        KERNEL=kernel_2712
        sudo make modules_install
        sudo make headers_install
        sudo cp arch/arm64/boot/Image.gz /boot/firmware/nap/$KERNEL-nap.img
        sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/nap/
        sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/firmware/nap/overlays/
        sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/nap/overlays/
        ````
    9. Edit `/boot/firmware/config.txt` and add the following lines at the bottom:
        ````
        os_prefix=nap/
        kernel=kernel_2712-nap.img
        dtparam=pciex1
        dtparam=pciex1_gen=3
        dtoverlay=pcie-32bit-dma
        ````
    10. Copy your original `cmdline.txt` file to your new custom folder:
        ````
        sudo cp /boot/firmware/cmdline.txt /boot/firmware/nap/cmdline.txt
        ````
    11. Reboot
        ````
        sudo reboot
        ````
6. Setup and initialize the interface
    1. Set your regulatory domain. Example for Portugal:
        ````
        sudo raspi-config nonint do_wifi_country PT
        ````
    2. Make `network-manager` ignore the interface:
        ````
        sudo bash -c 'echo -e "[keyfile]\nunmanaged-devices=interface-name:wlan1" > /etc/NetworkManager/conf.d/10-ignore-wlan1.conf && systemctl restart NetworkManager'
        ````
    3. If using an `ath10k` device:
        1. Get the PCI address of your wireless card:
            ````
            lspci
            ````
            Your output should look something like this:
            ````
            0001:00:00.0 PCI bridge: Broadcom Inc. and subsidiaries BCM2712 PCIe Bridge (rev 30)
            0001:01:00.0 Network controller: Qualcomm Atheros QCA986x/988x 802.11ac Wireless Network Adapter
            0002:00:00.0 PCI bridge: Broadcom Inc. and subsidiaries BCM2712 PCIe Bridge (rev 30)
            0002:01:00.0 Ethernet controller: Raspberry Pi Ltd RP1 PCIe 2.0 South Bridge
            ````
            So in this case, the address of the card is `0001:01:00.0`.
        2. Create a config file like at `/lib/firmware/ath10k/fwcfg-pci-<your-pci-address>.txt`. Ex: `/lib/firmware/ath10k/fwcfg-pci-0001\:01\:00.0.txt`

            Place the following contents in the file (more info [here](https://www.candelatech.com/ath10k-10.4.php#config)):
            ````
            fwname = firmware-2-ct-htt-mgt-community.bin
            fwver = 2
            max_nss = 1
            ````
    4. Reboot
        ````
        sudo reboot
        ````
    5. Ensure the correct regulatory domain has been applied:
        ````
        sudo iw reg get
        ````

        Your output should look something like this:
        ````
        global
        country PT: DFS-ETSI
            (2400 - 2483 @ 40), (N/A, 20), (N/A)
            (5150 - 5250 @ 80), (N/A, 23), (N/A), NO-OUTDOOR, AUTO-BW
            (5250 - 5350 @ 80), (N/A, 20), (0 ms), NO-OUTDOOR, DFS, AUTO-BW
            (5470 - 5725 @ 160), (N/A, 26), (0 ms), DFS
            (5725 - 5875 @ 80), (N/A, 13), (N/A)
            (5850 - 5925 @ 20), (N/A, 26), (N/A)
            (5945 - 6425 @ 320), (N/A, 23), (N/A), NO-OUTDOOR
            (57000 - 66000 @ 2160), (N/A, 40), (N/A)

        phy#0
        country PT: DFS-ETSI
            (2400 - 2483 @ 40), (N/A, 20), (N/A)
            (5150 - 5250 @ 80), (N/A, 23), (N/A), NO-OUTDOOR, AUTO-BW
            (5250 - 5350 @ 80), (N/A, 20), (0 ms), NO-OUTDOOR, DFS, AUTO-BW
            (5470 - 5725 @ 160), (N/A, 26), (0 ms), DFS
            (5725 - 5875 @ 80), (N/A, 13), (N/A)
            (5850 - 5925 @ 20), (N/A, 26), (N/A)
            (5945 - 6425 @ 320), (N/A, 23), (N/A), NO-OUTDOOR
            (57000 - 66000 @ 2160), (N/A, 40), (N/A)
        ````
    6. Activate the interface:
        1. ath9k
            ````
            sudo iw wlan1 set type ocb
            sudo ip l set dev wlan1 up
            sudo iw wlan1 ocb join 5900 10MHz
            ````
        2. ath10k
            ````
            phy=$(cat /sys/class/net/wlan1/phy80211/name)
            echo "10" | sudo tee /sys/kernel/debug/ieee80211/$phy/ath10k/chan_bw
            sudo iw wlan1 set type ocb
            sudo ip l set dev wlan1 up
            sudo iw wlan1 ocb join 5900 10MHz
            ````

### Example kernel logs
#### ath9k
````
[    6.573374] ath9k 0001:01:00.0: enabling device (0000 -> 0002)
[    7.013597] ath: EEPROM regdomain: 0x0
[    7.013607] ath: EEPROM indicates default country code should be used
[    7.013612] ath: Country alpha2 being used: 00
[    7.013613] ath: Regpair used: 0x64
````

#### ath10k
````
[    5.384999] ath10k 6.12 driver, optimized for CT firmware, with IEEE 802.11p support, probing pci device: 0x3c.
[    5.385742] ath10k_pci 0001:01:00.0: enabling device (0000 -> 0002)
[    5.385939] ath10k_pci 0001:01:00.0: pci irq msi oper_irq_mode 2 irq_mode 0 reset_mode 0
[    5.524988] ath10k_pci 0001:01:00.0: fwcfg key: fwname  val: firmware-2-ct-htt-mgt-community.bin
[    5.524996] ath10k_pci 0001:01:00.0: fwcfg key: fwver  val: 2
[    5.524998] ath10k_pci 0001:01:00.0: fwcfg key: max_nss  val: 1
[    5.528053] ath10k_pci 0001:01:00.0: qca988x hw2.0 target 0x4100016c chip_id 0x043202ff sub 0000:0000
[    5.528058] ath10k_pci 0001:01:00.0: kconfig debug 1 debugfs 1 tracing 1 dfs 0 testmode 0
[    5.528079] ath10k_pci 0001:01:00.0: firmware ver 10.1-ct-8x-__tH-023-23ea9f8e api 2 features wmi-10.x,mfp,peer-fixed-rate,txstatus-noack,wmi-10.x-CT,ratemask-CT,txrate-CT,no-bmiss-CT,get-temp-CT,tx-rc-CT,cust-stats-CT,retry-gt2-CT,txrate2-CT,beacon-cb-CT,wmi-block-ack-CT crc32 8665d010
[    5.558709] ath10k_pci 0001:01:00.0: board_file api 1 bmi_id N/A crc32 bebc7c08
[    6.367206] ath10k_pci 0001:01:00.0: hardware advertises support for more spatial streams than it should (3 > 1)
[    6.367330] ath10k_pci 0001:01:00.0: 10.1 wmi init: vdevs: 16  peers: 127  tid: 256
[    6.376600] ath10k_pci 0001:01:00.0: wmi print 'P 128 V 8 T 410'
[    6.376631] ath10k_pci 0001:01:00.0: wmi print 'msdu-desc: 1424  sw-crypt: 0 ct-sta: 0'
[    6.377198] ath10k_pci 0001:01:00.0: wmi print 'alloc rem: 26712 iram: 54568'
[    7.366597] ath10k_pci 0001:01:00.0: NOTE:  Firmware DBGLOG output disabled in debug_mask: 0x10000000
[    7.404900] ath10k_pci 0001:01:00.0: htt-ver 2.2 wmi-op 2 htt-op 2 cal otp max-sta 128 raw 0 hwcrypto 1
[    7.498629] ath: EEPROM regdomain: 0x0
[    7.498636] ath: EEPROM indicates default country code should be used
[    7.498639] ath: Country alpha2 being used: 00
[    7.498640] ath: Regpair used: 0x64
````

## Credits / References
- [IEEE 802.11p on Linux - by HPI Potsdam](https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux)
- [802.11p-linux - by CTU-IIG](https://github.com/CTU-IIG/802.11p-linux)
- [802.11p-on-linux - by Rostislav Lisovy](https://gist.github.com/lisovy/80dde5a792e774a706a9)
- [A Raghavendra Rao - University of Central Florida](https://gist.github.com/lisovy/80dde5a792e774a706a9?permalink_comment_id=1655168#gistcomment-1655168)
- [Raspberry Pi software](https://www.raspberrypi.com/software/)
- [Raspberry Pi - The Linux kernel](https://www.raspberrypi.com/documentation/computers/linux_kernel.html)
- [Ath10k Candela Technologies (CT) Firmware](https://www.candelatech.com/ath10k.php)

**Contributors**:
- [Rúben Castelhano](mailto:rubencastelhano@av.it.pt)
- [Rodrigo Rosmaninho](https://github.com/RodrigoRosmaninho)
- [Christian Gomes](https://www.linkedin.com/in/christian-anjos-gomes)