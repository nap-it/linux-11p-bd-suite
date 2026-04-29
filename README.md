# IEEE 802.11p/bd Linux Driver Suite
This repository provides instructions and build guides to enable IEEE 802.11p (and experimental IEEE 802.11bd) functionality on devices using ath9k- and ath10k- based devices. It covers a variety of platforms including Raspberry Pi, NVIDIA Jetson, and generic x86 systems, offering kernel patches, and example setups targetted toward vehicular networking and research applications.

# Contributions Overview
Below is an overview of the main contributions of this work:
- `ath10k`: 
    1. Enabled baseline ITS-G5 functionality by building on prior work from [HPI Potsdam](https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux/-/tree/deperecated-ath10k?ref_type=heads).
    2. Enabled correct reception of broadcast frames in OCB mode.
    3. Fixed STA state synchronization issues between `mac80211` and firmware, which previously caused the first frame transmitted to a new STA to be dropped.
    4. Corrected HTT receive buffer sizing to properly support higher MTUs (up to 2304 bytes).
- `mac80211`: Fixed an issue where the first received frame from a STA was dropped in OCB mode (affecting both `ath9k` and `ath10k`).
- `mac80211/cfg80211`: Reverted multiple upstream commits (with necessary adaptations) affecting more recent kernels, which had removed certain 5/10 MHz channel width functionality.

This work has been published as a paper entitled "A Unified Linux Driver Suite for IEEE 802.11bd and 802.11p for Atheros Chipsets", accepted to the IEEE Vehicular Networking Conference (VNC) 2026, to be held in June 2026. The paper provides a more comprehensive description of the design and implementation choices underlying the enabling of IEEE 802.11p functionality on ath10k-based hardware, along with an extended evaluation of the resulting system. In addition to detailing the required modifications to the Linux networking stack and driver components, it presents further experimental results characterising performance and behaviour under realistic vehicular communication conditions. The study also includes exploratory measurements related to 802.11bd-like operation, offering initial insight into achievable performance when extending legacy 802.11p configurations.

## Equipment

### Devices
| Name / Family               | Tutorial Link | Product Page | Product Photo |
|--------------------------|---------------|--------------|---------------|
| x86                      | [Tutorial](x86/README.md) | - | - |
| Raspberry Pi 5           | [Tutorial](Raspberry%20Pi/Pi%205/README.md) | https://www.raspberrypi.com/products/raspberry-pi-5/         |![Raspberry Pi 5](/images/RaspberryPi5.JPEG)|
| NVIDIA Jetson Orin Nano  | [Tutorial](NVIDIA%20Jetson/Orin%20Nano%20Devkit/README.md) | https://developer.nvidia.com/embedded/learn/get-started-jetson-orin-nano-devkit |![NVIDIA Jetson Orin Nano Devkit](/images/NVIDIA%20Jetson%20Orin%20Nano.JPEG)|

### Wireless Network Adapters
The following Wi-Fi network adapters have been tested and validated to work correctly in most situations (see individual notes for your target device):

| Product Name | Chipset | Driver | Notes | Product Page | Product Photo |
|--------------|---------|--------|-------|--------------|---------------|
|MikroTik R11e-5HnD|AR9580|ath9k|Possible power limitations (see individual notes for your target device)|https://www.mikrotik-store.eu/en/mikrotik-r11e-5hnd|![MikroTik R11e-5HnD](/images/R11e-5HnD.JPEG)|
|Compex WLE200NX 7A|AR9280|ath9k|None|https://compex.com.sg/wp-content/uploads/2018/10/wle200nx-hw-guide-v1.pdf|![Compex WLE200NX 7A](/images/WLE200NX%207A.JPEG)|
|Compex WLE900VX 7CA|QCA9890|ath10k|None|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-i-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7CA](/images/WLE900VX%207CA.JPEG)|

The following network adapters may exhibit limited functionality or may have not been tested yet (see notes bellow):

| Product Name | Chipset | Driver | Notes | Product Page | Product Photo |
|--------------|---------|--------|-------|--------------|---------------|
|Compex WLE600VX 7AA|QCA9882|ath10k|Untested. Probably locked to 20MHz channel width|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle600vx-wifi5-11ac-qca9882-qca9892/|![Compex WLE600VX 7AA](/images/placeholder.png)|
|Compex WLE600VX 7CA|QCA9892|ath10k|Untested. Should work fine.|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle600vx-i-wifi5-11ac-qca9882-qca9892/|![Compex WLE600VX 7CA](/images/placeholder.png)|
|Compex WLE900VX 7AA|QCA9880|ath10k|Locked to 20MHz channel width|https://compex.com.sg/shop/wifi-module/802-11ac-wave-1/wle900vx-wifi5-11ac-qca9880-qca9890/|![Compex WLE900VX 7AA](/images/WLE900VX%207AA.JPEG)|
|MikroTik R11e-5HacT|QCA9880|ath10k|Locked to 20MHz channel width. Possible TX power issues caused by insufficient power delivery (see individual notes for your target device)|https://mikrotik.com/product/R11e-5HacT|![MikroTik R11e-5HacT](/images/R11e-5HacT.JPEG)|

## Credits / References
- [IEEE 802.11p on Linux - by HPI Potsdam](https://gitlab.com/hpi-potsdam/osm/g5-on-linux/11p-on-linux)
- [802.11p-linux - by CTU-IIG](https://github.com/CTU-IIG/802.11p-linux)
- [802.11p-on-linux - by Rostislav Lisovy](https://gist.github.com/lisovy/80dde5a792e774a706a9)
- [A Raghavendra Rao - University of Central Florida](https://gist.github.com/lisovy/80dde5a792e774a706a9?permalink_comment_id=1655168#gistcomment-1655168)
- [Ath10k Candela Technologies (CT) Firmware](https://www.candelatech.com/ath10k.php)
- Additional references detailed in the individual board/device-specific pages

**Contributors**:
- [Rúben Castelhano](mailto:rubencastelhano@av.it.pt)
- [Rodrigo Rosmaninho](https://github.com/RodrigoRosmaninho)
- [Christian Gomes](https://www.linkedin.com/in/christian-anjos-gomes)

## License

linux-11p-bd-suite is licensed under LGPLv3, see [license file](LICENSE.md) for details.