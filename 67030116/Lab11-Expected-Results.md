# Week 11: ผลที่คาดว่าจะได้ (Lab 11.1 - 11.4)

> **สถานะ: ผลที่คาดว่าจะได้ ยังไม่ได้ทดสอบกับบอร์ดหรือ Broker จริง**
> ทุกค่าเป็นการคาดการณ์จากโค้ดและใบงาน ใบงานนี้ไม่มีตารางให้กรอก แต่ต้องส่งภาพหน้าจอจากการทดลองจริง

## ก่อนแฟลช

- ติดตั้ง Mosquitto ก่อน เพราะเครื่องนี้ยังไม่มี
- **Lab 11.2:** แก้ `WIFI_SSID`, `WIFI_PASS` และ `MQTT_BROKER_URI` ค่าเดิมเป็นเครือข่ายของคนอื่น
- **Lab 11.3:** แก้ `WIFI_SSID`, `WIFI_PASS`, `MQTTS_BROKER_URI` และสร้างใบรับรองเองตามใบงาน แล้ววางใน `main/certs/`

ใบรับรองใน `main/certs/` ที่สร้างไว้ในเครื่องใช้แค่ทดสอบ build และไม่ได้ขึ้น Git เมื่อโคลน repo ใหม่ต้องสร้างเองก่อน build

## Lab 11.1: Mosquitto & MQTT

ลำดับแพ็กเก็ตที่ควรเห็นใน Broker Log:

| QoS | ฝั่ง Publisher กับ Broker | ฝั่ง Broker กับ Subscriber |
| :---: | :--- | :--- |
| 0 | `PUBLISH` | `PUBLISH` |
| 1 | `PUBLISH`, `PUBACK` | `PUBLISH`, `PUBACK` |
| 2 | `PUBLISH`, `PUBREC`, `PUBREL`, `PUBCOMP` | `PUBLISH`, `PUBREC`, `PUBREL`, `PUBCOMP` |

Subscriber ที่เปิดใหม่ควรได้รับ Retained Message ล่าสุดทันที

## Lab 11.2: ESP32 MQTT Client

- `iot/node01/telemetry` ได้รับ JSON ทุก 4 วินาที รูปแบบตามใบงาน: `{"node_id":"esp32-node01","adc_raw":...,"voltage_v":...,"led_state":false,"uptime_ms":...}`
- `adc_raw` และ `voltage_v` เปลี่ยนตามการหมุน Potentiometer
- ส่ง `ON` หรือ `{"state": 1}` ไปที่ `iot/node01/cmd/led` แล้ว LED ติด และรอบถัดไป `led_state` เป็น `true`
- ส่ง `OFF` แล้ว LED ดับ และ `led_state` เป็น `false`

## Lab 11.3: MQTTS & mTLS

- Serial Monitor แสดง `MQTTS TLS Handshake Successful! Connected to Secure Broker.`
- Broker Log แสดงการตรวจสอบใบรับรองของ `esp32-secure-node` สำเร็จ
- Client ที่ไม่มี `client.crt` และ `client.key` ควรถูกปฏิเสธ

## Lab 11.4: ESP RainMaker

- กด Power ในแอปแล้ว LED ติดหรือดับตาม
- เลื่อน Brightness แล้ว Serial Monitor แสดงค่าเปอร์เซ็นต์ใหม่
- ปิด Wi-Fi มือถือแล้วใช้ 4G/5G ยังสั่งงานได้ เพราะคำสั่งผ่าน Cloud

ต้องมีบัญชี RainMaker และแอป ESP RainMaker จึงทดสอบได้

## สิ่งที่เพิ่มในโค้ด

- `Example_project/Lab11-3_MQTTS_Secure` และ `Example_project/Lab11-4_RainMaker_Light` สร้างจากโค้ดในใบงาน เพราะ repo ยังไม่มีโปรเจกต์ของ 2 Lab นี้

- Lab 11.3 ใช้ `espressif/mqtt` เพราะ ESP-IDF v6 ย้าย MQTT ออกจากตัว IDF
- Lab 11.4 ใช้ `espressif/esp_rainmaker` และ `rmaker_app_network` จาก repo ของ RainMaker เพราะชื่อ `esp-rainmaker` และ `app_wifi.h` ในใบงานใช้ไม่ได้แล้ว จึงเปลี่ยนเป็น `app_network_init()` กับ `app_network_start()` และเพิ่ม `partitions.csv` ขนาด Flash 4 MB
