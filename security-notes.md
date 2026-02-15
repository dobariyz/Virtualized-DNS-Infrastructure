# Security Considerations – Virtualized DNS Infrastructure

This project involves running a DNS server inside a virtualized environment. DNS services must be secured properly to avoid misuse.

## 1. Static IP Requirement

Pi-hole must use a static private IP address.

**If DHCP assigns dynamic IP:**
* Router may point to wrong DNS server
* Network-wide resolution failure can occur

**Mitigation:**
* Reserve static IP in router
* Or configure static via Netplan

## 2. Do NOT Expose Port 53 Publicly

DNS (Port 53) must **NOT** be open to:

```
0.0.0.0/0
```

**Open resolvers can be abused for:**
* DNS amplification attacks
* DDoS participation
* Reflection attacks

**Mitigation:**
* Restrict DNS access to:
  * Local LAN
  * Tailscale interface only
* Use firewall rules (UFW or security groups)

## 3. Tailscale as Secure Access Layer

Instead of exposing DNS publicly:
* Use Tailscale private network
* Devices authenticate via encrypted tunnel
* Pi-hole only accessible inside Tailnet

**Benefits:**
* Zero public exposure
* Encrypted DNS transport
* Device-level authentication

## 4. "Permit All Origins" Risk

Enabling "Permit all origins" allows Pi-hole to listen on all interfaces.

**Risk:**
* Unsafe if server is publicly exposed

**Safe in this architecture because:**
* Server is not publicly accessible
* Access limited to private network interfaces

## 5. Upstream DNS Trust Model

Upstream DNS provider handles non-blocked queries.

**Example:**
* Cloudflare (1.1.1.1)

**Security Considerations:**
* Provider visibility of queries
* DNS privacy implications

**Optional enhancement:**
* Deploy Unbound locally for full recursive DNS

## 6. VM Isolation Benefits

Running Pi-hole inside a VM provides:
* Process isolation
* Network segmentation
* Easy rollback via snapshots
* Reduced blast radius

Compared to bare-metal installation, virtualization increases operational safety.

## 7. Recommended Hardening (Optional Improvements)

* Enable UFW firewall
* Disable password SSH login
* Use SSH key authentication
* Enable automatic security updates
* Backup Pi-hole configuration

## Conclusion

This project demonstrates secure deployment of a network-level DNS filtering service inside a virtualized environment, with controlled access via a private VPN overlay network.
