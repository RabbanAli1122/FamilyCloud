# Hardware Compatibility List

This document tracks hardware that has been successfully tested with the FamilyCloud setup.

## Tested and Working

### Desktop PCs

| Model | CPU | RAM | Notes | Contributor |
|-------|-----|-----|-------|-------------|
| Lenovo ThinkCentre | Intel Core i3 2nd Gen | 8GB | Built-in NIC didn't work, used TP-Link USB WiFi dongle | Rabban Ali |

### Network Adapters

| Model | Type | Interface | Notes | Contributor |
|-------|------|-----------|-------|-------------|
| TP-Link TL-WN722N (or similar) | WiFi | USB | Works out of the box with Ubuntu 24.04 | Rabban Ali |

### Storage Drives

| Model | Capacity | Type | Notes | Contributor |
|-------|----------|------|-------|-------------|
| Western Digital Blue (WD10EZEX) | 1TB | HDD 7200rpm | Used, 40K hours, SMART healthy | Rabban Ali |

### Routers

| Model | Notes | Contributor |
|-------|-------|-------------|
| [Huawei-HG8345R] | [Advanced Configuration > LAN configuration > DHCP Static IP Configuration] | [Rabban Ali] |

## Untested But Likely Compatible

### Raspberry Pi
- Raspberry Pi 4 (4GB or 8GB RAM) - Should work but slower than PC
- Raspberry Pi 5 - Should work, not yet tested

### Mini PCs
- Intel NUC (any generation)
- HP EliteDesk Mini
- Dell OptiPlex Micro
- Any mini PC with 2GB+ RAM

### Old Laptops
- Any laptop with working screen/keyboard for initial setup
- Can run headless after configuration
- Battery acts as built-in UPS

## Known Issues

### Network Adapters
- Some built-in network cards in older PCs may not have Linux drivers
- Solution: Use USB WiFi/Ethernet adapter (most work out of the box)

### Storage Controllers
- Very old IDE drives may not be recognized
- Solution: Use SATA drives (any SATA HDD/SSD should work)

## How to Contribute

Tested the guide on your hardware? Please contribute!

1. Fork the repository
2. Add your hardware to this file
3. Include:
   - Exact model names
   - What worked / didn't work
   - Any workarounds needed
   - Your GitHub username
4. Submit a Pull Request

**Format:**
```
| Model Name | Specs | Notes | Your GitHub Username |
```

## Hardware Recommendations

### For Small Family (2-4 users)
- **CPU**: Dual-core, 1.5GHz+
- **RAM**: 4GB minimum, 8GB comfortable
- **Storage**: 500GB+ HDD
- **Network**: Any Ethernet or working WiFi

### For Medium Family (5-8 users)
- **CPU**: Quad-core, 2GHz+
- **RAM**: 8GB minimum, 16GB comfortable
- **Storage**: 1TB+ HDD or SSD
- **Network**: Gigabit Ethernet preferred

### For Large Family (9+ users) or Heavy Use
- **CPU**: Quad-core+ with Hyper-Threading
- **RAM**: 16GB minimum
- **Storage**: 2TB+ SSD recommended
- **Network**: Gigabit Ethernet required

## Budget Recommendations

### Performance Build ($400-600)
- Refurbished business PC with good CPU ($200-300)
- 2-4TB SSD ($150-250)
- Quality accessories ($50)
- Domain ($8-12/year)

### Ultra-Budget (Under $100 total)
- Use a PC you already have lying around
- Buy used HDD (500GB-1TB: $20-40)
- USB WiFi dongle if needed ($10-15)
- Domain ($8-12/year)

### Comfortable Budget ($200-300)
- Refurbished mini PC ($100-150)
- New 1-2TB HDD ($50-80)
- Accessories if needed ($20-30)
- Domain ($8-12/year)

## Power Consumption

Typical power consumption and electricity costs:

| Hardware Type | Idle | Active | Monthly Cost (at $0.12/kWh) |
|---------------|------|--------|------------------------------|
| Raspberry Pi 4 | 3-4W | 6-8W | $0.30-0.50 |
| Mini PC | 10-15W | 25-35W | $1-2 |
| Old Desktop PC | 30-50W | 80-150W | $3-8 |
| Modern Desktop | 20-30W | 50-100W | $2-5 |

**Tip**: Use a Kill-A-Watt meter to measure your actual consumption.

## Noise Levels

If running 24/7 in living space, consider noise:

- **Silent**: Raspberry Pi, fanless mini PCs
- **Quiet**: Mini PCs, modern desktops with good cooling
- **Noisy**: Old desktops with worn fans, servers

**Tip**: Replace old fans or use fan speed controllers to reduce noise.

---

**Last Updated**: 2026-03-01

**Maintainer**: Rabban Ali

**Contributor(s)**: Rabban Ali
