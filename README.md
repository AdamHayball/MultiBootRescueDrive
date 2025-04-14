# 🛠️ Ventoy Multi-Rescue NVMe Drive on ROG Strix Arion (Samsung 970 EVO 250GB)

This project details the creation of a **high-performance, durable, and consolidated Ventoy bootable drive** using an **ROG Strix Arion NVMe enclosure** paired with a **250GB Samsung 970 EVO** SSD. The purpose of this setup is to replace a scattered collection of USB sticks with a single, fast, and reliable drive that supports **BIOS and UEFI booting**, **secure boot**, **persistence**, and **portable applications** for both **Windows and Linux** environments.

---

<p align="center">
  <img src="https://github.com/AdamHayball/MultiBootRescueDrive/blob/main/usbsticks.jpg" alt="Old USB Sticks" height="200px" width="30%" />
  <img src="https://github.com/AdamHayball/MultiBootRescueDrive/blob/main/nvme.jpg" alt="NVMe in Enclosure" height="200px" width="30%" />
  <img src="https://github.com/AdamHayball/MultiBootRescueDrive/blob/main/drivelayout.jpg" alt="Drive Layout" height="200px" width="30%" />
</p>

---

## 🧰 What You’ll Need

- **NVMe enclsoure - ROG Strix Arion NVMe Enclosure**
- **NVMe Drive - Samsung 970 EVO 250GB NVMe SSD**
- Optionally you can perform this same setup on a good quality large capacity USB3.x thumbstick or sata drive, however you will see reduced performance
- A Linux or Windows host to run Ventoy and partition tools
- [Ventoy](https://www.ventoy.net/)
- (Optional) [VentoyPlugson](https://github.com/ventoy/VentoyPlugson) for persistence and secure boot
- Tools like `GParted`, `Rufus`, or `DiskGenius` (for partitioning if needed)
- Internet access for cloning additional Linux utilities and portable Windows apps
- Beware for **VERY** old systems a USB2.0 thumbstick, CD-R/W and even Floppy emulation is sometimes the best option, those use cases are outside of the scope of this project and will not be covered

---

## 💾 Partition Layout

The drive is initialized with an **MBR partition table** to maximize compatibility, especially with older legacy BIOS systems.

| Partition | Size         | Type       | Purpose                                                                 |
|-----------|--------------|------------|-------------------------------------------------------------------------|
| sdx1      | ~108GB       | exFAT      | `VENTOY` Primary Ventoy partition: Live ISOs (Windows/Linux), persistence files  |
| sdx2      | ~142GB       | exFAT      | `Portable` Additional data & tools: PortableApps (Windows), cloned Linux utilities |
| sdx3      | 32MB         | FAT16      | `VTOYEFI` – required by Ventoy for UEFI boot and Secure Boot support    |
| Unused    | Small buffer | -          | Small alignment buffer I left 8MB feel free to leave more              |

**Note:** The third user partition is placed at the *end* of the drive to ensure optimal compatibility with legacy BIOS environments, some of which misbehave with complex partitioning early on the disk and have a bug which will not allow the BIOS to read past 108GB.

---

## ⚙️ Features

- ✅ **Ventoy Bootloader**  
  Supports booting **multiple ISO/WIM/IMG/VHD(x)** files directly without needing to rewrite the drive.

- ✅ **Legacy BIOS & UEFI Compatible**  
  MBR layout ensures the drive boots on older BIOS systems **and** UEFI systems (with secure boot enabled).

- ✅ **Secure Boot Enabled**  
  Enabled via VentoyPlugson using Ventoy's signed UEFI bootloader in the `VTOYEFI` partition.

- ✅ **Persistence**  
  Select live Linux ISOs (e.g., Ubuntu, Kali, MX Linux) are configured with persistence via VentoyPlugson.

- ✅ **PortableApps (Windows)**  
  Second partition holds a **full PortableApps suite** to run Windows tools without installations.

- ✅ **Cloned Linux Git Tools**  
  Second partition also contains cloned Git repos of essential Linux tools/utilities like:
  - `rescuezilla`
  - `ventoy`
  - `testdisk`
  - `gparted`
  - `ddrescue-gui`
  - `balena-etcher`

- ✅ **Fast, Durable, Easy to Update**  
  NVMe performance + USB 3.2 gen 2 = lightning-fast ISOs and tool access with SSD-level endurance.

---

## 🪛 Setup Instructions

### 1. Prepare the Drive

1. Install the NVMe drive and connect your enclosure to the system.
2. Use **GParted** or **Disk Management** to create an **MBR layout** while this step is not strictly necessary it is a peace of mind step to ensure drive is setup correctly.
3. Run the Ventoy installer with the correct flags (next step) which will also create the 32MB `VTOYEFI` partition automatically.

### 2. Install Ventoy

```bash
sudo ./Ventoy2Disk.sh -i -s -r 145407960 /dev/sdX
```

> 💡 This will create:
> - First partition: `VENTOY` exFAT ISO/data partition (~108GB)
> - Second partition: `VTOYEFI` FAT16 for UEFI/Secure Boot (32MB)
> - You may now manually create a third `Tools` exFAT partition (~142GB) at the end of the drive.

### 3. Enable Secure Boot and Persistence

Use [VentoyPlugson](https://github.com/ventoy/VentoyPlugson) to:

- Enable Secure Boot support
- Configure persistence for desired ISOs
- Optionally enable theme customization, auto installation scripts, etc.

### 4. Add ISOs and Persistence

Copy all your desired ISOs to the first partition:
- Windows 10/11 installers
- Linux distros (Ubuntu, Debian, Arch, etc.)
- Rescue tools (Rescuezilla, Clonezilla, Hiren's Boot, etc.)

For persistence, follow [Ventoy’s persistence guide](https://www.ventoy.net/en/plugin_persistence.html).

### 5. Setup the Tools Partition

- Format the second partition (at the end) as **exFAT**.
- Clone Linux tool repositories:

```bash
git clone https://github.com/rescuezilla/rescuezilla.git /mnt/tools/rescuezilla
```

- Install [PortableApps.com Platform](https://portableapps.com/) and configure:

```bash
X:\PortableApps\PortableApps.exe
```

---

## 📁 Example Folder Structure

```
/Ventoy (1st Partition)
├── ISO/
│   ├── Windows11.iso
│   ├── ubuntu-22.04.iso
│   ├── kali-linux.iso
│   └── rescuezilla.iso
├── ventoy.json
└──persistence.dat

/VTOYEFI (2nd Partition - Hidden)
├── EFI/
│   └── Boot/ (Ventoy UEFI boot files)

/Tools (3rd Partition)
├── PortableApps/
│   ├── PortableApps.exe
│   └── Apps/
├── linux-tools/
│   ├── rescuezilla/
│   ├── testdisk/
│   └── ddrescue-gui/
```

---

## 🎯 Why This Setup?

- **Speed & Reliability**: NVMe drive in a high-quality USB-C enclosure is vastly superior to flash drives.
- **One Drive to Rule Them All**: Consolidates all recovery/install tools and portable environments in one device.
- **Always Ready**: Works across machines, from vintage BIOS laptops to modern UEFI systems with secure boot.
- **Persistence**: Linux livesystems retain changes and tools between reboots.

---

## 🧩 Future Ideas

- Add **Ventoy auto-install scripts** for unattended Windows or Linux installs.
- Setup a **dual-bootable Linux or Windows IoT install** on the drive.
- Include **encrypted partitions** or `veracrypt` containers for secure data storage.

---

## 📌 Credits

- [Ventoy](https://github.com/ventoy/Ventoy)
- [VentoyPlugson](https://github.com/ventoy/VentoyPlugson)
- [PortableApps](https://portableapps.com/)
- All open-source rescue tools and distros used on the drive
