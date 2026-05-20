# Updating the PV Pi via SWD on Raspberry Pi

## 1. Wiring
Install programming header J6 (2.54mm 4-pin header)<br>
With your PV Pi mounted on top of your Raspberry Pi<br>
<br>
PV Pi programming header (top to bottom):
```
┌──────────┐
│  GND     │  → Not Connected (NC)
│  SWDCLK  │  → Pi GPIO 20
│  SWDIO   │  → Pi GPIO 21
│  3.3V    │  → Not Connected (NC)
└──────────┘
```
<img width="930" height="789" alt="image" src="https://github.com/user-attachments/assets/2f38cab2-550f-42f3-bd0b-77077d2e8e66" />
---

## 2. Install OpenOCD

```bash
sudo apt update
sudo apt install openocd
```

---

## 3. Flash

Run the command for your Pi model updating the Firmware .bin file with the latest version:

**Pi 5:**
```bash
sudo openocd -f flash_pi5.cfg \
  -c "program firmware/pvpi_fw_v1.14.bin verify reset exit 0x08000000"
```

**Pi 1 / 2 / 3 / 4 / Zero:**
```bash
sudo openocd -f flash_pi1-4.cfg \
  -c "program firmware/pvpi_fw_v1.14.bin verify reset exit 0x08000000"
```

Expected output:
```
** Programming Finished **
** Verify Started **
** Verified OK **
** Resetting Target **
shutdown command invoked
```

---

## 4. Confirm
If using the PV Pi manager, then, in the pvpi_manager directory run:
```
uv run pvpi get-version
```

Or send the "GET_VERSION" command over UART:
```
GET_VERSION
```

You should see the updated FW number eg:
```
PVPi,D,1.14
```

---

## Troubleshooting

**No target found / `Failed to connect`**
- Double-check SWDIO/SWDCLK wiring — easy to swap
- Try slowing down — change `adapter speed 500` to `adapter speed 100`

**`Error: libgpiod: unable to open chip`** *(Pi 5 only)*
- Run `gpiodetect` to confirm chip number, try changing `-chip 4` to `-chip 0`

# Firmware Versions
### V1.14 (20/05/2026)
Fixes input voltage read for voltages > 32V
Fixes low-voltage shutdown/alarm set clashes
