
# แนวทางการทำงาน Sigma Delta Modulation LED Example
การทดลองนี้เป็นการควบคุมการกระพริบของ LED โดยใช้การสื่อสารผ่าน Sigma-Delta Modulation บน ESP32 โค้ดนี้ใช้ FreeRTOS เพื่อจัดการกับการทำงานของ task ที่ทำให้ LED มีความสว่างและมืดอย่างต่อเนื่อง 

## การเลือก Example IDF

![สกรีนช็อต 2024-10-29 235340](https://github.com/user-attachments/assets/70ce3f84-77ad-48bb-9f3f-557cd7ec98de)

1. เลือก ESP-IDF

2. เลือก show example

3. พิมพ์คำว่า Sdm

4. เลือก sdm_led


![สกรีนช็อต 2024-10-29 235134](https://github.com/user-attachments/assets/d94cd89b-36fe-42d3-83d5-4f22928c1389)

  
5. คลิกเลือก Create project using example sdm_led


6. เมื่อสร้างแล้วจะได้ไฟล์ทั้งหมดดังนี้

![สกรีนช็อต 2024-10-29 235153](https://github.com/user-attachments/assets/3a5ea28a-29da-4df8-9803-a28f7694ce20)

## การต่อ GPIO กับ LED 

7. เลือกไฟล์ main.c

### ในโค้ดมีการกำหนดการต่อ GPIO ดังนี้

- EXAMPLE_SIGMA_DELTA_GPIO_NUM คือ หมายเลข GPIO ที่ใช้เชื่อมต่อกับ LED
  
- EXAMPLE_LED_DIM_PERIOD_MS คือ ตัวแปรที่กำหนดค่าเวลาที่ LED จะมีการกระพริบ
  
- EXAMPLE_LED_DIM_DELAY_MS คือ ความช้าในการเปลี่ยนแปลงความสว่าง
  
- EXAMPLE_LED_DIM_DUTY_STEP คือ จำนวนที่เพิ่มหรือลดความสว่าง
  
- EXAMPLE_LED_DIM_DUTY_MAX คือ ค่าสูงสุดของ duty cycle
  
- EXAMPLE_LED_DIM_DUTY_MIN คือ ค่าต่ำสุดของ duty cycle

![image](https://github.com/user-attachments/assets/d220dc83-131d-42e7-9f72-b7275542c60b)

## Build and Flash

8. เลือก com port

9. เลือก Build Flash and Monitor

## ผลลัพท์

- Serial Moniter แสดง สถานะของ LED

```
I (245) cpu_start: Starting scheduler.
I (249) example: Install sigma delta channel
I (249) gpio: GPIO[14]| InputEn: 0| OutputEn: 1| OpenDrain: 0| Pullup: 1| Pulldown: 0| Intr:0
I (259) example: Enable sigma delta channel
I (259) example: Change duty cycle continuously
```

## การแก้ไข

![สกรีนช็อต 2024-10-30 000409](https://github.com/user-attachments/assets/4bcb986d-55bc-4f93-af74-c9fd064057be)

สามารถปรับค่าได้ตามความต้องการ ดังนี้

- EXAMPLE_LED_DIM_PERIOD_MS : เปลี่ยนค่าเวลาที่ LED จะมีการกระพริบ
เช่น ถ้าต้องการให้ LED กระพริบเร็วขึ้น สามารถลดค่าลงได้
- EXAMPLE_LED_DIM_DELAY_MS : เปลี่ยนระยะเวลาที่ LED จะอยู่ในแต่ละสถานะ (เปิด/ปิด)
เช่น การลดค่าให้เร็วขึ้นจะทำให้ LED กระพริบเร็วขึ้น
- EXAMPLE_LED_DIM_DUTY_STEP : ปรับค่าของการเปลี่ยนแปลงความสว่างในแต่ละรอบ ถ้าต้องการให้ความสว่างเพิ่มขึ้นหรือลดลงอย่างช้า ๆ ก็สามารถลดค่าได้
- EXAMPLE_LED_DIM_DUTY_MAX และ EXAMPLE_LED_DIM_DUTY_MIN : ปรับค่าความสว่างสูงสุดและต่ำสุดของ LED เพื่อให้มีการเปลี่ยนแปลงในช่วงที่ต้องการ
  
### ตัวอย่าง

```
#define EXAMPLE_LED_DIM_PERIOD_MS    500  // ลดเวลาสำหรับ LED กระพริบเร็วขึ้น
#define EXAMPLE_LED_DIM_DELAY_MS     5    // ทำให้ LED เปลี่ยนสถานะเร็วขึ้น
#define EXAMPLE_LED_DIM_DUTY_STEP    5    // เปลี่ยนค่าความสว่างในแต่ละรอบให้มากขึ้น
```

### ผลลัพท์การแก้ไข
