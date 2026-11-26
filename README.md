<h1 align="center">Raspberry Pi Secure Remote Access VPN</h1>
<p align="center">
  <img width="658" height="330" alt="image" src="https://github.com/user-attachments/assets/1cbb1663-f723-42c1-8be2-30325057b87b" />
</p>

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

### 3. Generating and Configuring VPN Client Profiles.
In order for a client device to connect to the VPN server, it must have a WireGuard client configuration profile. PiVPN provides a built-in utility to generate these profiles for individual client devices.  
#### 3.1 Generate a Client Configuration Profile
1. On the Raspberry Pi VPN server, execute the following command to generate a new client profile:  
```bash
pivpn add
```
2. When prompted, enter a name for the client profile. This name will be used to identify the individual device within the PiVPN server configuration.  
3. Once the profile has been generated, PiVPN will create a WireGuard configuration file containing the settings required for the client device to establish a VPN connection.
<img width="891" height="340" alt="image" src="https://github.com/user-attachments/assets/3644c92c-b8af-4bc7-bfa5-6ef8b9961a96" />


#### 3.2 Transfer the Client Configuration  
After generating the client profile, transfer the configuration file to the intended client device using a secure file-transfer method.  
  
 - Security Consideration: The WireGuard configuration file contains cryptographic credentials, including the client's private key. Treat this file as sensitive information   and avoid sharing it through unsecured channels or storing it in publicly accessible locations.

To transfer the conf file from the PiVPN server to my current machine, I used scp given that we are transferring sensitive material over the network (even though the network we're setting up a vpn for is most likely a trusted network, its still good practice).  

<img width="1182" height="75" alt="image" src="https://github.com/user-attachments/assets/49f6d324-41af-4c68-b6b5-727bbdf190ee" />  

#### 3.3 Import the configuration file into WireGuard  
1. Install and open the WireGuard application on the client device.  
2. Select the option to import a tunnel from a file.  
3. Locate and select the previously transferred WireGuard configuration file.  
4. Once imported, the VPN tunnel will appear within the WireGuard application and can be enabled when needed.

  
### 4. Configuring router port forwarding.  
To allow remote client devices to reach the WireGuard VPN server from outside the home network, I configured a port-forwarding rule on my router.  

Port forwarding directs incoming traffic on a designated port to the Raspberry Pi VPN server on the local network. This allows external WireGuard clients to initiate a connection to the VPN server without exposing unrelated services on the network.  

1. Access the router's administrative interface.  
2. Navigate to the port-forwarding configuration settings.  
3. Create a port-forwarding rule for the WireGuard VPN server, directing incoming UDP traffic on port 51820 to the Raspberry Pi's local IP address.  
4. Save and apply the configuration.  

#### Port-Forwarding Configuration  

 | Setting | Value |
 | --- | --- |
 | Service | WireGuard |
 | Protocol | UDP |
 | External Port | 51820 |
 | Internal Port | 51820 |
 | Destination IP | Raspberry Pi's local IP address |
 
 - Security Consideration: Port forwarding exposes the designated service to incoming traffic from the internet. The forwarding rule was limited to the WireGuard service rather than exposing administrative services such as SSH or Remote Desktop directly to the internet.

### 5. Established a remote VPN connection.  
After configuring port forwarding and generating the client configuration file, you need to test the WireGuard connection by being on a different network. In this case, I disconnected from the Wi-Fi and connected to my phone's hotspot so I can verify that the WireGuard tunnel could be established outside the local network.  

1. Open the WireGuard application on the client device.  
2. Select the previously imported VPN tunnel.  
3. Activate the tunnel to initiate the connection to the PiVPN server.  
4. Verify that the connection is established and that the client is communicating through the VPN tunnel.  

Once the tunnel has been activated, you should be able to ping the Pi through its Wireguard interface.  

On the Pi run the command shown here to get the IP of the pi that WireGuard has provided:

<img width="890" height="46" alt="Screenshot 2026-11-24 175703" src="https://github.com/user-attachments/assets/12cf27b5-f189-402e-87c6-270b5d092e25" />  

While the client machine is connected to VPN tunnel, ping the Pi to confirm the Pi and the Client device is now officially in the same network.  

<img width="620" height="167" alt="image" src="https://github.com/user-attachments/assets/d93ac406-d524-4bcb-bba7-d8549cb4c1ae" />  
  
We should also see the IP given to the client machine by running the following command:  
  
<img width="667" height="51" alt="image" src="https://github.com/user-attachments/assets/3a000cd8-b958-4945-901c-468e5b81783e" />
  
### 6. Configured RustDesk for remote desktop access through the VPN.  
Now that the client device is connected to the home network, we should now be able to remote into our home desktop using rustdesk.  

1. Install [rustdesk](https://rustdesk.com/) on both the client device and the device you want to remote into.  
2. On the device you want to remote into, go to security settings and enable password authentication on the client device and set your desired password.
  
<img width="916" height="562" alt="image" src="https://github.com/user-attachments/assets/bc388bc4-0b98-4cc6-a5b4-0ba2e30d7f2c" />  
  
3. On the client device, open rustdesk and enter the private IP of the device you want to remote into.
  
<img width="472" height="247" alt="image" src="https://github.com/user-attachments/assets/7dc8dc98-5171-4a24-9a22-d1cd4099c9f0" />

Now that we have successfully remoted into our desired machine, we can now securely work on our homelab from anywhere with internet connection.  

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
