# Worksheet Lab6-4-IoT-Sensor-Dashboard

## Build Flash and monitor

**Build command (Docker)**

```text
# รันจาก root directory: เช่น d:\GitHubRepos\__ENGEDU\__Iot_App_2569
docker run --rm --mount "type=bind,source=$((Get-Location).Path),target=/workspace" -w /workspace/ESP32_Project/Lab6-4-IoT-Sensor-Dashboard espressif/idf:release-v6.1 idf.py -B build-v6 build
```

**Flash command (Window)**

```text
# รันจาก folder: ESP32_Project/Lab6-4-IoT-Sensor-Dashboard
python -m esptool -p COM26 --chip esp32 -b 460800 --before default_reset --after hard_reset write_flash --flash_mode dio --flash_size 2MB --flash_freq 40m 0x1000 build-v6\bootloader\bootloader.bin 0x8000 build-v6\partition_table\partition-table.bin 0x10000 build-v6\iot_sensor_dashboard.bin && idf monitor -p COM26
```

## การทดสอบ

- Flash เสร็จแล้ว → เชื่อมต่อ Wi-Fi ชื่อ MY_ESP32_SENSOR_AP (Password: 12345678)
- เปิด Browser → http://192.168.4.1 → ดู Dashboard
- ทดสอบ JSON API → http://192.168.4.1/api/data
