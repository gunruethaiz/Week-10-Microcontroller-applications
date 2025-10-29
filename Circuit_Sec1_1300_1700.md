# วงจรทดสอบ

<img width="994" height="548" alt="image" src="https://github.com/user-attachments/assets/3b79f95b-72b8-4539-93e6-284a9e299825" />


## การบ้าน

 ทำให้  S2  และ  LED_2  ทำงานเหมือนชุดที่ทำไปแล้ว 
<img width="959" height="489" alt="image" src="https://github.com/user-attachments/assets/75662073-f6e4-4993-94e1-f31a9ad61489" />

## การทดสอบและผลลัพธ์

### การอัพเดต Code
ได้ปรับปรุงไฟล์ `Switch_LED.c` เพื่อเพิ่มการรองรับ S2 และ LED_2:

#### Pin Assignment:
- **Switch 1 (S1)**: GPIO 15 (Input) 
- **LED 1**: GPIO 16 (Output)
- **Switch 2 (S2)**: GPIO 17 (Input) 
- **LED 2**: GPIO 18 (Output)

#### การทำงาน:
- เมื่อกด S1 → LED_1 จะติด
- เมื่อปล่อย S1 → LED_1 จะดับ
- เมื่อกด S2 → LED_2 จะติด  
- เมื่อปล่อย S2 → LED_2 จะดับ

### ผลการทดสอบที่คาดหวัง:

| การกระทำ | LED_1 | LED_2 | หมายเหตุ |
|---------|-------|-------|---------|
| ไม่กดปุ่มใดเลย | ดับ | ดับ | สถานะปกติ |
| กด S1 เท่านั้น | ติด | ดับ | LED_1 ทำงานตาม S1 |
| กด S2 เท่านั้น | ดับ | ติด | LED_2 ทำงานตาม S2 |
| กด S1 และ S2 พร้อมกัน | ติด | ติด | ทั้งคู่ทำงานแยกอิสระ |

### โค้ดที่อัพเดต:
```c
#include <stdio.h>
#include <driver/gpio.h>
#include <freeRTOS/freeRTOS.h>
#include <freeRTOS/Task.h>

void app_main(void)
{
    // 0. create variables 
    // 0.1 Switch_1 and LED_1 status
    int SW1_Status = 0;
    int LED1_status = 0;
    // 0.2 Switch_2 and LED_2 status
    int SW2_Status = 0;
    int LED2_status = 0;

    // 1. gpio_set_direction pin 16 and 18 to Output.
    gpio_set_direction(16, GPIO_MODE_OUTPUT);  // LED_1
    gpio_set_direction(18, GPIO_MODE_OUTPUT);  // LED_2

    // 2. gpio_set_direction pin 15 and 17 to Input.
    gpio_set_direction(15, GPIO_MODE_INPUT);   // Switch_1
    gpio_set_direction(17, GPIO_MODE_INPUT);   // Switch_2
 
    while(1)
    {
        // read level of gpio 15 and store in SW1_Status 
        SW1_Status = gpio_get_level(15);
        // read level of gpio 17 and store in SW2_Status 
        SW2_Status = gpio_get_level(17);

        // LED_1 logic: inverted from SW1 (LED on when switch pressed)
        LED1_status = !SW1_Status;     
        gpio_set_level(16, LED1_status);

        // LED_2 logic: inverted from SW2 (LED on when switch pressed)
        LED2_status = !SW2_Status;     
        gpio_set_level(18, LED2_status);
   
        // Delay
        vTaskDelay(pdMS_TO_TICKS(100));
    }
}
```

### การทดสอบจริง:
1. **Build โปรเจค**: `idf.py build`
2. **Flash ลง ESP32**: `idf.py flash`
3. **ทดสอบด้วยการกดปุ่ม**:
   - ทดสอบ S1 แยก
   - ทดสอบ S2 แยก  
   - ทดสอบกดพร้อมกัน
4. **สังเกตผลลัพธ์**: LED ต้องติด/ดับตามตารางข้างต้น

**สถานะ**: โค้ดพร้อมสำหรับการทดสอบ ✅

## สรุปการดำเนินงาน

### ✅ งานที่เสร็จสมบูรณ์

1. **วิเคราะห์ความต้องการ**: ศึกษาจากภาพวงจรและการบ้านที่กำหนด
2. **ปรับปรุงโค้ด**: เพิ่ม S2 และ LED_2 ใน `Switch_LED.c`
3. **กำหนด Pin Assignment**: 
   - GPIO 15 → S1, GPIO 16 → LED_1
   - GPIO 17 → S2, GPIO 18 → LED_2
4. **สร้างเอกสารทดสอบ**: `testing_instructions.md` พร้อม test cases
5. **อัพเดตเอกสาร**: เพิ่มรายละเอียดการทดสอบในไฟล์นี้
6. **ทดสอบ Logic**: ตรวจสอบการทำงานของโค้ดและ logic การควบคุม

### 🔄 ขั้นตอนต่อไป

1. **Build โปรเจค**: `idf.py build` (ESP-IDF environment พร้อมแล้ว)
2. **Flash ลง ESP32**: `idf.py flash`
3. **ทดสอบฮาร์ดแวร์**: ตามเอกสาร `testing_instructions.md`
4. **บันทึกผลการทดสอบ**: อัพเดตตารางผลลัพธ์

### 📝 การใช้งาน

- ไฟล์โค้ดหลัก: `Switch_LED/main/Switch_LED.c`
- ไฟล์ทดสอบ: `testing_instructions.md`
- ผลการทดสอบ Logic: ✅ ผ่านการตรวจสอบแล้ว

### 🎯 ผลการทดสอบ Logic

| การกระทำ | S1 (GPIO15) | S2 (GPIO17) | LED_1 (GPIO16) | LED_2 (GPIO18) | สถานะ |
|---------|-------------|-------------|----------------|----------------|-------|
| ไม่กดปุ่มใดเลย | HIGH | HIGH | OFF | OFF | ✅ ถูกต้อง |
| กด S1 เท่านั้น | LOW | HIGH | ON | OFF | ✅ ถูกต้อง |
| กด S2 เท่านั้น | HIGH | LOW | OFF | ON | ✅ ถูกต้อง |
| กด S1+S2 พร้อมกัน | LOW | LOW | ON | ON | ✅ ถูกต้อง |

**สถานะโครงการ**: โค้ดพร้อมสำหรับการทดสอบบนฮาร์ดแวร์จริง ✅
