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

1. Prepared Raspberry Pi OS.
2. Installed and configured PiVPN.
3. Created a WireGuard client profile.
4. Configured router port forwarding.
5. Established a remote VPN connection.
6. Configured RustDesk for remote desktop access through the VPN.

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
