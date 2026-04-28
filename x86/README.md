# x86 Tutorial

The following instructions are targeted and have been tested on Debian 13. They assume the OS is already installed, secure boot is off, all commands are run on the target device, and the repository has been cloned into your home directory.

## x86 Devices and wireless cards tested

### Advantech EI-53
The following adapters have been tested and verified:
| Product Name     | Product Page / Retail Listing | Product Photo |
|------------------|-------------------------------|---------------|
| Delock Adapter M.2 Key B+M to Mini PCIe slot | https://www.delock.com/produkt/64103/merkmale.html |![Delock Adapter M.2 Key B+M to Mini PCIe slot](/images/placeholder.png)|

The following wireless network adapters have been tested and verified to work correctly:
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|Compex WLE200NX 7A |AR9280 |ath9k |None|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE200NX 7A](/images/placeholder.png)|
|Compex WLE900VX 7CA|QCA9890|ath10k|None|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-i-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7CA](/images/placeholder.png)|

The following wireless network adapters have been tested but have limited or no functionality (see notes):
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|MikroTik R11e-5HnD |AR9580 |ath9k |Extremely low and inconsistent TX power (most likely related to adapter/slot power delivery)|https://www.mikrotik-store.eu/en/mikrotik-r11e-5hnd|![MikroTik R11e-5HnD](/images/placeholder.png)|
|MikroTik R11e-5HacT|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX). Likely to suffer from the same TX power issues as its predecessor|https://mikrotik.com/product/R11e-5HacT|![MikroTik R11e-5HacT](/images/placeholder.png)|
|Compex WLE900VX 7AA|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE900VX 7AA](/images/placeholder.png)|

### PC-Engines APU2/3
No adapters have been tested as the device has built-in mini PCIe slots

The following wireless network adapters have been tested and verified to work correctly:
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|Compex WLE200NX 7A |AR9280 |ath9k |None|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE200NX 7A](/images/placeholder.png)|
|MikroTik R11e-5HnD |AR9580 |ath9k |None|https://www.mikrotik-store.eu/en/mikrotik-r11e-5hnd|![MikroTik R11e-5HnD](/images/placeholder.png)|
|Compex WLE900VX 7CA|QCA9890|ath10k|None|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-i-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7CA](/images/placeholder.png)|

The following wireless network adapters have been tested but have limited or no functionality (see notes):
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|MikroTik R11e-5HacT|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://mikrotik.com/product/R11e-5HacT|![MikroTik R11e-5HacT](/images/placeholder.png)|
|Compex WLE900VX 7AA|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE900VX 7AA](/images/placeholder.png)|

### Minisforum MS-01
The following adapters have been tested and verified:
| Product Name     | Product Page / Retail Listing | Product Photo |
|------------------|-------------------------------|---------------|
| Adaptateur MiniCard MiniPCIe ou USB vers PCIe ou USB DUAL BAND 2.4 5GHz + SIM | https://www.kalea-informatique.com/adaptateur-minicard-minipcie-ou-usb-vers-pcie-ou-usb.htm |![Adaptateur MiniCard MiniPCIe ou USB vers PCIe ou USB DUAL BAND 2.4 5GHz + SIM](/images/placeholder.png)|

The following wireless network adapters have been tested and verified to work correctly:
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|Compex WLE200NX 7A |AR9280 |ath9k |None|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE200NX 7A](/images/placeholder.png)|
|MikroTik R11e-5HnD |AR9580 |ath9k |None|https://www.mikrotik-store.eu/en/mikrotik-r11e-5hnd|![MikroTik R11e-5HnD](/images/placeholder.png)|
|Compex WLE900VX 7CA|QCA9890|ath10k|None|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-i-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7CA](/images/placeholder.png)|

The following wireless network adapters have been tested but have limited or no functionality (see notes):
| Product Name      | Chipset | Driver | Notes | Product Page / Retail Listing | Product Photo |
|-------------------|---------|--------|-------|-------------------------------|---------------|
|MikroTik R11e-5HacT|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://mikrotik.com/product/R11e-5HacT|![MikroTik R11e-5HacT](/images/placeholder.png)|
|Compex WLE900VX 7AA|QCA9880|ath10k|Limited to 20 MHz channels (unless DD-WRT firmware is used, which breaks broadcast TX)|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE900VX 7AA](/images/placeholder.png)|

