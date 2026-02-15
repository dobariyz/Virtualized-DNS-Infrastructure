# Virtualized DNS Infrastructure Setup Guide

**Linux VM + Pi-hole + Tailscale**

## 1. Architecture Overview

```
Client Device (Laptop / Phone)
        ↓
     Tailscale VPN
        ↓
   Linux Virtual Machine
        ↓
       Pi-hole
        ↓
 Upstream DNS (Cloudflare 1.1.1.1)
```

This setup creates a virtualized DNS filtering system accessible both locally and remotely via Tailscale.

## 2. Environment Requirements

* Linux Virtual Machine (Ubuntu 22.04 recommended)
* Static private IP address
* SSH access
* Tailscale account
* Internet access

**VM can be hosted on:**
* Proxmox
* VirtualBox
* VMware
* AWS EC2 (restricted access)
* Any hypervisor

## 3. Assign Static IP to VM

Pi-hole requires a static IP inside the local network.

**Example:**
```
192.168.1.53
```

**Why static?**
* DHCP assigns dynamic IPs
* If VM IP changes, DNS breaks
* Router must always point to same DNS server

**Check IP:**
```bash
hostname -I
```

## 4. Install Pi-hole

**SSH into VM:**
```bash
ssh user@192.168.1.53
```

**Run official installer:**
```bash
curl -sSL https://install.pi-hole.net | bash
```

**During installation:**
* Select upstream DNS provider (Cloudflare recommended)
* Enable query logging
* Confirm static IP warning
* Complete installation

**Access dashboard:**
```
http://192.168.1.53/admin
```

## 5. Reset Admin Password (If Needed)

```bash
sudo pihole setpassword
```

## 6. Install Tailscale on VM

Add device from Tailscale admin console.

**On VM:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Authenticate device via provided URL.

**Confirm:**
```bash
tailscale status
```

## 7. Allow Pi-hole to Listen on Tailscale Interface

By default, Pi-hole listens only on local interface.

**To allow Tailscale traffic:**
1. Open Pi-hole Admin
2. Go to Settings → DNS
3. Enable Expert Mode
4. Select: `Permit all origins`
5. Save and apply

⚠️ **Note:** This is safe because access is restricted to Tailscale network.

## 8. Configure Tailscale DNS Override

**In Tailscale Admin Console:**
1. Go to DNS
2. Enable MagicDNS
3. Click Add Nameserver
4. Select Custom
5. Enter Tailscale IP of Pi-hole VM
6. Click Override DNS servers

Now all devices in the Tailnet use Pi-hole as DNS.

## 9. Testing

**Run DNS test:**
* Visit DNS leak test website
* Check Pi-hole Query Log
* Confirm queries appear from Tailscale clients

## 10. Optional: Local Router Configuration

To enable DNS filtering for entire LAN:
* Set router DNS to VM static IP
* Disable ISP DNS override if required

---

**End of Setup Guide**
