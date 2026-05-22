# pfSense Network Security Infrastructure 🛡️

> HA pfSense firewall deployment — CARP failover, Suricata IDS, pfBlockerNG threat intelligence, zone-based firewall policies, VPN, and network segmentation on VMware ESXi.

![pfSense](https://img.shields.io/badge/Firewall-pfSense-212121?style=flat)
![Suricata](https://img.shields.io/badge/IDS-Suricata-FF6600?style=flat)
![VMware](https://img.shields.io/badge/Platform-VMware_ESXi-607078?style=flat&logo=vmware)

---

## 🏗️ Topology

```
         WAN
          │
  ┌───────┴────────────────┐
  │                        │
pfSense Primary       pfSense Secondary
192.168.1.1           192.168.1.2
(Active)              (Standby)
  │    CARP VIP: 192.168.1.254    │
  └──────────┬─────────────┘
             │
   ┌──────┬──┴────┬────────┐
  LAN    DMZ    VPN      MGMT
```

---

## ⚙️ Key Configurations

**CARP HA:** Sub-3-second failover with pfsync state synchronisation — active sessions survive failover. Tested repeatedly under simulated failure conditions.

**Firewall Policy:**
```
LAN → WAN:   Allow HTTP/HTTPS, DNS only
WAN → DMZ:   Allow 80/443 to web server only
VPN → LAN:   Allow post-authentication
Default:     Implicit deny all
```

**OpenVPN:** Split tunnel, certificate auth, per-user IP binding, full connection logging.

**Suricata IDS:** ET Open ruleset on WAN; custom rules for port scans, DNS anomalies, outbound volume spikes. Tuned to ~95% true positive rate.

**pfBlockerNG:** 15+ IP reputation feeds, DNSBL ad/tracker blocking, GeoIP restrictions. Blocking ~300–500 reputation hits/day.

---

## 📊 Results

| Metric | Result |
|---|---|
| CARP failover time | < 3 seconds |
| Active sessions surviving failover | ✅ Yes (pfsync) |
| IP reputation blocks/day | ~300–500 |
| DNSBL blocks/day | ~1,200+ |
| IDS true positive rate | ~95% |

---

## 📚 Lessons Learned

- pfsync needs a dedicated interface — sharing with LAN is a security issue
- pfBlockerNG DNSBL occasionally blocks legitimate CDN domains — maintain a whitelist
- CARP VHID must be unique per VIP — collisions cause split-brain

---

## 👤 Author

**Moses Gnamisan Daniel** — Cloud Security & DevSecOps Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/moses-daniel-a8a80861/)