## Instructions
1. Power off your device, then install your chosen wireless adapter along with any additional required adapters.
2. Turn on your device and access its terminal via SSH or the built-in desktop environment.
3. Modify the wireless regulatory database to allow ITS-G5 channels.
    1. Fetch sources for the wireless regulatory database (note that your folder may have a slightly different name)
        ````
        sudo apt install dpkg-dev -y
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
4. Build a customized version of the kernel
    1. Install the required build dependencies
        ````
        sudo apt install build-essential git bc bison flex libssl-dev make libncurses-dev debhelper-compat libelf-dev rsync iw ethtool -y
        ````
    2. Get and unpack the kernel sources
        ````
        cd ~/
        git clone --branch linux-6.12.y https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
        ````
    3. Patch the source code
        ````
        cd ~/linux
        patch -p1 < ~/linux-11p-bd-suite/x86/patches/linux-6.12.x.patch
        ````
    4. Copy the certificated used to sign the wireless regulatory database into the kernel's wireless certificate datastore
        ````
        cp ~/$(whoami).hex ~/linux/net/wireless/certs/$(whoami).hex
        ````
    5. Get the Candela Technologies ath10k firmware (optional)
        ````
        cd /lib/firmware/ath10k/QCA988X/hw2.0/
        sudo wget https://www.candelatech.com/downloads/ath10k-fw-beta/firmware-2-ct-htt-mgt-community.bin
        ````
    6. Modify the default kernel config
        1. Include the wireless regulatory database into the kernel build. This is optional but it ensures that future updates can not override your custom regulatory database. Additionally, if you wish to reuse your kernel build in other boards, you do not need to repeat the database build process or copy the resulting files.
            ````
            cd ~/linux
            cp /boot/config-$(uname -r) .config
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
        make -j$(nproc) SUBLEVEL=10$(make -s kernelversion | cut -d. -f3) EXTRAVERSION=-nap LOCALVERSION=-$(date +%s) bindeb-pkg
        ````
    8. Install the kernel.
        ````
        cd ~
        sudo dpkg -i linux-*.deb
        ````
5. Setup and initialize the interface
    1. If using an `ath10k` device:
        1. Get the PCI address of your wireless card:
            ````
            lspci
            ````
            Your output should look something like this:
            ````
            00:00.0 Host bridge: Intel Corporation Device a71c (rev 01)
            00:02.0 VGA compatible controller: Intel Corporation Raptor Lake-P [UHD Graphics] (rev 04)
            00:06.0 PCI bridge: Intel Corporation Raptor Lake PCIe 4.0 Graphics Port (rev 01)
            00:08.0 System peripheral: Intel Corporation GNA Scoring Accelerator module (rev 01)
            00:0a.0 Signal processing controller: Intel Corporation Raptor Lake Crashlog and Telemetry (rev 01)
            00:0d.0 USB controller: Intel Corporation Raptor Lake-P Thunderbolt 4 USB Controller (rev 01)
            00:14.0 USB controller: Intel Corporation Alder Lake PCH USB 3.2 xHCI Host Controller (rev 01)
            00:14.2 RAM memory: Intel Corporation Alder Lake PCH Shared SRAM (rev 01)
            00:15.0 Serial bus controller: Intel Corporation Alder Lake PCH Serial IO I2C Controller #0 (rev 01)
            00:15.1 Serial bus controller: Intel Corporation Alder Lake PCH Serial IO I2C Controller #1 (rev 01)
            00:16.0 Communication controller: Intel Corporation Alder Lake PCH HECI Controller (rev 01)
            00:16.3 Serial controller: Intel Corporation Alder Lake AMT SOL Redirection (rev 01)
            00:17.0 SATA controller: Intel Corporation Alder Lake-P SATA AHCI Controller (rev 01)
            00:1c.0 PCI bridge: Intel Corporation Alder Lake PCI Express x4 Root Port #5 (rev 01)
            00:1c.7 PCI bridge: Intel Corporation Alder Lake PCH-P PCI Express Root Port #9 (rev 01)
            00:1f.0 ISA bridge: Intel Corporation Raptor Lake LPC/eSPI Controller (rev 01)
            00:1f.3 Audio device: Intel Corporation Raptor Lake-P/U/H cAVS (rev 01)
            00:1f.4 SMBus: Intel Corporation Alder Lake PCH-P SMBus Host Controller (rev 01)
            00:1f.5 Serial bus controller: Intel Corporation Alder Lake-P PCH SPI Controller (rev 01)
            00:1f.6 Ethernet controller: Intel Corporation Ethernet Connection (16) I219-LM (rev 01)
            01:00.0 Network controller: Qualcomm Atheros QCA986x/988x 802.11ac Wireless Network Adapter
            02:00.0 Network controller: Qualcomm Technologies, Inc WCN785x Wi-Fi 7(802.11be) 320MHz 2x2 [FastConnect 7800] (rev 01)
            03:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-LM (rev 04)
            ````
            So in this case, the address of the card is `0001:01:00.0`.
        2. Create a config file like at `/lib/firmware/ath10k/fwcfg-pci-<your-pci-address>.txt`. Ex: `/lib/firmware/ath10k/fwcfg-pci-0000\:01\:00.0.txt`

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
    2. Make `network-manager` ignore the interface (optional)
        ````
        sudo bash -c 'echo -e "[keyfile]\nunmanaged-devices=interface-name:wlp1s0" > /etc/NetworkManager/conf.d/10-ignore-wlp1s0.conf && systemctl restart NetworkManager'
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
            sudo iw wlp1s0 set type ocb
            sudo ip l set dev wlp1s0 up
            sudo iw wlp1s0 ocb join 5900 10MHz
            ````
        2. ath10k
            ````
            phy=$(cat /sys/class/net/wlp1s0/phy80211/name)
            echo "10" | sudo tee /sys/kernel/debug/ieee80211/$phy/ath10k/chan_bw
            sudo iw wlp1s0 set type ocb
            sudo ip l set dev wlp1s0 up
            sudo iw wlp1s0 ocb join 5900 10MHz
            ````

