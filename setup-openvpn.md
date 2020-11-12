Setup OpenVPN
===
Prerequisites
---
- Internet Access
- System that run ubuntu derivatives (recommend : based on Ubuntu 18.04)
- Basic knowledge of Linux Command Line

Installing Dependencies
---
```sh
$ sudo apt update
$ sudo apt install haproxy
$ wget https://git.io/vpn -O openvpn-install.sh
$ bash openvpn-install.sh
```
It will create `.ovpn` files
Open Firewall
---
```sh
$ sudo ufw allow 1194
```
Copy .ovpn files to local
---
```sh
$ scp root@public_ip:/root/client.ovpn /home/laznp/Downloads/client.ovpn # Change laznp to your username
```
Connect VPN on Ubuntu / Debian / Linux Mint
---
```sh
$ sudo apt install openvpn
$ sudo openvpn /home/laznp/Downloads/client.ovpn # Change laznp to your username
```
Connect VPN on Mac OSX
---
```sh
$ brew install openvpn
$ echo "alias openvpn='sudo /usr/local/Cellar/openvpn/2.4.9/sbin/openvpn'" >> ~/.zshrc # For ZSH
$ echo "alias openvpn='sudo /usr/local/Cellar/openvpn/2.4.9/sbin/openvpn'" >> ~/.bashrc # For Bash
$ sudo openvpn /home/laznp/Downloads/client.ovpn # Change laznp to your username
```
Connect VPN on Mobile Devices
---
Copy your `client.ovpn` to mobile device and install OpenVPN from App Store/Play Store
Then open `client.ovpn` with OpenVPN application
