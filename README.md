<!-- omit from toc -->
# Flipper Zero BadUSB Script Collection

Welcome to this comprehensive Flipper Zero BadUSB script collection! These scripts are designed for security testing and educational purposes. By downloading and using these files, you automatically agree to the MIT license and the terms outlined below.

## ⚠️ Disclaimer

**These scripts are for authorized security testing only.** Unauthorized access to computer systems is illegal. Only use these tools on systems you own or have explicit permission to test. The creator assumes no liability for misuse.

---

## 📋 Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Payload Descriptions](#payload-descriptions)
  - [DataGrabber.txt](#datagrabber-browser--wifi-credentials)
- [Configuration Guide](#configuration-guide)
- [Usage Examples](#usage-examples)
- [Decryption Tools](#decryption-tools)
- [Supported Systems](#supported-systems)
- [License](#license)
- [Support](#support)

---

## ✨ Features

- ✅ **Multiple Export Methods**: USB mass storage or Caps Lock binary encoding with automatic fallback
- ✅ **Browser Data Extraction**: Chrome, Firefox, Edge passwords, history, and cookies
- ✅ **WiFi Password Grabbing**: Extracts all saved WiFi networks and credentials
- ✅ **Error Handling**: Automatic fallback to Caps Lock encoding if USB not available
- ✅ **Flipper Zero Compatible**: Optimized syntax for Flipper Zero BadUSB module
- ✅ **Flexible Configuration**: Easy-to-modify defines for quick customization
- ✅ **Auto-Cleanup**: Removes temporary files after exfiltration
- ✅ **Base64 Encoding**: WiFi passwords encoded for safe transmission

---

## 📦 Installation

1. **Clone or download this repository:**
   ```bash
   git clone https://github.com/Mr-Ender8/Flipper_Zero-BadUsb.git
   ```

2. **Copy payload files to your Flipper Zero:**
   - Connect Flipper Zero to your computer
   - Navigate to `badusb/` folder
   - Copy `.txt` files to this directory
   - Eject and use via BadUSB app on Flipper

3. **Alternative: Use Flipper's file manager**
   - Open Flipper app
   - Navigate to BadUSB
   - Upload `.txt` files

---

## 🎯 Payload Descriptions

### DataGrabber - Browser & WiFi Credentials

**Purpose:** Extract sensitive data including browser passwords, history, cookies, and WiFi credentials

**Modes:**
- **Mode 1 (Default):** Try USB mass storage export first, fallback to Caps Lock binary encoding
- **Mode 2:** USB mass storage export only
- **Mode 3:** Caps Lock binary encoding only

**Configuration:**
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 50
```

**Extracted Data:**

| Source | Data | Location |
|--------|------|----------|
| **Chrome** | Passwords (Login Data), History, Cookies | AppData\Local\Google\Chrome |
| **Firefox** | Profiles, Cookies, Passwords | AppData\Roaming\Mozilla\Firefox |
| **Edge** | Passwords (Login Data), History, Cookies | AppData\Local\Microsoft\Edge |
| **WiFi** | Network SSIDs & Passwords | netsh wlan profiles |

**Features:**
- Auto-detects installed browsers
- Extracts all saved WiFi networks and passwords in plaintext
- Copies to Flipper mass storage (USB mode)
- WiFi passwords encoded as base64 for Caps Lock mode
- Auto-cleanup of temporary files
- Real-time status messages

**Output Files (USB Mode):**
```
chrome_passwords.db        (encrypted - needs decryption)
chrome_history.db          (encrypted)
chrome_cookies.db          (encrypted)
firefox_*.db               (SQLite format)
edge_passwords.db          (encrypted)
edge_history.db            (encrypted)
wifi_passwords.txt         (plaintext SSID:password)
```

**Output (Caps Lock Mode):**
- WiFi passwords encoded as binary pattern (1=Caps Lock toggle, 0=space)
- Easily decodable by recording Caps Lock LED state or keyboard input

---

## ⚙️ Configuration Guide

### Basic Configuration

Edit the `DEFINE` section at the top of the payload:

```text
REM ===== DEFINE CONFIGURATION =====
DEFINE EXPORT_MODE 1           # 1=USB+fallback, 2=USB only, 3=CapsLock only
DEFINE CAPS_LOCK_DELAY_MS 50   # Milliseconds between toggles
REM ===== END DEFINES =====
```

### Export Modes

| Mode | Method | Speed | Reliability | Best For |
|------|--------|-------|-------------|----------|
| **1** | USB First → Caps Lock | ⚡ Fast (USB) | ✅ High | Most situations |
| **2** | USB Only | ⚡ Very Fast | 🔶 Medium | Air-gapped targets |
| **3** | Caps Lock Only | 🐢 Slow | ✅ Very High | Always reliable |

**To use Mode 2 (USB only):**
```text
DEFINE EXPORT_MODE 2
```

**To use Mode 3 (Caps Lock only):**
```text
DEFINE EXPORT_MODE 3
```

### Adjusting for Target System

| Issue | Solution |
|-------|----------|
| Caps Lock too fast | Increase `CAPS_LOCK_DELAY_MS` to 75-100 |
| Caps Lock too slow | Decrease to 30-50 |
| USB not detected | Ensure Flipper is in mass storage mode |
| PowerShell blocked | Payload automatically falls back to Caps Lock |
| Files not found | Script skips missing files with error handling |

---

## 🚀 Usage Examples

### Example 1: Default - USB with Fallback
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 50
```
- Try USB first, fallback to Caps Lock if not available
- Good for most scenarios

### Example 2: USB Only (Fast)
```text
DEFINE EXPORT_MODE 2
DEFINE CAPS_LOCK_DELAY_MS 50
```
- Only exports via USB mass storage
- Fastest method but requires USB connection

### Example 3: Caps Lock Only (Reliable)
```text
DEFINE EXPORT_MODE 3
DEFINE CAPS_LOCK_DELAY_MS 75
```
- Only uses Caps Lock encoding
- Works on air-gapped systems
- Increased delay for reliability

### Example 4: Slow Network
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 100
```
- Higher delay for slow/unreliable targets
- Better success rate on laggy systems

---

## 🔐 Decryption Tools

Browser databases are encrypted. Use these tools to decrypt offline:

| Tool | Purpose | Target |
|------|---------|--------|
| **[ChromePass](https://www.nirsoft.net/utils/chromepass.html)** | Decrypt Chrome passwords | Chrome/Chromium |
| **[Edge Passwords Viewer](https://www.nirsoft.net/utils/edgepass.html)** | Decrypt Edge passwords | Microsoft Edge |
| **[LaZagne](https://github.com/AlessandroZ/LaZagne)** | Multi-browser password extraction | All browsers |
| **[Firefox Credentials Viewer](https://www.nirsoft.net/utils/firefoxpassview.html)** | Decrypt Firefox passwords | Firefox |
| **[SQLiteBrowser](https://sqlitebrowser.org/)** | View SQLite databases | Firefox/Chrome history |

**Usage:**
```bash
# With ChromePass
chromepass.exe -inputfile chrome_passwords.db

# With Edge Passwords Viewer
edgepass.exe -inputfile edge_passwords.db

# With LaZagne
python -m laZagne all -path /path/to/extracted/data
```

### WiFi Password Extraction

WiFi passwords from `wifi_passwords.txt` are in plaintext:
```
SSID_Name : Key Content: YourPasswordHere
HomeNetwork : Key Content: securepass123
CoffeeShop : Key Content: publicwifi456
```

Simply extract the password after `Key Content:` for each network.

---

## 💻 Supported Systems

| OS | Version | Support |
|----|---------|---------|
| Windows | 10, 11 | ✅ Full |
| Windows | 7, 8 | ⚠️ Partial (may work) |
| macOS | Any | ❌ Not supported |
| Linux | Any | ❌ Not supported |

**Requirements:**
- PowerShell 3.0+
- Admin privileges recommended for system file access
- [Optional] Flipper Zero in mass storage mode (for USB export)

---

## 📊 Caps Lock Encoding Explanation

The Caps Lock encoding method converts WiFi password data to binary and uses keyboard state changes:

- **`1` (binary bit)** → Toggle Caps Lock (±)
- **`0` (binary bit)** → Send space (pause)

**Example:** `A` (ASCII 65) = `01000001` in binary
```
0 → space
1 → Caps Lock toggle
0 → space
0 → space
0 → space
0 → space
0 → space
1 → Caps Lock toggle
```

**Decoding Method:**
1. Record Caps Lock LED state changes during payload execution
2. Convert LED states to binary (ON=1, OFF=0)
3. Decode base64 string from binary
4. Decode to ASCII text

**Tools for decoding:**
```bash
# Convert Caps Lock LED pattern to base64, then:
echo "base64_string_here" | base64 -d
```

---

## 🛠️ Troubleshooting

| Problem | Solution |
|---------|----------|
| **Flipper not recognized** | Ensure BadUSB firmware is up-to-date |
| **No data extracted** | Verify target system has browsers/WiFi installed |
| **PowerShell blocked** | Payload automatically falls back to Caps Lock (Mode 1) |
| **USB mount not detected** | Manually connect Flipper in mass storage mode |
| **Caps Lock too slow** | Reduce delay with `CAPS_LOCK_DELAY_MS 30` |
| **Caps Lock too fast** | Increase delay with `CAPS_LOCK_DELAY_MS 100` |
| **Database files encrypted** | Use decryption tools listed in [Decryption Tools](#decryption-tools) |
| **WiFi passwords show as asterisks** | Use `wifi_passwords.txt` - this payload extracts in plaintext |

---

## 📝 License

All scripts in this repository are licensed under the **MIT License**. See LICENSE file for details.

```
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, and merge.
```

---

## 📧 Support

Have questions or issues?

- **GitHub Issues:** Open an issue on this repository
- **Discord:** Reach out via Discord
- **Email:** Check GitHub profile for contact info

---

## ⚖️ Legal Notice

**Use responsibly and legally.** These tools are provided for:
- ✅ Authorized security testing
- ✅ Educational purposes on your own systems
- ✅ Penetration testing (with written permission)

**NOT for:**
- ❌ Unauthorized system access
- ❌ Data theft
- ❌ Illegal activities
- ❌ Testing without explicit permission

By using these scripts, you accept full responsibility for your actions. The creator assumes no liability for misuse or damage caused.

---

**Happy hacking! Stay ethical. 🔒**

*Last Updated: 2026-08-04*
