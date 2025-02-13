---
tags:
  - WOL
---

# Networking

# Wake On lan
## Enable WoL on the network adapter [Source](https://wiki.archlinux.org/title/Wake-on-LAN)
It is crucial to enable Wol for the ehternet interface, otherwise the port will just be turned off and Wol will only work a single time when powering on the switch/router.

Don't forget to make this setting persistent as well or it will only work once.

1. Check if Wol is enabled on the adapter with `ethtool`
    ```
    ethtool eth0 | grep Wake-on
    ```

2. Enable Wol
    ```
    ethtool -s eth0 wol g
    ```

3. Make the Wol setting persistent
    Can be done with unraid `User Scripts` plugin. Add the ethtool command to the script and it should work.

    There are many alternatives to enable this automatically on Linux. Here is one using `systemd`
    For other options see the source page.
### WOL persistency with systemd.link 
    Link-level configuration is possible through systemd.link files. The actual setup is performed by the net_setup_link udev builtin. Add the WakeOnLan option to the network link file:
    ```
    file: /etc/systemd/network/50-wired.link
    -------------

    [Match]
    MACAddress=aa:bb:cc:dd:ee:ff

    [Link]
    NamePolicy=kernel database onboard slot path
    MACAddressPolicy=persistent
    WakeOnLan=magic
    ```
Note:
- Only the first matching file is applied. The content of the default link file /usr/lib/systemd/network/99-default.link shipped with systemd has to be included, otherwise the interface might be misconfigured.
- To be considered, the file name should alphabetically come before the default 99-default.link. For example 50-wired.link works.
- This configuration applies only to the link-level, and is independent of network-level daemons such as NetworkManager or systemd-networkd.
- In the Match section, OriginalName= can also be used to identify the interface.
