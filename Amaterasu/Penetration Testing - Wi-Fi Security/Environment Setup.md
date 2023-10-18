[[Wi-Fi Pentest]]
1. `lsmod | grep mac80211` - check your drivers
2. `iwconfig` - list wlan interfaces
3. `iw list` - to get list of adapter's supported modes and capabilities
4. `iwconfig wlan0 channel 11` - set the card to channel 11.
5. `iw dev wlan0 set channel 11` - set the card to channel 11.
6. `iw reg set BO; iw dev wlan0 set txpower fixed 30dbm` - increase maximum transmit power of a wireless adapter.
7. `airmon-ng start wlan0` - monitor mode can be configured on interface.
8. `airmon-ng stop mon0` - stopping/deleting the monitor interface is very simple
9. `airmon-ng check kill` - to get rid of blocking programs
10. test mode with airplay-ng
11. `airplay-ng -9 mon0` - test mode; airplay-ng will send out broadcast probe requests