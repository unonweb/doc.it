# wake on wlan

* https://www.cyberciti.biz/faq/configure-wireless-wake-on-lan-for-linux-wifi-wowlan-card/

```sh
iw list | grep WoW -A10 # check support
iw phy0 wowlan show # check status
```

