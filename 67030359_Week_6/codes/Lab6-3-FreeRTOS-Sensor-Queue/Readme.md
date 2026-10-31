# Worksheet Lab6-1-Wi-Fi-SoftAP

## Build Flash and monitor

**Build command (Docker)**

```text
# 1. เก็บ folder ปัจจุบันไว้  
$old = Get-Location

# 2. เปลี่ยนไปที่ workspace folder (ตามที่ระบุใน docker run)  
Set-Location ../..

# 3. รัน build command
docker run --rm --mount "type=bind,source=$((Get-Location).Path),target=/workspace" -w /workspace/ESP32_Project/Lab6-3-FreeRTOS-Sensor-Queue espressif/idf:release-v6.1  idf.py -B build-v6 build

# 4. กลับมาโฟลเดอร์เดิม เพื่อที่จะ flash
Set-Location $old
```

**Flash command (Window)**

```bash
python -m esptool -p COM26 --chip esp32 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 2MB --flash_freq 40m 0x1000  build-v6\bootloader\bootloader.bin 0x8000  build-v6\partition_table\partition-table.bin 0x10000 build-v6\FreeRTOS-Sensor-Queue.bin && idf monitor -p COM26
```

## References

```bash
docker run --rm \
  --mount "type=bind,source=$((Get-Location).Path),target=/workspace" \
  -w /workspace/ESP32_Project/Lab6-3-FreeRTOS-Sensor-Queue \
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
/workspace/ESP32_Project/Lab6-3-FreeRTOS-Sensor-Queue
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
