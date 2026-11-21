# Raspberry Pi Secure Remote Access VPN

## Project Overview

This project implements a self-hosted VPN using a Raspberry Pi 5 and PiVPN with WireGuard. The objective is to establish secure remote access to a private home network without exposing remote desktop services directly to the public internet.

Through the VPN, a remote Windows laptop can securely connect to the home network and access a Windows desktop using RustDesk.

## Objectives

- Deploy a self-hosted VPN using Raspberry Pi 5.
- Configure WireGuard through PiVPN.
- Establish secure remote connectivity.
- Implement remote desktop access through an encrypted VPN tunnel.
- Understand network routing, firewall rules, and port forwarding.
- Evaluate and document security controls.

## Technologies Used

- Raspberry Pi 5
- Raspberry Pi OS
- PiVPN
- WireGuard
- RustDesk
- Windows 11
- Ethernet networking

## Network Architecture

*Network topology diagram coming soon.*

## Implementation

### 1. Preparing Raspberry Pi OS.
Plug in the microSD card that you will be using as your boot drive for the Raspberry Pi, then install and open [pi imager](https://www.raspberrypi.com/software/) on your pc.  
1. Choose Model  
  -Raspberry pi 5 (in my case)
2. Choose OS  
  -Install the Raspberry Pi OS Lite (64-bit) because it is a headless version of Pi OS that comes with Debian packages.
3. Choose Storage  
  -SDXC card (or M.2 drive)  
   -NOTE: choose the correct drive because it will rewrite and erase everything on that drive.
4. Enter a host name  
  -My host name will be pi-vpn.  
5. Enter personal customization info such as:  
  -Username  
  -Password  
  -Wi-fi  
   -not necessary if connected via ethernet/LAN.  
  -Enable SSH as this will be your main way of controlling the Pi if not using raspberry pi connect.  
  -Enable Raspberry Pi connect in case of any internal network configurations that prevent you from communicating with the Pi.  
   (I wanted to ssh into my pi from my desktop that was connected via eth interface instead of wi-fi so I could not ssh until I bought a switch to connect the Pi to the eth interface.)  
6. Once you have written the drive and slotted the microSD card into the Pi. You will need to turn it on and configure a DHCP reservation on your router.  
  -NOTE: if the pi is connected to Wi-fi via mesh network such as google mesh, you will need to do the DHCP reservation on the mesh network management interface such as google home instead of through the router.  
  -The Pi can be found by looking for the hostname given to it on step 1.4.  
### 2. Installing and configuring PiVPN.  
1. Open a terminal into Pi via ssh (username@hostname.local) or through raspberry pi connect and enter this command the following command to install PiVPN.
```bash
curl -L https://install.pivpn.io | bash
```
2. You will be presented with the following screen.
<img width="1081" height="571" alt="image" src="https://github.com/user-attachments/assets/cd13e8ff-c204-4345-afb0-29c571953442" />  

3. Choose an interface for IPv4 and IPv6  
  -I chose eth0 for both  
4. It will ask you if you have already reserved an IP for the Pi, hit yes if already done so in step 1.6.  
5. Choose the user you configured your pi with in step 1.5. (should only be one user)  
6. Choose WireGuard or OpenVPN.  
  -Choice: WireGuard  
  -Performance: Runs at the kernel level, providing significantly faster speeds and lower overhead than OpenVPN.  
  -Security: Enforces strict public-key authentication, preventing brute-force password attacks.  
7. Enter the WireGuard port.  
  -We can keep the default port 51820 or change it if needed. Keep this port number in mind as we will be adding a port-forwarding rule in our router.  
8. Select DNS Provider  
  -I chose Google. While many people prefer Cloudflare, I'd rather avoid them after their outage.  
  -If you have your own DNS Server, you would hit custom. If you wanted to connect this to your own DNS, I recommend doing further research on this step.  
9. Public IP or DNS  
  -Choose Public IP (sensitive info) if your ISP provides a static IP address that rarely changes.  
  -Choose DNS Entry (Dynamic DNS / DDNS) if your ISP assigns a dynamic public IP that changes periodically. Using a free DDNS hostname (such as DuckDNS or No-IP) ensures your VPN clients can always locate your home network even if your IP address updates.  
10. Enable unattended upgrades.  
  -Since the Wireguard port is exposed to the internet, we want to ensure that the latest patches are installed at all times.  
11. Lastly, reboot the Pi when prompted.  



### 3. Created a WireGuard client profile.
### 4. Configured router port forwarding.
### 5. Established a remote VPN connection.
### 6. Configured RustDesk for remote desktop access through the VPN.

## Testing and Validation

- Verified successful WireGuard connectivity.
- Verified remote access to the home network.
- Successfully accessed the Windows desktop through RustDesk.

Additional firewall and security validation will be documented as the project progresses.

## Security Considerations

- WireGuard provides encrypted communication.
- Remote desktop access is performed through the VPN.
- VPN peer access and firewall configurations will be reviewed.
- Private keys and sensitive configuration information are excluded from this repository.

## Future Improvements

- Review and harden firewall rules.
- Implement least-privilege network access.
- Document VPN peer revocation procedures.
- Integrate pfSense for network segmentation.
- Expand the environment with isolated VirtualBox cybersecurity lab machines.
