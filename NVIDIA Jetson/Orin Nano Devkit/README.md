# NVIDIA Jetson Orin Nano Devkit Tutorial

## Equipment

The following M.2 A+E to mini-PCIe adapters have been tested and verified so far:
| Product Name     | Product Page / Retail Listing | Product Photo |
|------------------|-------------------------------|---------------|
| LeHang M.2 (NGFF) key A/E/A+E to Mini PCI-E Adapter with FFC Cable | https://www.amazon.es/-/pt/dp/B07JCPMKQ8 |![LeHang M.2 (NGFF) key A/E/A+E to Mini PCI-E Adapter with FFC Cable](/images/LeHang%20M.2%20(NGFF).JPEG)|

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

The following instructions are targeted toward JetPack 6.2.2 (L4T 36.5.0), although they should work for any 6.x.x version with some minor changes. These instructions assume that JetPack has already been installed. While cross-compilation from a host machine is possible, this guide assumes that all commands are executed directly on the Jetson target device. You can find the official quick start guide from NVIDIA [here](https://docs.nvidia.com/jetson/archives/r36.4.4/DeveloperGuide/IN/QuickStart.html).

1. With the board turned off, assemble the intermediary adapter and the external Wi-Fi module. You'll have to remove the included MediaTek Wi-Fi card.
2. Turn on the board and access its terminal via SSH or the built-in desktop environment.
3. Modify your APT sources so you can fetch package sources. You can do this by uncommenting the lines beginning with `deb-src` already in the `/etc/apt/sources.list` file and then updating your sources. Note that you don't need to uncomment all `deb-src` sources in the file, only the main `jammy` and `jammy-updates`. 
    ````
    sudo apt install nano -y
    sudo nano /etc/apt/sources.list
    sudo apt update
    ````

    The top of your `sources.list` file should look something like this before running apt update:
    ````
    # See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
    # newer versions of the distribution.
    deb http://ports.ubuntu.com/ubuntu-ports/ jammy main restricted
    deb-src http://ports.ubuntu.com/ubuntu-ports/ jammy main restricted

    ## Major bug fix updates produced after the final release of the
    ## distribution.
    deb http://ports.ubuntu.com/ubuntu-ports/ jammy-updates main restricted
    deb-src http://ports.ubuntu.com/ubuntu-ports/ jammy-updates main restricted
    ````
4. Modify the wireless regulatory database to allow ITS-G5 channels.
    1. Install required build dependencies
        ````
        sudo apt install build-essential python-is-python3 xxd python3-m2crypto dpkg-dev -y
        ````
    2. Fetch sources for the wireless regulatory database 
        ````
        apt source wireless-regdb
        cd wireless-regdb-2025.10.07
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
        sudo apt install git build-essential bc flex bison libssl-dev zstd libncurses-dev -y
        ````
    2. Get and unpack the kernel sources
        ````
        cd ~/
        wget https://developer.nvidia.com/downloads/embedded/l4t/r36_release_v5.0/release/Jetson_Linux_r36.5.0_aarch64.tbz2
        tar -xf Jetson_Linux_r36.5.0_aarch64.tbz2
        rm Jetson_Linux_r36.5.0_aarch64.tbz2
        cd Linux_for_Tegra/source
        ./source_sync.sh -t jetson_36.5 -e -s # -k seems broken...
        ````
    3. Patch the source code
        1. Patch the source code
            ````
            cd ~/Linux_for_Tegra/source/kernel/kernel-jammy-src/
            patch -p1 < ~/linux-11p-bd-suite/NVIDIA\ Jetson/Orin\ Nano\ Devkit/patches/linux-5.15.148.patch
            ````
    4. Copy the certificated used to sign the wireless regulatory database into the kernel's wireless certificate datastore
        ````
        cp ~/$(whoami).hex ~/Linux_for_Tegra/source/kernel/kernel-jammy-src/net/wireless/certs/$(whoami).hex
        ````
    5. Get the Candela Technologies ath10k firmware
        ````
        cd /lib/firmware/ath10k/QCA988X/hw2.0/
        sudo wget https://www.candelatech.com/downloads/ath10k-fw-beta/firmware-2-ct-htt-mgt-community.bin
        ````
    6. Modify the default kernel config
        1. Include the wireless regulatory database into the kernel build. This is optional but it ensures that future updates can not override your custom regulatory database. Additionally, if you wish to reuse your kernel build in other boards, you do not need to repeat the database build process or copy the resulting files.
            ````
            cd ~/Linux_for_Tegra/source/kernel/kernel-jammy-src/
            scripts/config --file arch/arm64/configs/defconfig --set-str CONFIG_EXTRA_FIRMWARE "regulatory.db regulatory.db.p7s"
            scripts/config --file arch/arm64/configs/defconfig --set-str CONFIG_EXTRA_FIRMWARE_DIR "/lib/firmware"
            ````
        2. If using an ath10k device, you can also include the custom firmware into the kernel build
            ````
            scripts/config --file arch/arm64/configs/defconfig --set-str CONFIG_EXTRA_FIRMWARE "regulatory.db regulatory.db.p7s ath10k/QCA988X/hw2.0/firmware-2-ct-htt-mgt-community.bin"
            ````
        3. Enable `ath9k` and `ath10k` to be built as modules as well as enable their debug and tracing features.
            ````
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_CFG80211_DEBUGFS
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_MAC80211_DEBUGFS
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH9K_HW
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH9K_COMMON
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_COMMON_DEBUG
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_BTCOEX_SUPPORT
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH9K
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_PCI
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_AHB
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_DEBUGFS
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_STATION_STATISTICS
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_RFKILL
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_PCOEM
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH9K_PCI_NO_EEPROM
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH9K_COMMON_SPECTRAL

            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH10K
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_CE
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH10K_PCI
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_AHB
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH10K_SDIO
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH10K_USB
            scripts/config --file arch/arm64/configs/defconfig --module CONFIG_ATH10K_SNOC
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_DEBUG
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_DEBUGFS
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_SPECTRAL
            scripts/config --file arch/arm64/configs/defconfig --enable CONFIG_ATH10K_TRACING
            ````
    7. Build the kernel using a custom `LOCALVERSION` to ensure separation from the default kernel
        ````
        cd ~/Linux_for_Tegra/source/
        export LOCALVERSION="-tegra-nap-$(date +%s)"
        make -C kernel
        ````
    8. Build the OOT kernel modules
        ````
        export KERNEL_HEADERS=$PWD/kernel/kernel-jammy-src
        make modules
        ````
    9. Build the DTBs
        ````
        make dtbs
        ````
    10. Install the new kernel, while keeping the original unchanged
        1. Install the new kernel
            ````
            export INSTALL_MOD_PATH=""
            sudo -E make install -C kernel
            sudo cp kernel/kernel-jammy-src/arch/arm64/boot/Image /boot/Image-nap
            sudo -E make modules_install
            sudo cp kernel-devicetree/generic-dts/dtbs/* /boot/dtb/
            sudo update-initramfs -u -k "5.15.185$LOCALVERSION"
            sudo ln -s /boot/initrd.img-5.15.185$LOCALVERSION /boot/initrd-nap.img
            ````
        2. Edit `/boot/extlinux/extlinux.conf` to default to the new kernel, while keeping the old one as a backup. 
            1. Create a copy of the primary entry and change the `LABEL` from `primary` to `custom`, the `MENU LABEL` from `primary kernel` to `custom kernel`, `LINUX` from `/boot/Image` to `/boot/Image-nap`, and `INITRD` from `/boot/inird` to `/boot/initrd-nap.img`
            2. At the top of the file change the `DEFAULT` from `primary` to `custom`.

            The resulting file should look something like:
            ````
            TIMEOUT 30
            DEFAULT custom

            MENU TITLE L4T boot options

            LABEL custom
                MENU LABEL custom kernel
                LINUX /boot/Image-nap
                INITRD /boot/initrd-nap.img
                APPEND ${cbootargs} root=PARTUUID=c9474391-932b-4bdc-bae6-91b39f316f26 rw rootwait rootfstype=ext4 mminit_loglevel=4 console=ttyTCU0,115200 firmware_class.path=/etc/firmware fbcon=map:0 video=efifb:off console=tty0 efi=runtime pci=pcie_bus_perf nvme.use_threaded_interrupts=1 nv-auto-config 

            LABEL primary
                MENU LABEL primary kernel
                LINUX /boot/Image
                INITRD /boot/initrd
                APPEND ${cbootargs} root=PARTUUID=c9474391-932b-4bdc-bae6-91b39f316f26 rw rootwait rootfstype=ext4 mminit_loglevel=4 console=ttyTCU0,115200 firmware_class.path=/etc/firmware fbcon=map:0 video=efifb:off console=tty0 efi=runtime pci=pcie_bus_perf nvme.use_threaded_interrupts=1 nv-auto-config
            ````
    11. Reboot
        ````
        sudo reboot
        ````
6. Setup and initialize the interface
    1. If using an `ath10k` device:
        1. Get the PCI address of your wireless card:
            ````
            lspci
            ````
            Your output should look something like this:
            ````
            0001:00:00.0 PCI bridge: NVIDIA Corporation Device 229e (rev a1)
            0001:01:00.0 Network controller: Qualcomm Atheros QCA986x/988x 802.11ac Wireless Network Adapter
            0004:00:00.0 PCI bridge: NVIDIA Corporation Device 229c (rev a1)
            0004:01:00.0 Non-Volatile memory controller: Kingston Technology Company, Inc. Device 5013 (rev 01)
            0008:00:00.0 PCI bridge: NVIDIA Corporation Device 229c (rev a1)
            0008:01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 15)
            ````
            So in this case, the address of the card is `0001:01:00.0`.
        2. Create a config file like at `/lib/firmware/ath10k/fwcfg-pci-<your-pci-address>.txt`. Ex: `/lib/firmware/ath10k/fwcfg-pci-0001\:01\:00.0.txt`

            Place the following contents in the file (more info [here](https://www.candelatech.com/ath10k-10.4.php#config)):
            ````
            fwname = firmware-2-ct-htt-mgt-community.bin
            fwver = 2
            max_nss = 1
            ````
        3. Reboot
            ````
            sudo reboot
            ````
    2. Make `network-manager` ignore the interface:
        ````
        sudo bash -c 'echo -e "[keyfile]\nunmanaged-devices=interface-name:wlP1p1s0" > /etc/NetworkManager/conf.d/10-ignore-wlP1p1s0.conf && systemctl restart NetworkManager'
        ````
    3. Set your regulatory domain in the `cfg80211` kernel module. Example for Portugal:
        ````
        echo "options cfg80211 ieee80211_regdom=PT" | sudo tee /etc/modprobe.d/cfg80211.conf
        sudo reboot
        ````
    4. Ensure the correct regulatory domain has been applied:
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
    5. Activate the interface:
        1. ath9k
            ````
            sudo iw wlP1p1s0 set type ocb
            sudo ip l set dev wlP1p1s0 up
            sudo iw wlP1p1s0 ocb join 5900 10MHz
            ````
        2. ath10k
            ````
            phy=$(cat /sys/class/net/wlP1p1s0/phy80211/name)
            echo "10" | sudo tee /sys/kernel/debug/ieee80211/$phy/ath10k/chan_bw
            sudo iw wlP1p1s0 set type ocb
            sudo ip l set dev wlP1p1s0 up
            sudo iw wlP1p1s0 ocb join 5900 10MHz
            ````

### Example kernel logs
#### ath9k
````
[    6.573150] ath9k 0001:01:00.0: Adding to iommu group 4
[    6.573374] ath9k 0001:01:00.0: enabling device (0000 -> 0002)
[    7.013597] ath: EEPROM regdomain: 0x0
[    7.013607] ath: EEPROM indicates default country code should be used
[    7.013612] ath: Country alpha2 being used: 00
[    7.013613] ath: Regpair used: 0x64
[    7.454415] ath9k 0001:01:00.0 wlP1p1s0: renamed from wlan0
````

#### ath10k
````
[    6.544441] ath10k_pci 0001:01:00.0: Adding to iommu group 4
[    6.545159] ath10k 5.15 driver, optimized for CT firmware, with IEEE 802.11p support, probing PCI device: 0x3c.
[    6.545560] ath10k_pci 0001:01:00.0: enabling device (0000 -> 0002)
[    6.548179] ath10k_pci 0001:01:00.0: pci irq msi oper_irq_mode 2 irq_mode 0 reset_mode 0
[    6.720003] ath10k_pci 0001:01:00.0: fwcfg key: fwname  val: firmware-2-ct-htt-mgt-community.bin
[    6.720013] ath10k_pci 0001:01:00.0: fwcfg key: fwver  val: 2
[    6.720018] ath10k_pci 0001:01:00.0: fwcfg key: max_nss  val: 1
[    7.277585] ath10k_pci 0001:01:00.0: qca988x hw2.0 target 0x4100016c chip_id 0x043202ff sub 0000:0000
[    7.277595] ath10k_pci 0001:01:00.0: kconfig debug 1 debugfs 1 tracing 1 dfs 0 testmode 0
[    7.277614] ath10k_pci 0001:01:00.0: firmware ver 10.1-ct-8x-__tH-023-23ea9f8e api 2 features wmi-10.x,mfp,peer-fixed-rate,txstatus-noack,wmi-10.x-CT,ratemask-CT,txrate-CT,no-bmiss-CT,get-temp-CT,tx-rc-CT,cust-stats-CT,retry-gt2-CT,txrate2-CT,beacon-cb-CT,wmi-block-ack-CT crc32 8665d010
[    7.303181] ath10k_pci 0001:01:00.0: Loading BDF type 0
[    7.325190] ath10k_pci 0001:01:00.0: board_file api 1 bmi_id N/A crc32 bebc7c08
[    8.151639] ath10k_pci 0001:01:00.0: hardware advertises support for more spatial streams than it should (3 > 1)
[    8.151871] ath10k_pci 0001:01:00.0: 10.1 wmi init: vdevs: 16  peers: 127  tid: 256
[    8.161215] ath10k_pci 0001:01:00.0: wmi print 'P 128 V 8 T 410'
[    8.161250] ath10k_pci 0001:01:00.0: wmi print 'msdu-desc: 1424  sw-crypt: 0 ct-sta: 0'
[    8.161835] ath10k_pci 0001:01:00.0: wmi print 'alloc rem: 26712 iram: 54568'
[    8.194098] ath10k_pci 0001:01:00.0: htt-ver 2.2 wmi-op 2 htt-op 2 cal otp max-sta 128 raw 0 hwcrypto 1
[    8.222026] ath10k_pci 0001:01:00.0: NOTE:  Firmware DBGLOG output disabled in debug_mask: 0x10000000
[    8.306495] ath: EEPROM regdomain: 0x0
[    8.306500] ath: EEPROM indicates default country code should be used
[    8.306503] ath: Country alpha2 being used: 00
[    8.306505] ath: Regpair used: 0x64
[    8.311707] ath10k_pci 0001:01:00.0 wlP1p1s0: renamed from wlan0
````

## Credits / References
- [IEEE 802.11p on Linux - by HPI Potsdam](https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux)
- [802.11p-linux - by CTU-IIG](https://github.com/CTU-IIG/802.11p-linux)
- [802.11p-on-linux - by Rostislav Lisovy](https://gist.github.com/lisovy/80dde5a792e774a706a9)
- [A Raghavendra Rao - University of Central Florida](https://gist.github.com/lisovy/80dde5a792e774a706a9?permalink_comment_id=1655168#gistcomment-1655168)
- [Jetson Download Center - by NVIDIA](https://developer.nvidia.com/embedded/downloads)
- [NVIDIA Jetson Linux Developer Guide - Quick Start](https://docs.nvidia.com/jetson/archives/r36.4.4/DeveloperGuide/IN/QuickStart.html)
- [Ath10k Candela Technologies (CT) Firmware](https://www.candelatech.com/ath10k.php)

**Contributors**:
- [Rúben Castelhano](mailto:rubencastelhano@av.it.pt)
- [Rodrigo Rosmaninho](https://github.com/RodrigoRosmaninho)
- [Christian Gomes](https://www.linkedin.com/in/christian-anjos-gomes)