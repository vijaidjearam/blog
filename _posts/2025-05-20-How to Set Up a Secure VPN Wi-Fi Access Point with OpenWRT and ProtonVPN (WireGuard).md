---
layout: post
date: 2025-05-20 11:40
title: How to Set Up a Secure VPN Wi-Fi Access Point with OpenWRT and ProtonVPN (WireGuard)
category: openwrt
tags: openwrt vpn
---

In this guide, we'll walk through setting up an OpenWRT-based Wi-Fi access point that forces all connected clients to route their traffic through ProtonVPN using WireGuard. This ensures privacy, prevents DNS leaks, and blocks fallback to your ISP (kill switch).

---

## 🔧 Prerequisites

- OpenWRT-compatible router with internet access
- OpenWRT firmware installed (with LuCI interface)
- ProtonVPN account with WireGuard support

---

## 1. Install Required Packages

SSH into your OpenWRT router and run:

```sh
opkg update
opkg install luci-proto-wireguard wireguard-tools luci-app-wireguard kmod-wireguard
```

---

## 2. Configure WireGuard Interface

Use ProtonVPN's [WireGuard config generator](https://account.protonvpn.com/downloads) and enter the config into:

**LuCI > Network > Interfaces > Add new interface**

- Name: `wg0`
- Protocol: WireGuard VPN
- Assign firewall zone: `wgzone`

Add interface details from ProtonVPN:
- Private key
- Public key
- Endpoint IP/port
- Allowed IPs: `0.0.0.0/0`
- DNS server: `10.2.0.1` (or as provided)

**[Proton Doc: Link](https://protonvpn.com/support/openwrt-wireguard?srsltid=AfmBOop2S2GCaYziukzSN5rCfQFHAA3V75wPFxdwOaJwZdAY7Lqb0_QX)
---

## 3. Create a VPN-Only Wi-Fi Network

**LuCI > Network > Wireless > Add**

- SSID: `VPN-WiFi`
- Network: check **vpnlan** only

Then go to **Network > Interfaces > Add**:

- Name: `vpnlan`
- Protocol: Static
- IPv4 address: `192.168.100.1`
- Netmask: `255.255.255.0`
- Firewall zone: create new `vpnlan`

Enable DHCP:  
**Network > DHCP and DNS > Interfaces > vpnlan**

- Start: 100
- Limit: 150
- Lease time: 12h

---

## 4. Configure Firewall Zones

**LuCI > Network > Firewall > Zones**

Create zones:

- **vpnlan**: covers `vpnlan` network
  - Input: accept
  - Output: accept
  - Forward: reject
  - ✅ Masquerading
  - ✅ MSS Clamping

- **wgzone**: covers `wg0`
  - Input: reject
  - Output: accept
  - Forward: reject
  - ✅ Masquerading

**Forwarding Rules**:

- Allow `vpnlan ➝ wgzone`
- ❌ Do NOT allow `vpnlan ➝ wan`

---

## 5. Force DNS Over VPN

**Network > DHCP and DNS**:

- ✅ Ignore resolv file
- DNS Forwardings: `10.2.0.1` (or ProtonVPN DNS)

**Firewall > Traffic Rules**:

1. **Allow DNS to VPN**:

    - Source zone: `vpnlan`
    - Destination zone: `wgzone`
    - Destination IP: `10.2.0.1`
    - Port: 53
    - ✅ Accept

2. **Block All Other DNS**:

    - Source zone: `vpnlan`
    - Port: 53
    - ❌ Reject

---

## 6. Test the Setup

### ✅ DNS Leak Test

Connect to `VPN-WiFi`, visit [https://dnsleaktest.com](https://dnsleaktest.com)

You should only see ProtonVPN DNS (e.g., 185.x.x.x in Netherlands).

### ✅ Kill Switch Test

Temporarily bring down the VPN:

```sh
ifdown wg0
```

Clients on `VPN-WiFi` should lose all internet access.

---

## 🔒 Result

You now have a secure, VPN-only wireless access point. All connected devices are:

- Protected via ProtonVPN (WireGuard)
- Free of DNS leaks
- Kill-switched from WAN fallback

Enjoy secure browsing!