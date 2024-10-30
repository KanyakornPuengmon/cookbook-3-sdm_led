
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


![image](https://github.com/user-attachments/assets/0248118a-c44f-4d9c-8c7d-a5c55e4c6307)


## Build and Flash

8. เลือก com port

9. เลือก Build Flash and Monitor

## ผลลัพท์

- Serial Moniter แสดง สถานะของ LED

![image](https://github.com/user-attachments/assets/ba383e53-ee35-4d3d-816c-aedaaca80a7b)

```
I (245) cpu_start: Start on CPU0
I (249) example: Install sigma delta channel
I (249) gpio: GPIO[18]| InputEn: 0| OutputEn: 1| OpenDrain: 0| Pullup: 1| Pulldown: 0| Intr:0
I (259) example: Enable sigma delta channel
I (259) example: Change duty cycle continuously
```

https://drive.google.com/file/d/1TztgrTv5GsYkEvPEka7i63QPSzl4_Lnp/view


## การแก้ไข

![image](https://github.com/user-attachments/assets/b83eecd6-85a0-41c8-a390-c7ef15626b59)


สามารถปรับค่าได้ตามความต้องการ ดังนี้

- EXAMPLE_LED_DIM_PERIOD_MS : เปลี่ยนค่าเวลาที่ LED จะมีการกระพริบ
เช่น ถ้าต้องการให้ LED กระพริบเร็วขึ้น สามารถลดค่าลงได้
- EXAMPLE_LED_DIM_DELAY_MS : เปลี่ยนระยะเวลาที่ LED จะอยู่ในแต่ละสถานะ (เปิด/ปิด)
เช่น การลดค่าให้เร็วขึ้นจะทำให้ LED กระพริบเร็วขึ้น
- EXAMPLE_LED_DIM_DUTY_STEP : ปรับค่าของการเปลี่ยนแปลงความสว่างในแต่ละรอบ ถ้าต้องการให้ความสว่างเพิ่มขึ้นหรือลดลงอย่างช้า ๆ ก็สามารถลดค่าได้
- EXAMPLE_LED_DIM_DUTY_MAX และ EXAMPLE_LED_DIM_DUTY_MIN : ปรับค่าความสว่างสูงสุดและต่ำสุดของ LED เพื่อให้มีการเปลี่ยนแปลงในช่วงที่ต้องการ
  
### ตัวอย่าง

```
#define EXAMPLE_LED_DIM_PERIOD_MS    800  // ลดเวลาสำหรับ LED กระพริบเร็วขึ้น
#define EXAMPLE_LED_DIM_DELAY_MS     10    // ทำให้ LED เปลี่ยนสถานะเร็วขึ้น
#define EXAMPLE_LED_DIM_DUTY_STEP    2    // เปลี่ยนค่าความสว่างในแต่ละรอบให้มากขึ้น
```

## ผลลัพท์การแก้ไข

- LED กระพริบเร็วขึ้น

https://drive.google.com/file/d/161-8x1vni2QJvoyo4KNkm9XjXJEJGDsy/edit

## ตัวอย่างโค้ดที่แก้ไข

```
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "esp_log.h"
#include "driver/sdm.h"

#define EXAMPLE_SIGMA_DELTA_GPIO_NUM  18
#define EXAMPLE_LED_DIM_PERIOD_MS    800
#define EXAMPLE_LED_DIM_DELAY_MS     10
#define EXAMPLE_LED_DIM_DUTY_STEP    2 
#define EXAMPLE_LED_DIM_DUTY_MAX     90
#define EXAMPLE_LED_DIM_DUTY_MIN     (EXAMPLE_LED_DIM_DUTY_MAX - EXAMPLE_LED_DIM_PERIOD_MS / EXAMPLE_LED_DIM_DELAY_MS * EXAMPLE_LED_DIM_DUTY_STEP)

static const char TAG = "sdm_led";

void app_main(void)
{
    ESP_LOGI(TAG, "Install sigma delta channel");
    sdm_channel_handle_t sdm_chan = NULL;
    sdm_config_t config = {
        .clk_src = SDM_CLK_SRC_DEFAULT,
        .gpio_num = EXAMPLE_SIGMA_DELTA_GPIO_NUM,
        .sample_rate_hz = 1 1000 * 1000, // 1MHz sample rate
    };
    ESP_ERROR_CHECK(sdm_new_channel(&config, &sdm_chan));

    ESP_LOGI(TAG, "Enable sigma delta channel");
    ESP_ERROR_CHECK(sdm_channel_enable(sdm_chan));

    ESP_LOGI(TAG, "Change duty cycle continuously");
    int8_t duty = 0;
    int step = EXAMPLE_LED_DIM_DUTY_STEP;
    while (1) {
        ESP_ERROR_CHECK(sdm_channel_set_pulse_density(sdm_chan, duty));
        /* By changing delay time, you can change the blink frequency of LED /
        vTaskDelay(pdMS_TO_TICKS(EXAMPLE_LED_DIM_DELAY_MS));

        duty += step;
        if (duty == EXAMPLE_LED_DIM_DUTY_MAX || duty == EXAMPLE_LED_DIM_DUTY_MIN) {
            step= -1;
        }
    }
}

```
 
## สรุปผลการทดลอง

- การทดลอง Sigma Delta Modulation LED Example บน ESP32 เป็นการทดลองเพื่อทดสอบการใช้เทคนิค Sigma-Delta modulation ในการควบคุมความสว่างของ LED ค่าความสว่างของ LED อาจมีการกระพริบถี่ ๆ ซึ่งขึ้นอยู่กับความถี่ของสัญญาณ modulation ที่ตั้งค่าไว้
