<!-- omit from toc -->
# Flipper Zero BadUSB Script Collection - Windows Only

Welcome to this comprehensive Flipper Zero BadUSB script collection for Windows systems! These scripts are designed for security testing and educational purposes. By downloading and using these files, you automatically agree to the MIT license and the terms outlined below.

## ⚠️ Disclaimer

**These scripts are for authorized security testing only.** Unauthorized access to computer systems is illegal. Only use these tools on systems you own or have explicit permission to test. The creator assumes no liability for misuse.

---

## 📋 Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Payload Descriptions](#payload-descriptions)
  - [DataGrabber.txt](#datagrabber-windows-browser--wifi-credentials)
- [Configuration Guide](#configuration-guide)
- [Usage Examples](#usage-examples)
- [Decryption Tools](#decryption-tools)
- [Testing Your Payload](#testing-your-payload)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## ✨ Features

- ✅ **Multiple Export Methods**: USB mass storage or Caps Lock binary encoding with automatic fallback
- ✅ **Browser Data Extraction**: Chrome, Firefox, Edge passwords, history, and cookies
- ✅ **WiFi Password Grabbing**: Extracts all saved WiFi networks and credentials in plaintext
- ✅ **Error Handling**: Automatic fallback to Caps Lock encoding if USB not available
- ✅ **Flipper Zero Compatible**: Optimized DuckyScript syntax for Flipper Zero BadUSB module
- ✅ **Flexible Configuration**: Easy-to-modify defines for quick customization
- ✅ **Auto-Cleanup**: Removes temporary files after exfiltration
- ✅ **Base64 Encoding**: WiFi passwords encoded safely for Caps Lock transmission

---

## 📦 Installation

1. **Clone or download this repository:**
   ```bash
   git clone https://github.com/Mr-Ender8/Flipper_Zero-BadUsb.git
   ```

2. **Copy payload files to your Flipper Zero:**
   - Connect Flipper Zero to your computer
   - Navigate to `badusb/` folder via qFlipper
   - Copy `.txt` files to this directory
   - Eject and use via BadUSB app on Flipper

3. **Alternative: Direct SD Card access**
   - Connect Flipper as USB drive
   - Navigate to `badusb/` folder
   - Drag and drop `.txt` files
   - Safely eject

---

## 🎯 Payload Descriptions

### DataGrabber - Windows Browser & WiFi Credentials

**Purpose:** Extract sensitive data including browser passwords, history, cookies, and WiFi credentials from Windows 10/11

**Modes:**
- **Mode 1 (Default):** Try USB mass storage export first, fallback to Caps Lock binary encoding
- **Mode 2:** USB mass storage export only (fastest)
- **Mode 3:** Caps Lock binary encoding only (always works)

**Configuration:**
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 50
```

**Extracted Data:**

| Source | Data Type | Encryption | Location |
|--------|-----------|------------|----------|
| **Chrome** | Passwords (Login Data) | 🔒 Encrypted | `AppData\Local\Google\Chrome\Default` |
| **Chrome** | History | 🔒 Encrypted | `AppData\Local\Google\Chrome\Default` |
| **Chrome** | Cookies | 🔒 Encrypted | `AppData\Local\Google\Chrome\Default` |
| **Firefox** | Profiles (*.db) | ⚠️ SQLite format | `AppData\Roaming\Mozilla\Firefox\Profiles` |
| **Edge** | Passwords (Login Data) | 🔒 Encrypted | `AppData\Local\Microsoft\Edge\Default` |
| **Edge** | History | 🔒 Encrypted | `AppData\Local\Microsoft\Edge\Default` |
| **WiFi** | Network SSIDs & Passwords | ✅ Plaintext | netsh wlan profiles |

**Features:**
- Auto-detects installed browsers
- Extracts all saved WiFi networks and passwords in **plaintext**
- Copies to Flipper mass storage via USB (Mode 1-2)
- WiFi passwords encoded as base64 for Caps Lock mode (Mode 1 & 3)
- Auto-cleanup of temporary files
- Real-time status messages in PowerShell

**Output Files (USB Mode):**
```
chrome_passwords.db        (encrypted - needs decryption tool)
chrome_history.db          (encrypted - needs SQLite reader)
chrome_cookies.db          (encrypted)
firefox_places.sqlite      (SQLite database - readable)
firefox_key4.db            (encrypted credentials)
edge_passwords.db          (encrypted)
edge_history.db            (encrypted)
wifi_passwords.txt         (plaintext - READY TO USE)
```

**Output (Caps Lock Mode):**
- WiFi passwords encoded as binary pattern
- Format: `1` = Caps Lock toggle, `0` = space key
- Decode with base64 decoder and binary-to-text converter

---

## ⚙️ Configuration Guide

### Basic Configuration

Edit the `DEFINE` section at the top of `DataGrabber.txt`:

```text
REM ===== DEFINE CONFIGURATION =====
DEFINE EXPORT_MODE 1           # 1=USB+fallback, 2=USB only, 3=CapsLock only
DEFINE CAPS_LOCK_DELAY_MS 50   # Milliseconds between toggles (30-100 recommended)
REM ===== END DEFINES =====
```

### Export Modes Explained

| Mode | Method | Speed | Reliability | When to Use |
|------|--------|-------|-------------|------------|
| **1** | USB First → Caps Lock | ⚡ Fast (USB) | ✅ Very High | **Default - most reliable** |
| **2** | USB Only | ⚡ Very Fast | 🟡 Moderate | Fast exfil, Flipper connected |
| **3** | Caps Lock Only | 🐢 Slow (5-10min for WiFi) | ✅ Always Works | Air-gapped, no USB access |

### Adjusting Caps Lock Delay

```text
DEFINE CAPS_LOCK_DELAY_MS 50   # Current setting (recommended for most systems)
```

- **30-40ms**: Very fast, may miss on slow systems
- **50ms**: Standard (default) - good balance
- **75-100ms**: Slower targets, older computers
- **150ms+**: Very slow/laggy systems, remote desktop

**How to test:**
1. Set to 50ms
2. Run on test system
3. If encoding fails/corrupts, increase to 75
4. If too slow, decrease to 30

---

## 🚀 Usage Examples

### Example 1: Default Setup (Recommended)
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 50
```
**Best for:** Most situations
- Tries USB first (fast)
- Automatically falls back to Caps Lock if USB not found
- Most flexible option

### Example 2: USB Only (Fastest)
```text
DEFINE EXPORT_MODE 2
DEFINE CAPS_LOCK_DELAY_MS 50
```
**Best for:** Ensuring Flipper is connected to USB
- Fastest method (~5 seconds)
- Requires USB mass storage connection
- Fails if USB not available

### Example 3: Caps Lock Only (Most Reliable)
```text
DEFINE EXPORT_MODE 3
DEFINE CAPS_LOCK_DELAY_MS 75
```
**Best for:** Air-gapped systems, remote access
- Works without USB connection
- Slower but more reliable
- Good for systems with restrictions

### Example 4: Slow/Laggy System
```text
DEFINE EXPORT_MODE 1
DEFINE CAPS_LOCK_DELAY_MS 100
```
**Best for:** Remote desktop, virtual machines, older computers
- Increased delay for reliability
- Still tries USB first
- Fallback to Caps Lock with proper timing

---

## 🔐 Decryption Tools

Browser databases are **encrypted**. Use these tools to decrypt offline:

| Tool | Purpose | Target | Link |
|------|---------|--------|------|
| **ChromePass** | Decrypt Chrome passwords | Chrome/Chromium | [Download](https://www.nirsoft.net/utils/chromepass.html) |
| **Edge Passwords Viewer** | Decrypt Edge passwords | Microsoft Edge | [Download](https://www.nirsoft.net/utils/edgepass.html) |
| **LaZagne** | Multi-browser extraction | All browsers | [GitHub](https://github.com/AlessandroZ/LaZagne) |
| **Firefox Credentials Viewer** | Decrypt Firefox passwords | Firefox | [Download](https://www.nirsoft.net/utils/firefoxpassview.html) |
| **SQLiteBrowser** | View SQLite databases | Firefox history | [Download](https://sqlitebrowser.org/) |
| **DB Browser** | SQLite database viewer | Chrome/Firefox DBs | [Download](https://sqlitebrowser.org/) |

### How to Decrypt Chrome Passwords

**Using ChromePass:**
```bash
1. Download ChromePass.exe from NirSoft
2. Copy chrome_passwords.db to same folder as ChromePass
3. Run: chromepass.exe -inputfile chrome_passwords.db
4. View decrypted passwords in output
```

**Using LaZagne (Python):**
```bash
python -m laZagne all -path /path/to/extracted/files
```

### WiFi Password Extraction

WiFi passwords from `wifi_passwords.txt` are **already in plaintext**:

```
SSID_Name : Key Content: YourPasswordHere
HomeNetwork : Key Content: securepass123
CoffeeShop_WiFi : Key Content: publicwifi456
Guest_Network : Key Content: temppass789
```

Simply extract the password after `Key Content:` - no decryption needed!

---

## 🧪 Testing Your Payload

### Step 1: Test on Your Own Computer FIRST

```
⚠️ IMPORTANT: Only test on systems you own!
```

1. **Create backup** of Chrome/Firefox data (optional but safe)
2. **Copy DataGrabber.txt** to Flipper `badusb/` folder
3. **Disconnect Flipper** from computer
4. **Plug Flipper into your test computer** via USB
5. **Open BadUSB app** on Flipper
6. **Select DataGrabber.txt**
7. **Press middle button** to execute
8. **Wait** for completion (30 seconds to 5 minutes depending on mode)
9. **Check for output:**
   - **USB Mode**: Check connected USB drive for .db files
   - **Caps Lock Mode**: Monitor Caps Lock LED (should flash rapidly)

### Step 2: Verify Data Extraction

**Check USB drive (if Mode 2):**
```
Look for files:
- chrome_passwords.db
- chrome_history.db
- firefox_*.db
- wifi_passwords.txt
```

**Check temp folder (if fallback):**
```
C:\Users\[YourUsername]\AppData\Local\Temp\
```

**Check Caps Lock encoding (Mode 3):**
```
- Monitor Caps Lock LED
- Should toggle rapidly for 5-10 minutes
- Record LED state changes as binary
- Decode base64 to get WiFi passwords
```

### Step 3: Decrypt Extracted Data

**For WiFi:**
- Open `wifi_passwords.txt` with notepad
- Passwords are in plaintext (ready to use)

**For Chrome/Edge:**
- Use ChromePass or LaZagne
- Select the .db files
- View decrypted passwords

**For Firefox:**
- Open `firefox_*.db` with SQLite browser
- View stored logins and history

---

## 🛠️ Troubleshooting

### Issue: Flipper Not Recognized

**Solution:**
- Update Flipper firmware to latest version
- Use official qFlipper app for file transfer
- Try different USB cable
- Try different USB port

### Issue: Script Doesn't Run / ERROR messages

**Solution:**
- Check for blank lines in payload file
- Verify DELAY timing (increase if too short)
- Remove any special characters
- Re-download payload (may be corrupted)

### Issue: PowerShell Opens But Nothing Happens

**Solution:**
- Increase initial DELAY from 500 to 1000ms
- PowerShell may be slower on some systems
- Try Mode 3 (Caps Lock only) as fallback

### Issue: USB Not Detected

**Solution:**
- Payload automatically falls back to Caps Lock (Mode 1)
- Ensure Flipper mass storage mode is enabled
- Try connecting Flipper to different USB port
- Use Mode 3 if USB issues persist

### Issue: Caps Lock Encoding Too Fast/Slow

**Solution:**
- Increase: `DEFINE CAPS_LOCK_DELAY_MS 75` (slower)
- Decrease: `DEFINE CAPS_LOCK_DELAY_MS 30` (faster)
- Test different values (30-150ms range)

### Issue: Files Not Found / Empty Output

**Solution:**
- Payload silently skips missing browsers
- If no browsers installed: expected (no passwords to grab)
- Check that target has Chrome, Firefox, or Edge installed
- WiFi extraction depends on netsh availability

### Issue: Caps Lock Encoding Incomplete

**Solution:**
- Increase delay: `CAPS_LOCK_DELAY_MS 100`
- Check system isn't in screensaver (wake system first)
- Monitor doesn't affect encoding (only visual feedback)

---

## 💻 System Requirements

| Requirement | Status | Notes |
|-------------|--------|-------|
| **OS** | ✅ Windows 10/11 | Windows 7/8 may work (untested) |
| **PowerShell** | ✅ v3.0+ | Usually preinstalled |
| **Admin Rights** | 🟡 Recommended | Better success with admin |
| **Browsers** | 🟡 Optional | Skips if not installed |
| **WiFi** | ✅ Auto-detected | Extracts via netsh |
| **USB** | 🟡 Optional | Falls back to Caps Lock if needed |
| **Flipper Firmware** | ✅ Current | Update recommended |

---

## 📊 Caps Lock Encoding Explanation

For advanced users understanding the Caps Lock method:

**Binary Encoding:**
- Plaintext → UTF-8 → Base64 → Binary string
- Each byte = 8 bits
- `1` = Caps Lock toggle, `0` = space key

**Example:**
```
WiFi Password: "pass123"
↓ Base64 encode
cGFzczEyMw==
↓ Binary
01100011 01000111 01000001 ...
↓ Caps Lock pattern
(0=space, 1=toggle)
```

**Decoding:**
1. Record/monitor Caps Lock LED state
2. Convert to binary
3. Decode base64
4. Read plaintext password

---

## 📝 License

All scripts in this repository are licensed under the **MIT License**.

```
MIT License - Free to use, modify, and distribute
See LICENSE.md for full details
```

---

## ⚖️ Legal Notice

**Use responsibly and legally:**
- ✅ Authorized security testing only
- ✅ Your own systems for education
- ✅ Written permission for penetration testing

**Do NOT use for:**
- ❌ Unauthorized system access
- ❌ Data theft or extortion
- ❌ Illegal activities
- ❌ Systems without permission

**By using these scripts, you accept full responsibility for your actions.**

---

## 📧 Support & Questions

- **GitHub Issues:** Open an issue on this repository
- **Discord:** [Your Discord]
- **Email:** Check GitHub profile

---

**Happy hacking! Stay ethical. 🔒**

*Last Updated: 2026-08-04*
*Compatible: Flipper Zero | Windows 10/11 | DuckyScript v1*
