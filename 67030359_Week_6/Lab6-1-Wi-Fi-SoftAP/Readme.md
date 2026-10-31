# Worksheet Lab6-1-Wi-Fi-SoftAP

## Build Flash and monitor

**Build command (Docker)**

```bash
docker run --rm --mount "type=bind,source=$((Get-Location).Path),target=/workspace" -w /workspace/ESP32_Project/Lab6-1-Wi-Fi-SoftAP espressif/idf:release-v6.1  idf.py -B build-v6 build
```

**Flash command (Window)**

```bash
python -m esptool --chip esp32 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 2MB --flash_freq 40m 0x1000 build-v6\bootloader\bootloader.bin 0x8000 build-v6\partition_table\partition-table.bin 0x10000 build-v6\wifi_softap_tracking.bin && idf monitor
```

## References

**Build command (Docker)**

```bash
docker run --rm \
  --mount "type=bind,source=$((Get-Location).Path),target=/workspace" \
  -w /workspace/ESP32_Project/Lab6-1-Wi-Fi-SoftAP \
  espressif/idf:release-v6.1 \
  idf.py -B build-v6 build
```

🧱 1) docker run สั่งให้ Docker สร้างและรัน container จาก image ที่กำหนด

🧹 2) --rm เมื่อ container ทำงานเสร็จ ให้ลบทิ้งทันที ไม่เก็บ container ค้างไว้ → workspace สะอาด

📁 3) --mount "type=bind,source=...,target=..." เอาโฟลเดอร์บนเครื่องจริง (Windows) มาเชื่อมต่อเข้าไปใน container

type=bind → ผูกโฟลเดอร์จริงเข้ากับ container

source=$((Get-Location).Path) → path ปัจจุบันของ PowerShell

target=/workspace → container จะเห็นโฟลเดอร์นี้เป็น /workspace

นี่คือจุดที่ผิดบ่อยที่สุด ถ้า PowerShell อยู่ผิดที่ → container จะเห็นโครงสร้างไฟล์ไม่ตรงกับที่คุณคิด

📌 4) -w /workspace/... ตั้ง working directory ของ container เหมือน cd เข้าไปในโฟลเดอร์นั้นก่อนรันคำสั่ง

Docker จะเข้าไปใน:

```text
/workspace/ESP32_Project/Lab6-2-RSSI-Speed-Profiler
```

แล้วรันคำสั่งต่อไป

ถ้าโฟลเดอร์นี้ไม่มีจริง → Docker จะบอกว่าไม่เจอ CMakeLists.txt

🧰 5) espressif/idf:release-v6.1 ชื่อ image ที่จะใช้รัน เป็น ESP-IDF เวอร์ชัน 6.1 ที่ Espressif ทำไว้ให้

🛠️ 6) idf.py -B build-v6 build คำสั่งที่รันใน container

idf.py → ตัว build system ของ ESP-IDF

-B build-v6 → ให้สร้าง build directory ชื่อ build-v6

build → คำสั่งให้ compile project

🎯 สรุปแบบสั้นที่สุด (จำง่าย)

| Argument | ความหมาย |
| --- | --- |
| --rm | รันเสร็จลบ container |
| --mount | ผูกโฟลเดอร์ Windows → container |
| source= | โฟลเดอร์จริงบน Windows |
| target= | โฟลเดอร์ที่ container จะเห็น |
| -w | โฟลเดอร์ที่ container จะเข้าไปทำงาน |
| espressif/idf:release-v6.1 | ESP-IDF image |
| idf.py -B build-v6 build | คำสั่ง build ESP-IDF |

**Flash command (Window)**

```bash
cd ESP32_Project/Lab6-1-Wi-Fi-SoftAP

python -m esptool -p COM24 --chip esp32 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 2MB --flash_freq 40m 0x1000 build-v6\bootloader\bootloader.bin 0x8000 build-v6\partition_table\partition-table.bin 0x10000 build-v6\wifi_softap_tracking.bin && idf monitor -p COM24
```

| Argument / Parameter | ความหมายและการทำงาน |
| --- | --- |
| python -m esptool | เรียกใช้งานเครื่องมือ esptool (โมดูลสำหรับจัดการโปรแกรม ESP32 ของ Espressif) ผ่าน Python |
| -p COMx | ผ่าน COM port x |
| --chip esp32 | ระบุตระกูลของชิปเป้าหมายเป็น ESP32 |
| -b 460800 (หรือ --baud) | กำหนด Baud Rate สื่อสารในการเขียน Flash เป็น 460,800 bps (ช่วยให้เขียนโปรแกรมได้เร็วขึ้น) |
| --before default_reset | สถานะก่อนการแฟลช: สั่งให้ส่งสัญญาณ DTR/RTS เข้าสู่ Download/Bootloader Mode อัตโนมัติ |
| --after hard_reset | สถานะหลังแฟลชเสร็จ: สั่งให้ทำ Hardware Reset บอร์ด เพื่อให้ ESP32 เริ่มทำงานทันที |
| write_flash | คำสั่งหลักในการสั่งให้เขียนไฟล์ Binary ลงในหน่วยความจำ Flash |
| --flash_mode dio | กำหนดโหมดการสื่อสารกับหน่วยความจำ SPI Flash เป็น DIO (Dual I/O - ใช้สายข้อมูล 2 เส้น) |
| --flash_size 2MB | กำหนดขนาดของ Flash Memory ที่ใช้งานเป็น 2MB |
| --flash_freq 40m | กำหนดความถี่สัญญาณนาฬิกาในการรับส่งข้อมูลกับ Flash เป็น 40 MHz |
| 0x1000 build-v6\bootloader\...bin | [Address + File 1] แฟลชไฟล์ Bootloader ที่ตำแหน่งหน่วยความจำ 0x1000 |
| 0x8000 build-v6\partition_table\...bin | [Address + File 2] แฟลชไฟล์ Partition Table ที่ตำแหน่งหน่วยความจำ 0x8000 |
| 0x10000 build-v6\wifi_softap_tracking.bin | [Address + File 3] แฟลชไฟล์ Application (โปรแกรมหลัก) ที่ตำแหน่งหน่วยความจำ 0x10000 |
| && | ตัวเชื่อมคำสั่ง: ถ้ารันคำสั่งแฟลชสำเร็จ จะรันคำสั่งถัดไปทันที |
| idf monitor | เปิดโปรแกรม Serial Monitor ของ ESP-IDF เพื่อดูข้อมูล Log จากบอร์ด |
