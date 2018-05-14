# WiFi

## Symptoms

- WiFi disconnects.
- Firmware crashes:
  ```console
  $ dmesg -H
  [...]
  [May14 10:27] ath10k_pci 0000:3a:00.0: firmware crashed! (uuid 5e323599-d1e8-44f2-a614-9c0df1ea5147)
  [  +0.000100] ath10k_pci 0000:3a:00.0: qca6174 hw3.2 (0x05030000, 0x00340aff sub 1a56:1535) fw WLAN.RM.2.0-00180-QCARMSWPZ-1 fwapi 4 bdapi 2 htt-ver 3.26 wmi-op 4 htt-op 3 cal otp max-sta 32 raw 0 hwcrypto 1 f
  [  +0.000032] ath10k_pci 0000:3a:00.0: debug 0 debugfs 1 tracing 1 dfs 0 testmode 0
  [  +0.002041] ath10k_pci 0000:3a:00.0: firmware register dump:
  [  +0.000036] ath10k_pci 0000:3a:00.0: [00]: 0x05030000 0x000015B3 0x009860FA 0x00955B31
  [  +0.000030] ath10k_pci 0000:3a:00.0: [04]: 0x009860FA 0x00060730 0x00000004 0x0040E8A0
  [  +0.000022] ath10k_pci 0000:3a:00.0: [08]: 0x00498110 0x00955A00 0x0000000B 0x00400000
  [  +0.000029] ath10k_pci 0000:3a:00.0: [12]: 0x00000009 0x00000000 0x00952CD0 0x00952CE6
  [  +0.000036] ath10k_pci 0000:3a:00.0: [16]: 0x00952CC4 0x0091080D 0x00000000 0x0091080D
  [  +0.000024] ath10k_pci 0000:3a:00.0: [20]: 0x409860FA 0x0040E7E8 0x00000000 0x0041E0DC
  [  +0.000021] ath10k_pci 0000:3a:00.0: [24]: 0x800B5A1D 0x0040E848 0x000FFFFF 0xC09860FA
  [  +0.000025] ath10k_pci 0000:3a:00.0: [28]: 0x809B3230 0x0040E948 0x00000018 0x004313B8
  [  +0.000027] ath10k_pci 0000:3a:00.0: [32]: 0x809B2992 0x0040E998 0x0040E9BC 0x00429548
  [  +0.000027] ath10k_pci 0000:3a:00.0: [36]: 0x8091D252 0x0040E9B8 0x00000000 0x00000001
  [  +0.000020] ath10k_pci 0000:3a:00.0: [40]: 0x809FF05D 0x0040EA68 0x0043A380 0x00429C10
  [  +0.000021] ath10k_pci 0000:3a:00.0: [44]: 0x809FCFDB 0x0040EA88 0x0043A380 0x00000001
  [  +0.000035] ath10k_pci 0000:3a:00.0: [48]: 0x80911210 0x0040EAD8 0x00000010 0x004041D0
  [  +0.000025] ath10k_pci 0000:3a:00.0: [52]: 0x80911154 0x0040EB28 0x00400000 0x00000000
  [  +0.000021] ath10k_pci 0000:3a:00.0: [56]: 0x8091122D 0x0040EB48 0x00000000 0x00400600
  [  +0.085946] ieee80211 phy0: Hardware restart was requested
  [  +2.371769] ath10k_pci 0000:3a:00.0: device successfully recovered
  [...]
  ```

## Steps

```console
sudo lshw
[...]
           *-network
                description: Wireless interface
                product: QCA6174 802.11ac Wireless Network Adapter
                vendor: Qualcomm Atheros
                physical id: 0
                bus info: pci@0000:3a:00.0
                logical name: wlp58s0
                version: 32
                serial: 9c:b6:d0:d1:98:a1
                width: 64 bits
                clock: 33MHz
                capabilities: pm msi pciexpress bus_master cap_list ethernet physical wireless
                configuration: broadcast=yes driver=ath10k_pci driverversion=4.4.0-124-generic firmware=WLAN.RM.2.0-00180-QCARMSWPZ-1 ip=192.168.1.165 latency=0 link=yes multicast=yes wireless=IEEE 802.11abgn
                resources: irq:135 memory:dc000000-dc1fffff
[...]
```

```console
$ sudo apt update
$ sudo apt install --reinstall linux-firmware
```

```console
$ tree --charset ascii -D /lib/firmware/ath10k/QCA6174/
/lib/firmware/ath10k/QCA6174/
|-- [May 11 14:54]  hw2.1
|   |-- [Nov 17 16:40]  board-2.bin
|   |-- [Mar 30  2017]  board.bin
|   |-- [Nov 17 16:40]  firmware-5.bin
|   `-- [Mar 30  2017]  notice_ath10k_firmware-5.txt
`-- [May 11 14:54]  hw3.0
    |-- [May 12  2016]  board-2.bin
    |-- [Feb  9 16:59]  board-2.bin.wifi-qca6174
    |-- [May 12  2016]  board.bin
    |-- [Mar 30  2017]  board.bin.wifi-qca6174
    |-- [Mar 30  2017]  firmware-4.bin
    |-- [Jan 17 15:49]  firmware-6.bin
    |-- [Mar 30  2017]  notice_ath10k_firmware-4.txt
    `-- [Jan 17 15:49]  notice_ath10k_firmware-6.txt

2 directories, 12 files
```

- Back `/lib/firmware/ath10k/QCA6174` up.
- Delete everything under `/lib/firmware/ath10k/QCA6174` and just keep `/lib/firmware/ath10k/QCA6174/hw3.0` as an empty directory.

```console
$ cd /lib/firmware/ath10k/QCA6174/hw3.0/
$ sudo curl -Lo board-2.bin "https://github.com/kvalo/ath10k-firmware/blob/master/QCA6174/hw3.0/board-2.bin?raw=true"
$ sudo curl -Lo board.bin "https://github.com/kvalo/ath10k-firmware/blob/master/QCA6174/hw3.0/board.bin?raw=true"
$ sudo curl -Lo firmware-5.bin "https://github.com/kvalo/ath10k-firmware/blob/master/QCA6174/hw3.0/4.4.1/firmware-6.bin_WLAN.RM.4.4.1-00102-QCARMSWP-1?raw=true"
$ sudo curl -LO "https://raw.githubusercontent.com/kvalo/ath10k-firmware/master/QCA6174/hw3.0/4.4.1/notice.txt_WLAN.RM.4.4.1-00102-QCARMSWP-1"
$ tree --charset ascii -D /lib/firmware/ath10k/QCA6174/
/lib/firmware/ath10k/QCA6174/
`-- [May 14 15:05]  hw3.0
    |-- [May 14 15:05]  board-2.bin
    |-- [May 14 15:05]  board.bin
    |-- [May 14 15:05]  firmware-5.bin
    `-- [May 14 15:05]  notice.txt_WLAN.RM.4.4.1-00102-QCARMSWP-1

1 directory, 4 files
$ sudo reboot
```

## Resources

- https://wireless.wiki.kernel.org/en/users/Drivers/ath10k/firmware
- https://github.com/kvalo/ath10k-firmware/tree/master/QCA6174
