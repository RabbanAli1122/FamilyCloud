# FamilyCloud - Your Own Home Family Drive Server

**Break free from Big Tech's limited storage and ridiculous prices. Build your own family cloud server at home for a fraction of the cost.**

## What is This Project?

FamilyCloud is a complete guide to building your own private cloud storage server at home using an old PC and Nextcloud. Instead of paying monthly fees to Google Drive, iCloud, or Dropbox with their limited storage (15GB, 5GB, 2GB free), you can have **unlimited storage** limited only by the hard drive you install.

This project was built using:
- An old Lenovo ThinkCentre (Core i3 2nd gen, 8GB RAM) - basically a "potato PC"
- A 1TB hard drive (you can use any size - 500GB, 2TB, 4TB, whatever you have)
- Free/cheap software and services
- **Total setup time: ~2-3 hours**

## Features

- **Complete data ownership** - Your files stay in your home, not in some corporate datacenter
- **One-time cost** - No monthly subscriptions (except ~$8/year for domain)
- **Automatic phone backup** - Photos, contacts, calendar sync just like iCloud/Google Photos
- **Cross-platform** - Works on Android, iPhone, Windows, Mac, Linux
- **Access from anywhere** - Securely access your files from anywhere in the world
- **Multi-user support** - Separate accounts for each family member
- **Private & secure** - End-to-end encrypted access via HTTPS

## Table of Contents