### Example kernel logs
#### ath9k
````
[    2.935690] ath9k 0000:01:00.0: enabling device (0000 -> 0002)
[    3.370521] ath: EEPROM regdomain: 0x0
[    3.370526] ath: EEPROM indicates default country code should be used
[    3.370528] ath: Country alpha2 being used: 00
[    3.370529] ath: Regpair used: 0x64
[    3.413229] ath9k 0000:01:00.0 wlp1s0: renamed from wlan0
````

#### ath10k
````
[    3.287090] ath10k 6.12, optimized for CT firmware, with IEEE 802.11p support, probing pci device: 0x3c.
[    3.287270] ath10k_pci 0000:01:00.0: enabling device (0000 -> 0002)
[    3.287599] ath10k_pci 0000:01:00.0: pci irq msi oper_irq_mode 2 irq_mode 0 reset_mode 0
[    3.420730] ath10k_pci 0000:01:00.0: fwcfg key: fwname  val: firmware-2-ct-htt-mgt-community.bin
[    3.420735] ath10k_pci 0000:01:00.0: fwcfg key: fwver  val: 2
[    3.420737] ath10k_pci 0000:01:00.0: fwcfg key: max_nss  val: 1
[    3.420901] ath10k_pci 0000:01:00.0: qca988x hw2.0 target 0x4100016c chip_id 0x043202ff sub 0000:0000
[    3.420905] ath10k_pci 0000:01:00.0: kconfig debug 1 debugfs 1 tracing 1 dfs 0 testmode 0
[    3.421027] ath10k_pci 0000:01:00.0: firmware ver 10.1-ct-8x-__tH-023-23ea9f8e api 2 features wmi-10.x,mfp,peer-fixed-rate,txstatus-noack,wmi-10.x-CT,ratemask-CT,txrate-CT,no-bmiss-CT,get-temp-CT,tx-rc-CT,cust-stats-CT,retry-gt2-CT,txrate2-CT,beacon-cb-CT,wmi-block-ack-CT crc32 8665d010
[    3.446969] ath10k_pci 0000:01:00.0: board_file api 1 bmi_id N/A crc32 bebc7c08
[    4.255495] ath10k_pci 0000:01:00.0: hardware advertises support for more spatial streams than it should (3 > 1)
[    4.255520] ath10k_pci 0000:01:00.0: 10.1 wmi init: vdevs: 16  peers: 127  tid: 256
[    4.265197] ath10k_pci 0000:01:00.0: wmi print 'P 128 V 8 T 410'
[    4.265300] ath10k_pci 0000:01:00.0: wmi print 'msdu-desc: 1424  sw-crypt: 0 ct-sta: 0'
[    4.265797] ath10k_pci 0000:01:00.0: wmi print 'alloc rem: 26712 iram: 54568'
[    4.306177] ath10k_pci 0000:01:00.0: htt-ver 2.2 wmi-op 2 htt-op 2 cal otp max-sta 128 raw 0 hwcrypto 1
[    4.328209] ath10k_pci 0000:01:00.0: NOTE:  Firmware DBGLOG output disabled in debug_mask: 0x10000000
[    4.392846] ath: EEPROM regdomain: 0x0
[    4.392851] ath: EEPROM indicates default country code should be used
[    4.392852] ath: Country alpha2 being used: 00
[    4.392853] ath: Regpair used: 0x64
[    4.395039] ath10k_pci 0000:01:00.0 wlp1s0: renamed from wlan0
````

## Credits / References
- [IEEE 802.11p on Linux - by HPI Potsdam](https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux)
- [802.11p-linux - by CTU-IIG](https://github.com/CTU-IIG/802.11p-linux)
- [802.11p-on-linux - by Rostislav Lisovy](https://gist.github.com/lisovy/80dde5a792e774a706a9)
- [A Raghavendra Rao - University of Central Florida](https://gist.github.com/lisovy/80dde5a792e774a706a9?permalink_comment_id=1655168#gistcomment-1655168)
- [Ath10k Candela Technologies (CT) Firmware](https://www.candelatech.com/ath10k.php)

**Contributors**:
- [Rúben Castelhano](mailto:rubencastelhano@av.it.pt)
- [Rodrigo Rosmaninho](https://github.com/RodrigoRosmaninho)
- [Christian Gomes](https://www.linkedin.com/in/christian-anjos-gomes)