1. [What You'll Need](#what-youll-need)
2. [Part 1: Installing Ubuntu Server](#part-1-installing-ubuntu-server)
3. [Part 2: Initial Server Setup](#part-2-initial-server-setup)
4. [Part 3: Installing Nextcloud](#part-3-installing-nextcloud)
5. [Part 4: Adding Storage Drive](#part-4-adding-storage-drive)
6. [Part 5: Making it Accessible from Anywhere (Cloudflare)](#part-5-making-it-accessible-from-anywhere-cloudflare)
7. [Part 6: Setting Up Automatic Backups](#part-6-setting-up-automatic-backups)
8. [Troubleshooting](#troubleshooting)
9. [What's Next (v2 Features)](#whats-next-v2-features)

---

## What You'll Need

### Hardware
- **Old PC/Laptop**: Literally any old computer that can boot
  - Minimum: Dual-core CPU, 2GB RAM (4GB recommended)
  - My setup: Lenovo ThinkCentre, Core i3 2nd gen, 8GB RAM (overkill for this)
  - Even a Raspberry Pi 4 works!
- **Storage Drive**: Any size HDD or SSD for storing your files
  - 500GB, 1TB, 2TB, 4TB - whatever fits your needs and budget
  - I used a healthy used 1TB HDD
- **Network Connection**: 
  - Ethernet cable (recommended for stability)
  - OR USB WiFi dongle (I used TP-Link dongle as built-in card didn't support Linux)
- **USB Flash Drive**: 8GB+ for Ubuntu installation

### Software (All Free)
- Ubuntu Server 24.04 LTS ISO
- Balena Etcher (for creating bootable USB)
- Nextcloud (will install via snap)

### Services
- **Cloudflare Account** (free)
- **Domain Name** (~$8-12/year) - We'll use Cloudflare domains

### Optional
- Monitor + Keyboard (only needed during initial setup)
- Once configured, you can run it headless (no monitor)

---

## Part 1: Installing Ubuntu Server

### Step 1.1: Download Ubuntu Server

1. Go to https://ubuntu.com/download/server
2. Download **Ubuntu Server 24.04 LTS** (Long Term Support)
3. Save the ISO file (~2.5GB)

### Step 1.2: Create Bootable USB

1. Download Balena Etcher: https://etcher.balena.io/
2. Insert your USB flash drive (8GB+)
3. Open Balena Etcher
4. **Flash from file** → Select the Ubuntu ISO you downloaded
5. **Select target** → Choose your USB drive
6. **Flash!** → Wait for it to complete (~5-10 minutes)

### Step 1.3: Boot from USB

1. Insert the USB drive into your old PC
2. Power on the PC and immediately press the boot menu key:
   - **Lenovo/ThinkPad**: F12 or F1
   - **HP**: F9 or Esc
   - **Dell**: F12
   - **ASUS**: F8 or Esc
   - If unsure, Google: "boot menu key [your PC model]"
3. Select your USB drive from the boot menu
4. You'll see the Ubuntu installer screen

### Step 1.4: Install Ubuntu Server

Follow the installation wizard:

1. **Language**: Select your language (English)

2. **Keyboard**: Select your keyboard layout

3. **Type of install**: Choose "Ubuntu Server"

4. **Network connections**: 
   - If using Ethernet: Should auto-detect
   - If using WiFi dongle: Select your WiFi network and enter password
   - **Important**: Note the IP address shown (e.g., 192.168.1.50) - you'll need this later

5. **Proxy**: Leave blank, press Enter

6. **Mirror**: Use default, press Enter

7. **Storage configuration**: 
   - Choose "Use an entire disk"
   - Select your system disk (usually the smallest drive - NOT your storage drive)
   - Confirm "Continue"

8. **Profile setup**: Create your account
   - Your name: (e.g., Rabban)
   - Server name: **jarvis** (or any name you like)
   - Username: (e.g., rabban)
   - Password: Choose a strong password
   - **Write these down!**

9. **SSH Setup**: 
   - Select "Install OpenSSH server" (use spacebar to select)
   - This allows you to control the server from another computer

10. **Featured Server Snaps**: 
    - Don't select anything, just press "Done"

11. **Installation**: 
    - Wait for installation to complete (~10-15 minutes)
    - When finished, select "Reboot Now"
    - Remove the USB drive when prompted

12. **First Boot**:
    - Wait for the login prompt
    - Login with your username and password

**Congratulations! Ubuntu Server is now installed!**

---

## Part 2: Initial Server Setup

### Step 2.1: Update the System

After logging in, run these commands:

```bash
sudo apt update
sudo apt upgrade -y
```

This will take 5-10 minutes. Let it complete.

### Step 2.2: Set Static IP Address

Your server needs a fixed IP address so you can always find it on your network.

**Important**: This process varies by router. 

**Option A: Set Static IP in Ubuntu (Recommended if you're comfortable with terminal)**

1. Find your network interface name:
```bash
ip a
```
Look for your connection (like `eth0` for Ethernet or `wlx7820516a979c` for WiFi)

2. Edit the netplan configuration:
```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

3. Replace the contents with (adjust values for your network):
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:  # Replace with your interface name
      dhcp4: no
      addresses:
        - 192.168.100.44/24  # Your desired static IP
      routes:
        - to: default
          via: 192.168.100.1  # Your router's IP (usually 192.168.1.1 or 192.168.0.1)
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

**Important IP Address Rules**:
- First three numbers should match your router (e.g., if router is 192.168.1.1, use 192.168.1.X)
- Last number (X) should be between 2-254 and NOT used by another device
- Common router IPs: 192.168.1.1, 192.168.0.1, 192.168.100.1, 10.0.0.1

4. Save and exit:
   - Press `Ctrl+X`
   - Press `Y` to confirm
   - Press `Enter`

5. Apply the changes:
```bash
sudo netplan apply
```

6. Verify your IP:
```bash
ip a
```

**Option B: Set Static IP in Router (Easier for beginners)**

**This is highly recommended as it's more reliable and survives Ubuntu reinstalls.**

Search on YouTube: **"how to set static ip on [your router brand]"**

Example searches:
- "how to set static ip on TP-Link router"
- "how to set static ip on Netgear router"  
- "how to set static ip on ASUS router"

You'll need to:
1. Log into your router's admin panel (usually 192.168.1.1 or 192.168.0.1)
2. Find DHCP reservation or Static IP settings
3. Assign a static IP to your server's MAC address

### Step 2.3: Test SSH Access (Optional but Recommended)

Now you can control your server from your laptop/desktop and disconnect the monitor.

**From your laptop (Windows/Mac/Linux):**

Windows (using PowerShell or Command Prompt):
```bash
ssh rabban@192.168.100.44
```

Mac/Linux (using Terminal):
```bash
ssh rabban@192.168.100.44
```

Replace:
- `rabban` with your username
- `192.168.100.44` with your server's IP

Enter your password when prompted.

**Success?** You can now disconnect the monitor and keyboard from the server. It will run headless!

---

## Part 3: Installing Nextcloud

### Step 3.1: Install Nextcloud via Snap

This is the easiest method - literally one command:

```bash
sudo snap install nextcloud
```

Wait 2-3 minutes for installation to complete.

### Step 3.2: Create Admin Account

```bash
sudo nextcloud.manual-install admin YourStrongPasswordHere
```

Replace `YourStrongPasswordHere` with a strong password for your admin account.

**Write down these credentials!**

### Step 3.3: Configure Trusted Domains

Allow access from your local IP:

```bash
sudo nextcloud.occ config:system:set trusted_domains 1 --value=192.168.100.44
```

Replace `192.168.100.44` with your server's IP.

### Step 3.4: Test Local Access

Open a web browser on your laptop and go to:
```
http://192.168.100.44
```

**You should see the Nextcloud login page!**

Login with:
- Username: `admin`
- Password: (the one you set in Step 3.2)

---

## Part 4: Adding Storage Drive

By default, Nextcloud only has access to your system disk (which might be small). Let's add your large storage drive.

### Step 4.1: Connect Your Storage Drive

1. Shut down the server:
```bash
sudo shutdown now
```

2. Connect your storage drive (HDD/SSD) to the PC
   - If using an old CD-ROM SATA port, remove the CD-ROM and connect your drive there

3. Power on the server

4. SSH back in

### Step 4.2: Identify Your Drive

```bash
lsblk
```

You'll see output like:
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 298.1G  0 disk 
├─sda1   8:1    0     1G  0 part /boot/efi
└─sda2   8:2    0   297G  0 part /
sdb      8:16   0 931.5G  0 disk 
```

Your storage drive is the one with NO mountpoints and the size you expect (in this example, `sdb` is 1TB).

**WARNING**: Make sure you identify the correct drive! The wrong drive contains your system.

### Step 4.3: Check Drive Health (Optional but Recommended)

Install smartmontools:
```bash
sudo apt install smartmontools -y
```

Check drive health (replace `sdb` with your drive):
```bash
sudo smartctl -a /dev/sdb
```

Look for:
- `SMART overall-health self-assessment test result: PASSED` (Good)
- Check `Reallocated_Sector_Ct` - should be 0 or very low
- Check `Current_Pending_Sector` - should be 0

If the drive shows many errors, consider using a different drive.

### Step 4.4: Format the Drive

**WARNING: THIS WILL ERASE EVERYTHING ON THE DRIVE!**

```bash
sudo wipefs -a /dev/sdb
sudo mkfs.ext4 /dev/sdb -L "cloud-storage"
```

Replace `sdb` with your drive identifier.

### Step 4.5: Create Mount Point and Mount

```bash
sudo mkdir -p /mnt/cloud-storage
sudo mount /dev/sdb /mnt/cloud-storage
```

Verify it's mounted:
```bash
df -h /mnt/cloud-storage
```

You should see your drive size and available space.

### Step 4.6: Make it Mount Automatically on Boot

1. Get the drive's UUID:
```bash
sudo blkid /dev/sdb
```

Copy the UUID (looks like: `3b966f02-8352-40ce-80a4-2462bcb28889`)

2. Add it to fstab:
```bash
echo "UUID=YOUR-UUID-HERE /mnt/cloud-storage ext4 defaults 0 2" | sudo tee -a /etc/fstab
```

Replace `YOUR-UUID-HERE` with the actual UUID.

3. Test auto-mount:
```bash
sudo umount /mnt/cloud-storage
sudo mount -a
df -h /mnt/cloud-storage
```

If it shows mounted, you're good!

4. Reload systemd:
```bash
sudo systemctl daemon-reload
```

### Step 4.7: Connect Snap to External Drive

Snap runs in a confined environment and needs permission to access `/mnt`:

```bash
sudo snap connect nextcloud:removable-media
```

### Step 4.8: Configure Nextcloud to Use New Drive

```bash
sudo nextcloud.occ maintenance:mode --on
sudo nextcloud.occ config:system:set datadirectory --value="/mnt/cloud-storage/data"
```

Create the data directory structure:
```bash
sudo mkdir -p /mnt/cloud-storage/data
echo "# Nextcloud data directory" | sudo tee /mnt/cloud-storage/data/.ncdata
sudo chown -R root:root /mnt/cloud-storage/data/
sudo chmod -R 770 /mnt/cloud-storage/data/
```

Turn off maintenance mode:
```bash
sudo nextcloud.occ maintenance:mode --off
```

Scan the new directory:
```bash
sudo nextcloud.occ files:scan --all
```

### Step 4.9: Verify Storage

Check your admin user info:
```bash
sudo nextcloud.occ user:info admin
```

You should see your large storage drive in the quota section!

---

## STOP HERE if You Only Want LAN Access

**If you only need to access your Nextcloud from your home network (not from outside), you can stop here!**

What you have now:
- Working Nextcloud server accessible at `http://192.168.100.44` (or your server's IP)
- All family members can access it when connected to home WiFi
- Automatic phone/computer backups work on home network
- Complete privacy - data never leaves your home

This is perfect if:
- All family members live in the same house
- You don't need access when away from home
- You want maximum privacy (no external services involved)
- You want to save the $8/year domain cost

**To continue and make it accessible from anywhere in the world (via mobile data, work WiFi, etc.), proceed to Part 5.**

---

## Part 5: Making it Accessible from Anywhere (Cloudflare)

Right now, your Nextcloud only works on your home network. Let's make it accessible from anywhere in the world securely.

### Why Not Free Options?

I tried several free methods before settling on a paid domain. Here's what I found:

**DuckDNS (Free)**: 
- Provides free subdomains like `yourname.duckdns.org`
- **Problem with Cloudflare Tunnel**: Doesn't allow changing nameservers, so it doesn't work with Cloudflare Tunnel
- Cannot create DNS records required for Cloudflare routing

**EU.org (Free)**:
- Free domain names
- **Problem**: 4-8 week waiting period with 60-70% approval rate
- Too slow for getting started

**InfinityFree (Free)**:
- Free hosting with subdomains
- **Problem**: No nameserver control, same issue as DuckDNS

**Cloudflare Domain ($8-12/year)**:
- Works perfectly with Cloudflare Tunnel
- Cheap annual fee (~$8-12 depending on domain)
- Renews at the same price (no surprise increases)
- Full control over DNS
- **Built-in DDoS protection** - Cloudflare has massive bandwidth to absorb attacks
- **This is what I strongly recommend for external access**

**Why DDoS Protection Matters:**

If you expose your home server directly to the internet (without Cloudflare):
- Anyone can flood your home internet connection with traffic
- Your home upload speed (typically 5-50 Mbps) can be overwhelmed in seconds
- A basic DDoS attack uses 100+ Mbps, instantly taking you offline
- **No other free service provides DDoS protection** - it requires massive infrastructure
- Cloudflare has 172+ Tbps of network capacity to absorb attacks for you
- Without this protection, a single malicious person can make your server (and entire home internet) unusable

**Free Alternative That Works (With Limitations):**

There IS a free method that worked, but with significant trade-offs:

**DuckDNS + Tailscale Funnel** (Free):
- Use DuckDNS for a free subdomain
- Use Tailscale Funnel instead of Cloudflare Tunnel
- Tailscale Funnel allows any domain including free ones
- **Advantages**: Completely free, no domain cost
- **Disadvantages**: 
  - Ugly URL (e.g., `https://jarvis.tail36521.ts.net` or DuckDNS subdomain)
  - No DDoS protection - your home connection is exposed
  - If attacked, your entire home internet goes down
  - Cannot use custom professional domain easily
  - Less polished than Cloudflare solution

**My Recommendation:** Spend the $8/year for a Cloudflare domain. The DDoS protection alone is worth it, and you get a professional-looking domain name for your family.

### Understanding Bottlenecks

Before we proceed, understand that your access speed from outside depends on:

1. **Your home upload speed** (biggest factor):
   - If you have 5 Mbps upload, that's your maximum speed anywhere
   - Check your upload speed at https://fast.com or https://speedtest.net
   - Common home internet: 10-50 Mbps upload

2. **Your router's processing power**:
   - Older routers can become bottlenecks
   - If router is slow, consider upgrading or using Cloudflare Tunnel (bypasses router entirely)

3. **Your server's disk speed**:
   - HDD: ~100-150 MB/s read/write
   - SSD: ~500+ MB/s read/write
   - Usually not the bottleneck

### Step 5.1: Install Cloudflare Tunnel

```bash
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb
```

### Step 5.2: Create Cloudflare Account

1. Go to https://dash.cloudflare.com/sign-up
2. Create a free account
3. Verify your email

### Step 5.3: Buy a Domain (Recommended)

1. In Cloudflare dashboard, go to **Domain Registration** → **Register Domain**
2. Search for a domain name (e.g., `familycloud.cc`, `mycloud.cc`, `smithfamily.com`)
3. Pick one that's ~$8-12/year
4. Complete the purchase
5. Wait 5-10 minutes for domain to activate

**Note**: `.cc`, `.xyz`, `.online` domains are usually cheapest ($8-10/year)

### Step 5.4: Create Cloudflare Tunnel

1. In Cloudflare dashboard, go to **Zero Trust** → **Networks** → **Tunnels**
2. Click **Create a tunnel**
3. Choose **Cloudflared**
4. Name your tunnel (e.g., "home-nextcloud")
5. Click **Save tunnel**
6. You'll see a token - **don't close this page yet!**

### Step 5.5: Connect Tunnel to Your Server

Copy the installation command shown (looks like):
```bash
sudo cloudflared service install eyJhIjoiYT...VERY_LONG_TOKEN...
```

Paste and run it in your server terminal.

Verify tunnel is running:
```bash
sudo systemctl status cloudflared
```

Should show "active (running)".

### Step 5.6: Configure Public Hostname

1. Back in Cloudflare dashboard (tunnel creation page)
2. Click **Next** or **Add a public hostname**
3. Configure:
   - **Subdomain**: Leave blank (or put `www`)
   - **Domain**: Select your domain
   - **Type**: HTTP
   - **URL**: `localhost:80`
4. Click **Save tunnel**

### Step 5.7: Add Domain to Nextcloud Trusted Domains

```bash
sudo nextcloud.occ config:system:set trusted_domains 2 --value=yourdomain.com
sudo nextcloud.occ config:system:set trusted_domains 3 --value=www.yourdomain.com
```

Replace `yourdomain.com` with your actual domain.

### Step 5.8: Test External Access

1. Open a web browser (can be on your phone using mobile data - NOT your home WiFi)
2. Go to `https://yourdomain.com`
3. **You should see your Nextcloud login page!**

**Important**: Use HTTPS (not HTTP). Cloudflare provides free SSL automatically.

### Step 5.9: Create User Accounts for Family

Now create accounts for your family members:

Via web interface:
1. Login as admin at https://yourdomain.com
2. Click your profile picture (top right) → **Users**
3. Click **New user**
4. Fill in: Username, Display name, Email, Password
5. Click **Add user**

Or via command line:
```bash
sudo nextcloud.occ user:add username
```

**Optional**: Set storage quotas per user so one person can't fill the entire drive:

```bash
sudo nextcloud.occ user:setting username files quota 100GB
```

---

## Part 6: Setting Up Automatic Backups

Now let's configure automatic backups for phones and computers.

### 6.1: Android Phone Backup

#### Download Nextcloud App

1. Open Google Play Store
2. Search for "Nextcloud"
3. Install the official Nextcloud app (by Nextcloud GmbH)

#### Setup Auto-Upload (Photos/Videos)

1. Open the Nextcloud app
2. Login with:
   - Server: `https://yourdomain.com`
   - Username: your username
   - Password: your password
3. Go to **Settings** (☰ menu) → **Auto Upload**
4. Enable **Camera** folder
5. Configure:
   - **Remote folder**: `/Phone Backup/Camera` (or any name you prefer)
   - **Upload on**: WiFi only (to save mobile data)
   - **Upload videos**: Enable if you want
6. Tap **Add** to enable auto-upload
7. Repeat for other folders like Screenshots if needed

#### Setup Contacts & Calendar Backup

In the Nextcloud app:
1. Go to **Settings** → **Automatic Upload**
2. Scroll down to **Contacts & Calendar**
3. Enable:
   - **Contacts**: ON
   - **Calendar**: ON/OFF (your choice)
4. Tap **Back up now** for the first backup
5. It will automatically backup daily from now on

**Where are contacts stored?**
- On your server at: `/mnt/cloud-storage/data/username/files/Contacts/`
- As a `.vcf` file (vCard format)
- You can see it in the web interface too

### 6.2: iPhone Backup

#### Download Nextcloud App

1. Open App Store
2. Search for "Nextcloud"
3. Install the official Nextcloud app

#### Setup Auto-Upload

1. Open the Nextcloud app
2. Login with your server details
3. The app will ask for photo library access - **Allow**
4. Go to **Settings** → **Auto Upload**
5. Enable **Photo Library**
6. Configure:
   - **Remote folder**: `/Phone Backup/Photos`
   - **Upload on**: WiFi only
7. Same settings for **Contacts** and **Calendar** backup

### 6.3: Windows Laptop Backup

#### Download Desktop Client

1. Go to https://nextcloud.com/install/#install-clients
2. Download **Desktop Client for Windows**
3. Run the installer

#### Setup Sync

1. Open the Nextcloud desktop app
2. Enter your server: `https://yourdomain.com`
3. Login with your credentials
4. Choose folders to sync:
   - Desktop
   - Documents
   - Downloads
   - Pictures
   - Or create a custom "Backup" folder
5. Click **Connect**

The sync folder will appear in your File Explorer. Anything you put there syncs automatically!

### 6.4: Mac Laptop Backup

#### Download Desktop Client

1. Go to https://nextcloud.com/install/#install-clients
2. Download **Desktop Client for macOS**
3. Run the installer

#### Setup Sync

Same as Windows:
1. Open the Nextcloud app
2. Enter server: `https://yourdomain.com`
3. Login
4. Choose folders to sync:
   - Desktop
   - Documents
   - Downloads
   - Pictures
5. Click **Connect**

**Note**: This is NOT a Time Machine backup. It only syncs files you choose. For full Mac backups with Time Machine, you'd need to set up the server as a NAS/SMB share (more complex, consider for v2).

### 6.5: How to Restore on New Phone/Computer

#### New Phone

1. Set up phone normally (skip Google/iCloud restore)
2. Install Nextcloud app
3. Login with your credentials
4. Your files and photos are instantly accessible
5. For contacts:
   - Android: Install Nextcloud app → Settings → Contacts & Calendar → Restore from backup
   - iPhone: Settings in Nextcloud app → Import contacts

#### New Computer

1. Install Nextcloud desktop client
2. Login with your server
3. Choose where to sync files
4. Everything downloads automatically

**Note**: Apps, app data, and system settings are NOT backed up by Nextcloud. You'll need to reinstall apps manually. Nextcloud is best for files, photos, contacts, and calendar.

---

## Troubleshooting

### Issue: Can't SSH into server

**Solution:**
```bash
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh
```

### Issue: Server not accessible from local network

1. Check if Nextcloud is running:
```bash
sudo snap services nextcloud
```

2. Restart if needed:
```bash
sudo snap restart nextcloud
```

3. Verify IP address:
```bash
ip a
```

### Issue: "Your data directory is invalid" error

**Solution:**
```bash
echo "# Nextcloud data directory" | sudo tee /mnt/cloud-storage/data/.ncdata
sudo chmod 770 /mnt/cloud-storage/data/
```

### Issue: Can't access external drive (Permission denied)

**Solution:**
```bash
sudo snap connect nextcloud:removable-media
sudo chown -R root:root /mnt/cloud-storage/data/
sudo chmod -R 770 /mnt/cloud-storage/data/
```

### Issue: Cloudflare tunnel not connecting

1. Check tunnel status:
```bash
sudo systemctl status cloudflared
```

2. Restart tunnel:
```bash
sudo systemctl restart cloudflared
```

3. Check logs:
```bash
sudo journalctl -u cloudflared -f
```

### Issue: "Failed to open directory: Input/output error"

This indicates disk issues. Check drive health:
```bash
sudo smartctl -a /dev/sdb
```

Look for:
- High `Reallocated_Sector_Ct`
- High `Current_Pending_Sector`
- SMART status: FAILED

If the drive is failing, back up immediately and replace the drive.

### Issue: Auto-upload not working on phone

1. Check app permissions:
   - Android: Settings → Apps → Nextcloud → Permissions → Allow all
   - iPhone: Settings → Nextcloud → Allow Photos access

2. Check auto-upload settings in app:
   - Ensure "Upload on WiFi only" is OFF if you want to use mobile data
   - Or connect to WiFi

3. Force a sync:
   - Pull down on file list to refresh
   - Or Settings → Clear cache

### Issue: Phone app showing deleted files

Clear app cache:
1. Settings → Clear cache
2. Pull down to refresh
3. Or reinstall the app (your data is safe on the server)

### Issue: Nextcloud is slow

Common causes:
1. **Upload speed bottleneck**: Check your home upload speed at fast.com
2. **Old router**: Consider upgrading router or ensure it has enough resources
3. **Disk speed**: HDDs are slower than SSDs, but usually not the main bottleneck
4. **Server RAM**: Add more RAM if using <4GB

Optimizations:
```bash
# Increase PHP memory limit
sudo snap set nextcloud php.memory-limit=512M

# Restart Nextcloud
sudo snap restart nextcloud
```

### Issue: Out of space but drive should have space

Check actual usage:
```bash
df -h /mnt/cloud-storage
sudo du -sh /mnt/cloud-storage/data/*
```

Clear trash and versions:
```bash
sudo nextcloud.occ trashbin:cleanup --all-users
sudo nextcloud.occ versions:cleanup
```

### Issue: Forgot admin password

Reset it:
```bash
sudo nextcloud.occ user:resetpassword admin
```

### Issue: Can't login from new device

Add the device's IP to trusted domains:
```bash
sudo nextcloud.occ config:system:set trusted_domains 4 --value=yourdomain.com
```

### Issue: Server not auto-starting after reboot

Enable services:
```bash
sudo systemctl enable snapd
sudo snap start nextcloud
```

### Still Having Issues?

1. Check Nextcloud logs:
```bash
sudo nextcloud.occ log:tail
```

2. Check system logs:
```bash
sudo journalctl -xe
```

3. Visit Nextcloud community forum: https://help.nextcloud.com/

---

## What's Next (v2 Features)

This project is actively being improved. Here are features planned for v2:

### Multi-Cloud Backup Strategy (Coming Soon)

Automatically backup your Nextcloud data to multiple free cloud providers for redundancy:
- Mega.nz (20GB free)
- Google Drive (15GB free)
- pCloud (10GB free)
- Icedrive (10GB free)
- And more...

**Total: 107GB+ free cloud backup storage!**

Using rclone for automated encrypted backups to protect against:
- Drive failure
- Accidental deletion
- Ransomware
- Physical damage (fire, flood)

This creates a true 3-2-1 backup strategy:
- 3 copies of data
- 2 different storage types (local + cloud)
- 1 offsite backup

### Additional Features Being Considered

- **Resource monitoring dashboard** - Monitor server CPU, RAM, disk usage
- **Two-factor authentication setup** - Extra security for logins
- **Email server integration** - Send sharing notifications
- **Media streaming** - Stream videos directly from your server
- **Automated backup scheduling** - Set it and forget it
- **Custom mobile apps** - Branded apps for your family
- **Collaborative editing** - Real-time document collaboration like Google Docs
- **Full-text search** - Search inside documents
- **Music server** - Like your private Spotify
- **Face recognition** - Auto-tag people in photos
- **GPS photo mapping** - See where photos were taken

Want to contribute? Open an issue or PR on GitHub!

---

## FAQ

### How much does this cost?

**One-time costs:**
- Old PC: Free (use existing) or ~$50-150 used
- Storage drive: $30-150 depending on size (500GB-4TB)
- USB WiFi dongle (if needed): $10-20

**Recurring costs:**
- Domain: ~$8-12/year (if you want external access)
- Electricity: ~$5-15/month (depends on your power costs and PC efficiency)

**Total first year**: ~$100-350 (with domain) or ~$80-200 (LAN-only)
**After first year**: ~$70-180/year (with domain) or ~$60-180/year (LAN-only)

Compare to Google One 2TB: $100/year (but only 2TB, and you don't own the hardware)

### Is my data really private?

Yes! Your data never touches Google, Dropbox, or any third-party servers. Everything is in your home.

**For LAN-only setup**: Complete privacy - data never leaves your home network.

**For external access via Cloudflare Tunnel**: 
- Your files stay on your server
- Cloudflare Tunnel does decrypt traffic momentarily for routing (DDoS protection requirement)
- They don't store your data, only see metadata during routing
- You can use end-to-end encryption in Nextcloud for extra security

### Can I use this without buying a domain?

**Yes, if you only want LAN access** - Stop at Part 4 and skip Parts 5-6. Perfect for families all living in the same house.

**For external access without paying for a domain** - See the "Free Alternative" section in Part 5. It uses Tailscale Funnel with DuckDNS (free) but has limitations compared to Cloudflare.

### What if my hard drive fails?

This is why the v2 multi-cloud backup is being developed. For now:
1. Use a reliable drive (check SMART health regularly)
2. Keep important files backed up elsewhere temporarily
3. Consider buying 2 drives and setting up RAID 1 (mirroring) for automatic redundancy

### Can I access this from my phone's mobile data?

**LAN-only setup**: No - only works on home WiFi

**With Cloudflare Tunnel or Tailscale Funnel**: Yes! You can access from:
- Home WiFi
- Mobile data (4G/5G)
- Friend's WiFi
- Coffee shop WiFi
- Anywhere with internet

---

## Contributing

Found an issue? Have a suggestion? Want to add a feature?

1. Open an issue on GitHub
2. Submit a pull request
3. Share your experience!

This project is for the community, by the community. Let's build the best self-hosted family cloud guide together!

---

## License

This project guide is released under the MIT License. You're free to use, modify, and distribute it.

The software used (Ubuntu, Nextcloud) have their own licenses:
- Ubuntu: Free and open source
- Nextcloud: AGPLv3 (free for personal use)

---

## Credits

Created by Rabban Ali with help from Farhan.

Special thanks to:
- Nextcloud community
- Ubuntu/Canonical
- Cloudflare for free tunnel service
- Everyone who helped troubleshoot issues

**If this guide helped you, please star the repo and share it with others who want to break free from Big Tech!**

---

## Support

Having trouble? Create an issue on GitHub with:
- Your hardware specs
- Ubuntu version (`lsb_release -a`)
- Error messages (full output)
- What you've already tried

The community will help you out!

---

**Remember: Your data, your rules. Stay independent!**
