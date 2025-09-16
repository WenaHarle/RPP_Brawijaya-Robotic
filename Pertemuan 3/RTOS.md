## 📚 Teori Dasar FreeRTOS

### Apa itu RTOS?
Real-Time Operating System (RTOS) adalah sistem operasi yang dirancang untuk menjalankan aplikasi real-time dengan respons waktu yang dapat diprediksi. Berbeda dengan sistem operasi desktop, RTOS memprioritaskan **determinisme** dan **predictability** daripada throughput.

### Mengapa Menggunakan FreeRTOS di Arduino?

**Tanpa RTOS (Arduino Biasa):**
```cpp
void loop() {
  blinkLED();        // Tunggu 500ms
  readSensor();      // Tunggu 100ms  
  sendData();        // Tunggu 200ms
  checkButton();     // Hanya bisa cek setelah yang lain selesai
}
```
☹️ **Masalah**: Jika `sendData()` hang, semua fungsi lain terhenti!

**Dengan RTOS:**
```cpp
// Task berjalan secara paralel dan independen
TaskBlink()    // Berjalan setiap 500ms
TaskSensor()   // Berjalan setiap 100ms  
TaskSend()     // Berjalan setiap 200ms
TaskButton()   // Selalu responsif!
```
😊 **Keuntungan**: Setiap task independen, sistem lebih responsif dan dapat diandalkan.

---

## 🔧 Instalasi FreeRTOS di Arduino

### Langkah 1: Install Library
1. Buka Arduino IDE
2. Pergi ke **Tools → Manage Libraries**
3. Cari **"FreeRTOS"** oleh Richard Barry
4. Install **Arduino_FreeRTOS.h**

### Langkah 2: Verifikasi Instalasi
```cpp
#include <Arduino_FreeRTOS.h>

void setup() {
  Serial.begin(9600);
  Serial.println("FreeRTOS berhasil diinstall!");
}

void loop() {
  // Kosong
}
```

---

## 🔑 Sintaks Utama FreeRTOS di Arduino

### 1. Membuat Task - `xTaskCreate()`

#### Sintaks Lengkap:
```cpp
BaseType_t xTaskCreate(
  TaskFunction_t pvTaskCode,    // Pointer ke fungsi task
  const char * const pcName,    // Nama task (untuk debugging)
  configSTACK_DEPTH_TYPE usStackDepth,  // Ukuran stack (dalam words)
  void *pvParameters,           // Parameter yang dikirim ke task
  UBaseType_t uxPriority,       // Prioritas task (0 = terendah)
  TaskHandle_t *pxCreatedTask   // Handle untuk mengontrol task
);
```

#### Penjelasan Parameter:

| Parameter | Tipe Data | Keterangan | Contoh |
|-----------|-----------|------------|---------|
| `pvTaskCode` | Function pointer | Nama fungsi task yang akan dijalankan | `TaskBlink` |
| `pcName` | String | Nama task untuk debugging (maks 16 karakter) | `"LED_Task"` |
| `usStackDepth` | Integer | Ukuran memori stack untuk task (dalam words) | `128` (512 bytes) |
| `pvParameters` | Void pointer | Data yang dikirim ke task | `NULL` atau `&data` |
| `uxPriority` | Integer | Prioritas task (0-24, semakin besar semakin penting) | `1` |
| `pxCreatedTask` | TaskHandle_t* | Pointer untuk menyimpan handle task | `&taskHandle` |

#### Return Value:
- `pdPASS` (1): Task berhasil dibuat
- `errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY`: Gagal karena memory tidak cukup

#### Contoh Penggunaan:
```cpp
TaskHandle_t xTaskLED;

void setup() {
  BaseType_t result = xTaskCreate(
    TaskBlink,        // Fungsi task
    "LED_Blink",      // Nama task
    128,              // Stack size (128 words = 512 bytes)
    NULL,             // Parameter
    1,                // Prioritas
    &xTaskLED         // Task handle
  );
  
  if (result == pdPASS) {
    Serial.println("Task LED berhasil dibuat!");
  } else {
    Serial.println("Gagal membuat task!");
  }
}
```

---

### 2. Menunda Task - `vTaskDelay()`

#### Sintaks:
```cpp
void vTaskDelay(TickType_t xTicksToDelay);
```

#### Penjelasan:
- `xTicksToDelay`: Jumlah tick (satuan waktu) untuk delay
- **1 tick = 1 ms** di Arduino FreeRTOS (default)
- Task akan **suspended** selama delay, memberi kesempatan task lain berjalan

#### Konversi Waktu:
```cpp
// Metode 1: Manual
vTaskDelay(1000);  // 1000 tick = 1000 ms = 1 detik

// Metode 2: Menggunakan konstanta (lebih readable)
vTaskDelay(1000 / portTICK_PERIOD_MS);  // 1 detik

// Metode 3: Menggunakan macro (paling direkomendasikan)
vTaskDelay(pdMS_TO_TICKS(1000));  // 1 detik
```

#### Perbedaan dengan `delay()` Arduino:
| `delay()` Arduino | `vTaskDelay()` FreeRTOS |
|-------------------|-------------------------|
| Blocking - menghentikan semua | Non-blocking - hanya menunda task ini |
| CPU idle/waste power | CPU bisa menjalankan task lain |
| Single-tasking | Multitasking |

---

### 3. Memulai Scheduler - `vTaskStartScheduler()`

#### Sintaks:
```cpp
void vTaskStartScheduler(void);
```

#### Penjelasan:
- **Wajib dipanggil** setelah semua task dibuat
- Setelah dipanggil, `loop()` tidak akan pernah dieksekusi
- Scheduler akan mengatur pergantian task sesuai prioritas
- Fungsi ini **tidak akan return** jika berhasil

#### Contoh:
```cpp
void setup() {
  // Buat semua task
  xTaskCreate(Task1, "Task1", 128, NULL, 1, NULL);
  xTaskCreate(Task2, "Task2", 128, NULL, 2, NULL);
  
  // Start scheduler (harus di akhir setup)
  vTaskStartScheduler();
  
  // Kode setelah ini tidak akan pernah dieksekusi
  Serial.println("Ini tidak akan pernah tercetak!");
}

void loop() {
  // Loop ini tidak akan pernah dipanggil!
}
```

---

### 4. Menghapus Task - `vTaskDelete()`

#### Sintaks:
```cpp
void vTaskDelete(TaskHandle_t xTaskToDelete);
```

#### Parameter:
- `xTaskToDelete`: Handle task yang akan dihapus
- `NULL`: Hapus task yang sedang berjalan (suicide)

#### Contoh:
```cpp
TaskHandle_t xTaskLED;

// Hapus task tertentu
vTaskDelete(xTaskLED);

// Task menghapus dirinya sendiri
void TaskSelfDelete(void *pvParameters) {
  for (int i = 0; i < 10; i++) {
    Serial.print("Countdown: ");
    Serial.println(10 - i);
    vTaskDelay(pdMS_TO_TICKS(1000));
  }
  
  Serial.println("Task selesai, menghapus diri sendiri...");
  vTaskDelete(NULL);  // Suicide!
}
```

---

### 5. Struktur Fungsi Task

#### Template Task:
```cpp
void TaskName(void *pvParameters) {
  // Inisialisasi (dijalankan sekali)
  pinMode(LED_PIN, OUTPUT);
  
  // Loop utama task (infinite loop)
  for (;;) {  // Sama dengan while(1)
    // Kode task di sini
    digitalWrite(LED_PIN, HIGH);
    vTaskDelay(pdMS_TO_TICKS(500));
    
    digitalWrite(LED_PIN, LOW);
    vTaskDelay(pdMS_TO_TICKS(500));
  }
  
  // Kode setelah loop tidak akan pernah dieksekusi
  // kecuali task dihapus dengan vTaskDelete()
}
```

#### Menggunakan Parameter:
```cpp
typedef struct {
  int ledPin;
  int delayMs;
} LedTaskParams_t;

void TaskLED(void *pvParameters) {
  // Cast parameter ke tipe yang sesuai
  LedTaskParams_t *params = (LedTaskParams_t*)pvParameters;
  
  pinMode(params->ledPin, OUTPUT);
  
  for (;;) {
    digitalWrite(params->ledPin, HIGH);
    vTaskDelay(pdMS_TO_TICKS(params->delayMs));
    digitalWrite(params->ledPin, LOW);
    vTaskDelay(pdMS_TO_TICKS(params->delayMs));
  }
}

// Penggunaan:
void setup() {
  LedTaskParams_t ledParams = {13, 500};
  
  xTaskCreate(TaskLED, "LED", 128, &ledParams, 1, NULL);
  vTaskStartScheduler();
}
```

---

## 🎯 Multitasking Sederhana

### Tujuan:
Membuat 2 task yang berjalan bersamaan:
1. Task LED blink setiap 500ms
2. Task Serial print setiap 1 detik

### Kode Lengkap:
```cpp
#include <Arduino_FreeRTOS.h>

// Task 1: Blink LED
void TaskBlink(void *pvParameters) {
  pinMode(LED_BUILTIN, OUTPUT);
  
  for (;;) {
    digitalWrite(LED_BUILTIN, HIGH);
    vTaskDelay(pdMS_TO_TICKS(500));  // LED ON 500ms
    
    digitalWrite(LED_BUILTIN, LOW);
    vTaskDelay(pdMS_TO_TICKS(500));  // LED OFF 500ms
  }
}

// Task 2: Serial Output
void TaskSerial(void *pvParameters) {
  Serial.begin(9600);
  
  // Tunggu serial ready
  while (!Serial) {
    vTaskDelay(pdMS_TO_TICKS(10));
  }
  
  int counter = 0;
  
  for (;;) {
    Serial.print("Hello RTOS! Counter: ");
    Serial.println(counter++);
    vTaskDelay(pdMS_TO_TICKS(1000));  // Print setiap 1 detik
  }
}

void setup() {
  // Membuat task dengan prioritas sama
  xTaskCreate(TaskBlink, "Blink", 128, NULL, 1, NULL);
  xTaskCreate(TaskSerial, "Serial", 256, NULL, 1, NULL);

  // Mulai scheduler
  vTaskStartScheduler();
}

void loop() {
  // Loop tidak digunakan dalam FreeRTOS
}
```

### Output yang Diharapkan:
```
Hello RTOS! Counter: 0    // LED blink 2 kali
Hello RTOS! Counter: 1    // LED blink 2 kali  
Hello RTOS! Counter: 2    // LED blink 2 kali
...
```

---

## ⚖️ Prioritas Task dan Scheduler

### Cara Kerja Scheduler FreeRTOS:

#### 1. Priority-Based Preemptive
- Task dengan prioritas **lebih tinggi** akan **selalu** dijalankan terlebih dahulu
- Jika ada task prioritas tinggi yang ready, task prioritas rendah akan dihentikan (preempted)

#### 2. Round-Robin untuk Prioritas Sama
- Task dengan prioritas sama akan mendapat waktu CPU secara bergantian
- Setiap task mendapat **time slice** yang sama

### Contoh Prioritas Berbeda:

```cpp
#include <Arduino_FreeRTOS.h>

// Task Prioritas Tinggi (Emergency)
void TaskEmergency(void *pvParameters) {
  pinMode(2, OUTPUT);  // LED Emergency
  
  for (;;) {
    // Simulasi kondisi darurat setiap 3 detik
    vTaskDelay(pdMS_TO_TICKS(3000));
    
    // Blink cepat 5 kali (emergency signal)
    for (int i = 0; i < 5; i++) {
      digitalWrite(2, HIGH);
      vTaskDelay(pdMS_TO_TICKS(100));
      digitalWrite(2, LOW);
      vTaskDelay(pdMS_TO_TICKS(100));
    }
  }
}

// Task Prioritas Sedang (Button)  
void TaskButton(void *pvParameters) {
  pinMode(3, INPUT_PULLUP);
  Serial.begin(9600);
  
  for (;;) {
    if (digitalRead(3) == LOW) {
      Serial.println("Button pressed!");
      vTaskDelay(pdMS_TO_TICKS(200));  // Debounce
    }
    vTaskDelay(pdMS_TO_TICKS(50));  // Check setiap 50ms
  }
}

// Task Prioritas Rendah (LED Normal)
void TaskLED(void *pvParameters) {
  pinMode(LED_BUILTIN, OUTPUT);
  
  for (;;) {
    digitalWrite(LED_BUILTIN, HIGH);
    vTaskDelay(pdMS_TO_TICKS(1000));
    digitalWrite(LED_BUILTIN, LOW);
    vTaskDelay(pdMS_TO_TICKS(1000));
  }
}

void setup() {
  xTaskCreate(TaskEmergency, "Emergency", 128, NULL, 3, NULL);  // Prioritas TERTINGGI
  xTaskCreate(TaskButton, "Button", 256, NULL, 2, NULL);        // Prioritas SEDANG
  xTaskCreate(TaskLED, "LED", 128, NULL, 1, NULL);              // Prioritas TERENDAH
  
  vTaskStartScheduler();
}

void loop() {}
```

### Perilaku Scheduler:
1. **TaskLED** (prioritas 1) blink normal setiap 1 detik
2. **TaskButton** (prioritas 2) akan "mengganggu" TaskLED untuk cek button
3. **TaskEmergency** (prioritas 3) akan "mengganggu" semua task lain saat emergency

---

## 📊 Task States dan Lifecycle

### State Diagram Task:
```
    CREATE
       |
       v
   ┌─────────┐  SCHEDULER   ┌─────────┐
   │ READY   │ ────────────> │ RUNNING │
   │         │ <──────────── │         │
   └─────────┘   PREEMPT    └─────────┘
       ▲                         │
       │                         │ DELAY/
       │                         │ BLOCK
       │                         ▼
   ┌─────────┐   TIMEOUT/    ┌─────────┐
   │ DELETED │   RESUME      │ BLOCKED │
   │         │ <──────────── │         │
   └─────────┘               └─────────┘
```

### Fungsi untuk Mengecek Status Task:
```cpp
#include <Arduino_FreeRTOS.h>

TaskHandle_t xTaskLED;

void TaskMonitor(void *pvParameters) {
  Serial.begin(9600);
  
  for (;;) {
    // Cek status task
    eTaskState taskState = eTaskGetState(xTaskLED);
    
    Serial.print("Task LED status: ");
    switch (taskState) {
      case eReady:
        Serial.println("READY");
        break;
      case eRunning:
        Serial.println("RUNNING");
        break;
      case eBlocked:
        Serial.println("BLOCKED");
        break;
      case eSuspended:
        Serial.println("SUSPENDED");
        break;
      case eDeleted:
        Serial.println("DELETED");
        break;
    }
    
    vTaskDelay(pdMS_TO_TICKS(2000));
  }
}
```

---

## 🔄 Komunikasi Antar Task - Queue

### Apa itu Queue?
Queue adalah struktur data FIFO (First In, First Out) yang digunakan untuk komunikasi antar task dengan aman. Queue bersifat **thread-safe** dan **blocking**.

### Fungsi-fungsi Queue:

#### 1. Membuat Queue - `xQueueCreate()`
```cpp
QueueHandle_t xQueueCreate(
  UBaseType_t uxQueueLength,    // Jumlah maksimal item dalam queue
  UBaseType_t uxItemSize        // Ukuran setiap item (dalam bytes)
);
```

#### 2. Mengirim Data - `xQueueSend()`
```cpp
BaseType_t xQueueSend(
  QueueHandle_t xQueue,         // Handle queue
  const void *pvItemToQueue,    // Pointer ke data yang dikirim
  TickType_t xTicksToWait       // Timeout jika queue penuh
);
```

#### 3. Menerima Data - `xQueueReceive()`
```cpp
BaseType_t xQueueReceive(
  QueueHandle_t xQueue,         // Handle queue
  void *pvBuffer,               // Buffer untuk menyimpan data
  TickType_t xTicksToWait       // Timeout jika queue kosong
);
```

### Contoh Lengkap: Sensor ke Display

```cpp
#include <Arduino_FreeRTOS.h>
#include <queue.h>

// Queue handle
QueueHandle_t xSensorQueue;

// Struktur data sensor
typedef struct {
  int temperature;
  int humidity;
  unsigned long timestamp;
} SensorData_t;

// Task Sensor (Producer)
void TaskSensor(void *pvParameters) {
  SensorData_t sensorData;
  
  for (;;) {
    // Baca sensor (simulasi)
    sensorData.temperature = analogRead(A0) / 10;  // Simulasi suhu
    sensorData.humidity = analogRead(A1) / 10;     // Simulasi kelembaban
    sensorData.timestamp = millis();
    
    // Kirim data ke queue
    if (xQueueSend(xSensorQueue, &sensorData, pdMS_TO_TICKS(100)) == pdPASS) {
      Serial.println("Data sensor dikirim ke queue");
    } else {
      Serial.println("Queue penuh! Data hilang.");
    }
    
    vTaskDelay(pdMS_TO_TICKS(2000));  // Baca sensor setiap 2 detik
  }
}

// Task Display (Consumer)
void TaskDisplay(void *pvParameters) {
  SensorData_t receivedData;
  Serial.begin(9600);
  
  for (;;) {
    // Terima data dari queue (blocking)
    if (xQueueReceive(xSensorQueue, &receivedData, portMAX_DELAY) == pdPASS) {
      // Tampilkan data
      Serial.println("=== DATA SENSOR ===");
      Serial.print("Suhu: ");
      Serial.print(receivedData.temperature);
      Serial.println("°C");
      
      Serial.print("Kelembaban: ");
      Serial.print(receivedData.humidity);
      Serial.println("%");
      
      Serial.print("Waktu: ");
      Serial.println(receivedData.timestamp);
      Serial.println();
    }
  }
}

void setup() {
  // Buat queue untuk 5 item SensorData_t
  xSensorQueue = xQueueCreate(5, sizeof(SensorData_t));
  
  if (xSensorQueue == NULL) {
    Serial.println("Gagal membuat queue!");
    while(1);  // Hang jika gagal
  }
  
  // Buat tasks
  xTaskCreate(TaskSensor, "Sensor", 256, NULL, 2, NULL);
  xTaskCreate(TaskDisplay, "Display", 256, NULL, 1, NULL);
  
  vTaskStartScheduler();
}

void loop() {}
```

### Keuntungan Queue:
- **Thread-safe**: Aman diakses dari multiple tasks
- **Blocking**: Task otomatis menunggu jika queue kosong/penuh
- **FIFO**: Data diterima sesuai urutan pengiriman
- **Decoupling**: Task producer dan consumer tidak perlu tahu satu sama lain

---

## 🔒 Sinkronisasi Task - Semaphore

### Apa itu Semaphore?
* **Semaphore = sinyal/penanda** untuk mengatur akses ke resource.
* Bisa dipakai untuk:

  * Membatasi **jumlah task** yang boleh masuk.
  * Sinkronisasi antar task (misalnya Task A memberi sinyal ke Task B).

### Jenis Semaphore

* **Counting Semaphore** → bisa lebih dari 1 resource. Misal ada **3 kursi kosong**, jadi bisa dipakai 3 anak sekaligus. Kalau kursi habis → yang lain harus nunggu.
* **Binary Semaphore** → hanya 0/1 (kosong atau penuh). Cocok untuk **sinyal** atau **resource tunggal**.

### Contoh di FreeRTOS

```cpp
#include <Arduino_FreeRTOS.h>
#include <semphr.h>

SemaphoreHandle_t xSemaphore;

void TaskA(void *pvParameters) {
  while (1) {
    if (xSemaphoreTake(xSemaphore, portMAX_DELAY)) {  // ambil semaphore
      Serial.println("Task A jalan");
      vTaskDelay(1000 / portTICK_PERIOD_MS);
      xSemaphoreGive(xSemaphore); // kembalikan semaphore
    }
  }
}

void TaskB(void *pvParameters) {
  while (1) {
    if (xSemaphoreTake(xSemaphore, portMAX_DELAY)) {
      Serial.println("Task B jalan");
      vTaskDelay(1000 / portTICK_PERIOD_MS);
      xSemaphoreGive(xSemaphore);
    }
  }
}

void setup() {
  Serial.begin(9600);
  xSemaphore = xSemaphoreCreateBinary();
  xSemaphoreGive(xSemaphore); // kasih "tiket" awal
  
  xTaskCreate(TaskA, "TaskA", 128, NULL, 1, NULL);
  xTaskCreate(TaskB, "TaskB", 128, NULL, 1, NULL);
  vTaskStartScheduler();
}

void loop() {}
```
📌 Hasil: Task A dan Task B **bergantian**, tidak bisa bareng-bareng masuk.

### Binary Semaphore - Signaling antar Task

```cpp
#include <Arduino_FreeRTOS.h>
#include <semphr.h>

SemaphoreHandle_t xBinarySemaphore;

// Task yang memberikan signal
void TaskProducer(void *pvParameters) {
  pinMode(2, INPUT_PULLUP);  // Button
  
  for (;;) {
    // Tunggu button ditekan
    if (digitalRead(2) == LOW) {
      Serial.println("Button ditekan! Mengirim signal...");
      
      // Berikan semaphore (signal)
      xSemaphoreGive(xBinarySemaphore);
      
      // Debounce
      vTaskDelay(pdMS_TO_TICKS(200));
    }
    
    vTaskDelay(pdMS_TO_TICKS(50));
  }
}

// Task yang menunggu signal
void TaskConsumer(void *pvParameters) {
  Serial.begin(9600);
  pinMode(LED_BUILTIN, OUTPUT);
  
  for (;;) {
    // Tunggu semaphore (blocking)
    if (xSemaphoreTake(xBinarySemaphore, portMAX_DELAY) == pdTRUE) {
      Serial.println("Signal diterima! Menyalakan LED...");
      
      // Blink LED sebagai respon
      for (int i = 0; i < 3; i++) {
        digitalWrite(LED_BUILTIN, HIGH);
        vTaskDelay(pdMS_TO_TICKS(200));
        digitalWrite(LED_BUILTIN, LOW);
        vTaskDelay(pdMS_TO_TICKS(200));
      }
    }
  }
}

void setup() {
  // Buat binary semaphore
  xBinarySemaphore = xSemaphoreCreateBinary();
  
  if (xBinarySemaphore == NULL) {
    Serial.println("Gagal membuat semaphore!");
    while(1);
  }
  
  xTaskCreate(TaskProducer, "Producer", 128, NULL, 2, NULL);
  xTaskCreate(TaskConsumer, "Consumer", 256, NULL, 1, NULL);
  
  vTaskStartScheduler();
}

void loop() {}
```

---

## 🛡️ Mutual Exclusion - Mutex

### Apa itu Mutex?
* **Mutex** = jenis khusus dari Binary Semaphore.
* Dipakai untuk **mutual exclusion** → memastikan **hanya satu task** yang boleh akses resource pada suatu waktu.
* Biasanya dipakai untuk **resource kritis**: port serial, I2C, LCD, file.

### Bedanya dengan Semaphore
* Semaphore bisa di-*give* oleh task lain.
* Mutex hanya boleh di-*give* oleh task yang mengambilnya → lebih aman (ada kepemilikan).
* Mutex punya fitur **priority inheritance** → mencegah deadlock karena prioritas rendah menghalangi task prioritas tinggi.

### Contoh di FreeRTOS

```cpp
#include <Arduino_FreeRTOS.h>
#include <semphr.h>

SemaphoreHandle_t xMutex;

void TaskPrintA(void *pvParameters) {
  while (1) {
    if (xSemaphoreTake(xMutex, portMAX_DELAY)) {
      Serial.println("Task A pakai Serial");
      vTaskDelay(500 / portTICK_PERIOD_MS);
      xSemaphoreGive(xMutex);
    }
  }
}

void TaskPrintB(void *pvParameters) {
  while (1) {
    if (xSemaphoreTake(xMutex, portMAX_DELAY)) {
      Serial.println("Task B pakai Serial");
      vTaskDelay(500 / portTICK_PERIOD_MS);
      xSemaphoreGive(xMutex);
    }
  }
}

void setup() {
  Serial.begin(9600);
  xMutex = xSemaphoreCreateMutex();
  
  xTaskCreate(TaskPrintA, "TaskA", 128, NULL, 1, NULL);
  xTaskCreate(TaskPrintB, "TaskB", 128, NULL, 1, NULL);
  vTaskStartScheduler();
}

void loop() {}
```

### Contoh: Shared Serial Port

```cpp
#include <Arduino_FreeRTOS.h>
#include <semphr.h>

SemaphoreHandle_t xSerialMutex;

// Task 1: Mengirim data sensor
void TaskSensor(void *pvParameters) {
  for (;;) {
    int sensorValue = analogRead(A0);
    
    // Ambil mutex sebelum akses serial
    if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(1000)) == pdTRUE) {
      // Critical section - akses exclusive ke serial
      Serial.print("[SENSOR] Nilai sensor: ");
      Serial.println(sensorValue);
      
      // Lepaskan mutex
      xSemaphoreGive(xSerialMutex);
    } else {
      // Timeout - tidak bisa akses serial
      Serial.println("[SENSOR] Timeout akses serial!");
    }
    
    vTaskDelay(pdMS_TO_TICKS(1000));
  }
}

// Task 2: Mengirim status sistem
void TaskStatus(void *pvParameters) {
  for (;;) {
    // Ambil mutex
    if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(1000)) == pdTRUE) {
      // Critical section
      Serial.print("[STATUS] Free memory: ");
      Serial.print(xPortGetFreeHeapSize());
      Serial.println(" bytes");
      
      Serial.print("[STATUS] Uptime: ");
      Serial.print(millis() / 1000);
      Serial.println(" seconds");
      
      // Lepaskan mutex
      xSemaphoreGive(xSerialMutex);
    }
    
    vTaskDelay(pdMS_TO_TICKS(2000));
  }
}

void setup() {
  Serial.begin(9600);
  
  // Buat mutex
  xSerialMutex = xSemaphoreCreateMutex();
  
  if (xSerialMutex == NULL) {
    Serial.println("Gagal membuat mutex!");
    while(1);
  }
  
  xTaskCreate(TaskSensor, "Sensor", 256, NULL, 1, NULL);
  xTaskCreate(TaskStatus, "Status", 256, NULL, 1, NULL);
  
  vTaskStartScheduler();
}

void loop() {}
```

### Output yang Diharapkan:
```
[SENSOR] Nilai sensor: 512
[STATUS] Free memory: 1024 bytes
[STATUS] Uptime: 2 seconds
[SENSOR] Nilai sensor: 515
[SENSOR] Nilai sensor: 520
[STATUS] Free memory: 1024 bytes
[STATUS] Uptime: 4 seconds
```

**Tanpa mutex**, output bisa tercampur:
```
[SENSOR] Nilai[STATUS] Free memory: 1024sensor: 512 bytes
```

---

## 📝 Ringkasan Sintaks Lengkap

### Task Management
| Fungsi | Parameter | Return | Keterangan |
|--------|-----------|---------|------------|
| `xTaskCreate()` | Function, Name, Stack, Params, Priority, Handle | `pdPASS/pdFAIL` | Membuat task baru |
| `vTaskDelete()` | TaskHandle | void | Menghapus task |
| `vTaskDelay()` | Ticks | void | Delay non-blocking |
| `vTaskDelayUntil()` | LastWake, Period | void | Delay periodik presisi |
| `vTaskSuspend()` | TaskHandle | void | Suspend task |
| `vTaskResume()` | TaskHandle | void | Resume task |
| `uxTaskPriorityGet()` | TaskHandle | Priority | Dapatkan prioritas |
| `vTaskPrioritySet()` | TaskHandle, Priority | void | Set prioritas |

### Queue Operations
| Fungsi | Parameter | Return | Keterangan |
|--------|-----------|---------|------------|
| `xQueueCreate()` | Length, ItemSize | QueueHandle | Buat queue |
| `xQueueSend()` | Queue, Item, Timeout | `pdPASS/pdFAIL` | Kirim ke queue |
| `xQueueReceive()` | Queue, Buffer, Timeout | `pdPASS/pdFAIL` | Terima dari queue |
| `uxQueueMessagesWaiting()` | Queue | Count | Jumlah item di queue |
| `xQueueReset()` | Queue | `pdPASS/pdFAIL` | Kosongkan queue |

### Semaphore Operations
| Fungsi | Parameter | Return | Keterangan |
|--------|-----------|---------|------------|
| `xSemaphoreCreateBinary()` | None | SemaphoreHandle | Buat binary semaphore |
| `xSemaphoreCreateCounting()` | Max, Initial | SemaphoreHandle | Buat counting semaphore |
| `xSemaphoreCreateMutex()` | None | SemaphoreHandle | Buat mutex |
| `xSemaphoreGive()` | Semaphore | `pdPASS/pdFAIL` | Berikan semaphore |
| `xSemaphoreTake()` | Semaphore, Timeout | `pdPASS/pdFAIL` | Ambil semaphore |

### Konstanta Penting
```cpp
#define portMAX_DELAY        0xFFFFFFFFUL  // Infinite timeout
#define pdMS_TO_TICKS(ms)    ((ms)/portTICK_PERIOD_MS)  // Convert ms ke ticks
#define portTICK_PERIOD_MS   1  // 1 tick = 1 ms di Arduino
#define pdPASS               1   // Return value sukses
#define pdFAIL               0   // Return value gagal
#define pdTRUE               1   // Boolean true
#define pdFALSE              0   // Boolean false
```

---

## 🧪 Sistem Monitoring Suhu dengan Alarm

### Tujuan:
Membuat sistem monitoring suhu dengan fitur:
1. Task pembaca sensor suhu
2. Task display LCD/Serial
3. Task alarm LED jika suhu > threshold
4. Task button untuk mengatur threshold

### Arsitektur Sistem:
```
[Sensor Task] --Queue--> [Display Task]
      |                      |
      +--Queue--> [Alarm Task] <--Semaphore-- [Button Task]
```

### Kode Lengkap:
```cpp
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <semphr.h>

// Pin definitions
#define SENSOR_PIN A0
#define ALARM_LED 2
#define BUTTON_PIN 3
#define BUZZER_PIN 4

// Global variables
QueueHandle_t xTempQueue;
SemaphoreHandle_t xAlarmSemaphore;
int tempThreshold = 30; // Default threshold 30°C

// Data structure
typedef struct {
  float temperature;
  unsigned long timestamp;
  bool alarmStatus;
} TempData_t;

// Task 1: Sensor Reading (Prioritas Tinggi)
void TaskSensor(void *pvParameters) {
  TempData_t tempData;
  
  for (;;) {
    // Baca sensor (konversi ADC ke Celsius)
    int rawValue = analogRead(SENSOR_PIN);
    tempData.temperature = (rawValue * 5.0 / 1024.0 - 0.5) * 100.0; // LM35
    tempData.timestamp = millis();
    tempData.alarmStatus = (tempData.temperature > tempThreshold);
    
    // Kirim data ke queue
    if (xQueueSend(xTempQueue, &tempData, pdMS_TO_TICKS(100)) != pdPASS) {
      Serial.println("ERROR: Queue penuh!");
    }
    
    // Jika suhu tinggi, trigger alarm
    if (tempData.alarmStatus) {
      xSemaphoreGive(xAlarmSemaphore);
    }
    
    vTaskDelay(pdMS_TO_TICKS(1000)); // Sampling setiap 1 detik
  }
}

// Task 2: Display (Prioritas Sedang)
void TaskDisplay(void *pvParameters) {
  TempData_t receivedData;
  Serial.begin(9600);
  
  Serial.println("=== SISTEM MONITORING SUHU ===");
  Serial.println("Threshold: " + String(tempThreshold) + "°C");
  Serial.println("===============================");
  
  for (;;) {
    if (xQueueReceive(xTempQueue, &receivedData, pdMS_TO_TICKS(2000)) == pdPASS) {
      // Format output
      Serial.print("[");
      Serial.print(receivedData.timestamp / 1000);
      Serial.print("s] Suhu: ");
      Serial.print(receivedData.temperature, 1);
      Serial.print("°C");
      
      if (receivedData.alarmStatus) {
        Serial.print(" *** ALARM! SUHU TINGGI! ***");
      } else {
        Serial.print(" - Normal");
      }
      Serial.println();
      
    } else {
      Serial.println("TIMEOUT: Tidak ada data sensor!");
    }
  }
}

// Task 3: Alarm System (Prioritas Tinggi)
void TaskAlarm(void *pvParameters) {
  pinMode(ALARM_LED, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  
  for (;;) {
    // Tunggu signal alarm dari sensor task
    if (xSemaphoreTake(xAlarmSemaphore, portMAX_DELAY) == pdTRUE) {
      Serial.println("!!! AKTIVASI ALARM !!!");
      
      // Blink LED dan buzzer selama 3 detik
      for (int i = 0; i < 6; i++) {
        digitalWrite(ALARM_LED, HIGH);
        tone(BUZZER_PIN, 1000); // 1kHz tone
        vTaskDelay(pdMS_TO_TICKS(250));
        
        digitalWrite(ALARM_LED, LOW);
        noTone(BUZZER_PIN);
        vTaskDelay(pdMS_TO_TICKS(250));
      }
    }
  }
}

// Task 4: Button Control (Prioritas Rendah)
void TaskButton(void *pvParameters) {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  
  bool lastButtonState = HIGH;
  bool currentButtonState;
  
  for (;;) {
    currentButtonState = digitalRead(BUTTON_PIN);
    
    // Deteksi falling edge (button pressed)
    if (lastButtonState == HIGH && currentButtonState == LOW) {
      // Increase threshold by 5°C
      tempThreshold += 5;
      if (tempThreshold > 50) {
        tempThreshold = 15; // Reset ke minimum
      }
      
      Serial.println("THRESHOLD DIUBAH: " + String(tempThreshold) + "°C");
      
      // Debounce delay
      vTaskDelay(pdMS_TO_TICKS(300));
    }
    
    lastButtonState = currentButtonState;
    vTaskDelay(pdMS_TO_TICKS(50)); // Polling setiap 50ms
  }
}

// Task 5: System Status (Prioritas Rendah)
void TaskStatus(void *pvParameters) {
  for (;;) {
    // Print system information setiap 10 detik
    vTaskDelay(pdMS_TO_TICKS(10000));
    
    Serial.println("\n=== STATUS SISTEM ===");
    Serial.println("Uptime: " + String(millis() / 1000) + " detik");
    Serial.println("Free Memory: " + String(xPortGetFreeHeapSize()) + " bytes");
    Serial.println("Threshold: " + String(tempThreshold) + "°C");
    
    // Cek jumlah pesan di queue
    UBaseType_t queueItems = uxQueueMessagesWaiting(xTempQueue);
    Serial.println("Queue items: " + String(queueItems));
    Serial.println("====================\n");
  }
}

void setup() {
  // Buat queue untuk data suhu (buffer 5 item)
  xTempQueue = xQueueCreate(5, sizeof(TempData_t));
  
  // Buat binary semaphore untuk alarm
  xAlarmSemaphore = xSemaphoreCreateBinary();
  
  // Validasi pembuatan objek
  if (xTempQueue == NULL || xAlarmSemaphore == NULL) {
    Serial.begin(9600);
    Serial.println("FATAL ERROR: Gagal membuat queue/semaphore!");
    while(1); // System halt
  }
  
  // Buat tasks dengan prioritas berbeda
  xTaskCreate(TaskSensor, "Sensor", 256, NULL, 3, NULL);    // Prioritas tertinggi
  xTaskCreate(TaskAlarm, "Alarm", 256, NULL, 3, NULL);      // Prioritas tertinggi
  xTaskCreate(TaskDisplay, "Display", 512, NULL, 2, NULL);  // Prioritas sedang
  xTaskCreate(TaskButton, "Button", 128, NULL, 1, NULL);    // Prioritas rendah
  xTaskCreate(TaskStatus, "Status", 256, NULL, 1, NULL);    // Prioritas rendah
  
  // Start scheduler
  vTaskStartScheduler();
}

void loop() {
  // Tidak digunakan
}
```

### Analisis Sistem:
1. **Task Sensor & Alarm** → Prioritas 3 (critical)
2. **Task Display** → Prioritas 2 (important)  
3. **Task Button & Status** → Prioritas 1 (background)

### Skenario Testing:
1. Panaskan sensor → Alarm aktif
2. Tekan button → Threshold berubah
3. Monitor serial output untuk melihat multitasking

---

## 🎯 Advanced - Smart Home Controller

### Tujuan:
Membuat sistem smart home dengan:
1. Multiple sensors (suhu, cahaya, PIR)
2. Multiple outputs (LED, relay, servo)
3. Command processing via serial
4. Data logging to EEPROM

### Arsitektur Advanced:
```cpp
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <semphr.h>
#include <EEPROM.h>
#include <Servo.h>

// Structures
typedef struct {
  float temperature;
  int lightLevel;
  bool motionDetected;
  unsigned long timestamp;
} SensorReading_t;

typedef struct {
  char command[16];
  int deviceId;
  int value;
} Command_t;

typedef enum {
  DEVICE_LED = 1,
  DEVICE_RELAY = 2,
  DEVICE_SERVO = 3,
  DEVICE_ALL = 99
} DeviceType_t;

// Global objects
QueueHandle_t xSensorQueue;
QueueHandle_t xCommandQueue;
SemaphoreHandle_t xEEPROMMutex;
SemaphoreHandle_t xSerialMutex;
Servo doorServo;

// Configuration
#define MAX_LOG_ENTRIES 20
#define EEPROM_START_ADDR 0

// Sensor Task - Multi-sensor reading
void TaskSensorHub(void *pvParameters) {
  SensorReading_t reading;
  
  // Initialize sensors
  pinMode(A0, INPUT); // Temperature sensor
  pinMode(A1, INPUT); // Light sensor  
  pinMode(2, INPUT);  // PIR sensor
  
  for (;;) {
    // Read all sensors
    reading.temperature = analogRead(A0) * 0.48828125; // Convert to Celsius
    reading.lightLevel = map(analogRead(A1), 0, 1023, 0, 100); // Percentage
    reading.motionDetected = digitalRead(2);
    reading.timestamp = millis();
    
    // Send to queue
    xQueueSend(xSensorQueue, &reading, pdMS_TO_TICKS(100));
    
    vTaskDelay(pdMS_TO_TICKS(2000)); // Read every 2 seconds
  }
}

// Command Parser Task
void TaskCommandParser(void *pvParameters) {
  Command_t cmd;
  String inputBuffer = "";
  
  for (;;) {
    // Check for serial input
    if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(100)) == pdTRUE) {
      if (Serial.available()) {
        char c = Serial.read();
        if (c == '\n') {
          // Parse command: "LED:1:255" atau "SERVO:3:90"
          int firstColon = inputBuffer.indexOf(':');
          int secondColon = inputBuffer.indexOf(':', firstColon + 1);
          
          if (firstColon > 0 && secondColon > 0) {
            inputBuffer.substring(0, firstColon).toCharArray(cmd.command, 16);
            cmd.deviceId = inputBuffer.substring(firstColon + 1, secondColon).toInt();
            cmd.value = inputBuffer.substring(secondColon + 1).toInt();
            
            // Send to command queue
            xQueueSend(xCommandQueue, &cmd, 0);
            
            Serial.println("Command received: " + inputBuffer);
          } else {
            Serial.println("Invalid format! Use: COMMAND:ID:VALUE");
          }
          
          inputBuffer = "";
        } else if (c != '\r') {
          inputBuffer += c;
        }
      }
      xSemaphoreGive(xSerialMutex);
    }
    
    vTaskDelay(pdMS_TO_TICKS(50));
  }
}

// Device Controller Task
void TaskDeviceController(void *pvParameters) {
  Command_t receivedCmd;
  
  // Initialize devices
  pinMode(3, OUTPUT);  // LED 1
  pinMode(5, OUTPUT);  // LED 2  
  pinMode(7, OUTPUT);  // Relay 1
  pinMode(8, OUTPUT);  // Relay 2
  doorServo.attach(9); // Servo motor
  
  for (;;) {
    if (xQueueReceive(xCommandQueue, &receivedCmd, portMAX_DELAY) == pdTRUE) {
      
      if (strcmp(receivedCmd.command, "LED") == 0) {
        int pin = (receivedCmd.deviceId == 1) ? 3 : 5;
        analogWrite(pin, receivedCmd.value);
        
      } else if (strcmp(receivedCmd.command, "RELAY") == 0) {
        int pin = (receivedCmd.deviceId == 1) ? 7 : 8;
        digitalWrite(pin, receivedCmd.value ? HIGH : LOW);
        
      } else if (strcmp(receivedCmd.command, "SERVO") == 0) {
        doorServo.write(constrain(receivedCmd.value, 0, 180));
        
      } else if (strcmp(receivedCmd.command, "STATUS") == 0) {
        printSystemStatus();
      }
      
      // Log command execution
      logCommandExecution(receivedCmd);
    }
  }
}

// Data Logger Task
void TaskDataLogger(void *pvParameters) {
  SensorReading_t reading;
  int logIndex = 0;
  
  for (;;) {
    if (xQueueReceive(xSensorQueue, &reading, portMAX_DELAY) == pdTRUE) {
      
      // Take EEPROM mutex
      if (xSemaphoreTake(xEEPROMMutex, pdMS_TO_TICKS(1000)) == pdTRUE) {
        
        // Write to EEPROM
        int addr = EEPROM_START_ADDR + (logIndex * sizeof(SensorReading_t));
        EEPROM.put(addr, reading);
        
        logIndex = (logIndex + 1) % MAX_LOG_ENTRIES; // Circular buffer
        
        xSemaphoreGive(xEEPROMMutex);
        
        // Also display current reading
        displaySensorData(reading);
      }
    }
  }
}

// Watchdog Task - System health monitoring
void TaskWatchdog(void *pvParameters) {
  for (;;) {
    vTaskDelay(pdMS_TO_TICKS(30000)); // Every 30 seconds
    
    if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(1000)) == pdTRUE) {
      Serial.println("\n=== SYSTEM HEALTH CHECK ===");
      Serial.println("Uptime: " + String(millis() / 1000) + "s");
      Serial.println("Free heap: " + String(xPortGetFreeHeapSize()) + " bytes");
      
      // Check queue status
      Serial.println("Sensor queue items: " + String(uxQueueMessagesWaiting(xSensorQueue)));
      Serial.println("Command queue items: " + String(uxQueueMessagesWaiting(xCommandQueue)));
      
      Serial.println("===========================\n");
      xSemaphoreGive(xSerialMutex);
    }
  }
}

// Helper functions
void displaySensorData(SensorReading_t data) {
  if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(500)) == pdTRUE) {
    Serial.println("--- Sensor Data ---");
    Serial.println("Temp: " + String(data.temperature, 1) + "°C");
    Serial.println("Light: " + String(data.lightLevel) + "%");
    Serial.println("Motion: " + String(data.motionDetected ? "YES" : "NO"));
    Serial.println("Time: " + String(data.timestamp));
    Serial.println("------------------");
    
    xSemaphoreGive(xSerialMutex);
  }
}

void logCommandExecution(Command_t cmd) {
  if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(500)) == pdTRUE) {
    Serial.println("EXECUTED: " + String(cmd.command) + 
                  " ID:" + String(cmd.deviceId) + 
                  " VAL:" + String(cmd.value));
    xSemaphoreGive(xSerialMutex);
  }
}

void printSystemStatus() {
  if (xSemaphoreTake(xSerialMutex, pdMS_TO_TICKS(1000)) == pdTRUE) {
    Serial.println("\n=== SMART HOME STATUS ===");
    Serial.println("System uptime: " + String(millis() / 1000) + " seconds");
    Serial.println("Available commands:");
    Serial.println("  LED:1:0-255    - Control LED brightness");
    Serial.println("  RELAY:1:0/1    - Control relay on/off");
    Serial.println("  SERVO:3:0-180  - Control servo angle");
    Serial.println("  STATUS:0:0     - Show this status");
    Serial.println("=========================\n");
    
    xSemaphoreGive(xSerialMutex);
  }
}

void setup() {
  Serial.begin(9600);
  Serial.println("Smart Home Controller Starting...");
  
  // Create queues
  xSensorQueue = xQueueCreate(10, sizeof(SensorReading_t));
  xCommandQueue = xQueueCreate(5, sizeof(Command_t));
  
  // Create mutexes
  xEEPROMMutex = xSemaphoreCreateMutex();
  xSerialMutex = xSemaphoreCreateMutex();
  
  // Validation
  if (!xSensorQueue || !xCommandQueue || !xEEPROMMutex || !xSerialMutex) {
    Serial.println("FATAL: Failed to create FreeRTOS objects!");
    while(1);
  }
  
  // Create tasks with different priorities
  xTaskCreate(TaskSensorHub, "SensorHub", 512, NULL, 3, NULL);
  xTaskCreate(TaskCommandParser, "CmdParser", 512, NULL, 2, NULL);
  xTaskCreate(TaskDeviceController, "DeviceCtrl", 512, NULL, 2, NULL);
  xTaskCreate(TaskDataLogger, "DataLogger", 512, NULL, 1, NULL);
  xTaskCreate(TaskWatchdog, "Watchdog", 256, NULL, 1, NULL);
  
  Serial.println("All tasks created successfully!");
  printSystemStatus();
  
  vTaskStartScheduler();
}

void loop() {
  // Never reached
}
```

### Contoh Penggunaan:
```bash
# Via Serial Monitor, kirim:
LED:1:128     # LED 1 brightness 50%
RELAY:1:1     # Relay 1 ON
SERVO:3:90    # Servo ke posisi 90 derajat
STATUS:0:0    # Tampilkan status sistem
```

---

## ⚠️ Troubleshooting dan Debugging

### 1. Stack Overflow
**Gejala:** Arduino restart terus-menerus, watchdog reset

**Penyebab:**
- Stack size task terlalu kecil
- Recursive function calls
- Large local variables

**Solusi:**
```cpp
// Perbesar stack size
xTaskCreate(MyTask, "Task", 512, NULL, 1, NULL); // dari 128 ke 512

// Monitor stack usage
void checkStackUsage() {
  UBaseType_t stackHighWaterMark = uxTaskGetStackHighWaterMark(NULL);
  Serial.print("Stack remaining: ");
  Serial.print(stackHighWaterMark * sizeof(StackType_t));
  Serial.println(" bytes");
}
```

### 2. Heap Exhaustion
**Gejala:** `xTaskCreate()` gagal, `xQueueCreate()` return NULL

**Debugging:**
```cpp
void memoryDiagnostic() {
  Serial.print("Free heap: ");
  Serial.print(xPortGetFreeHeapSize());
  Serial.println(" bytes");
  
  Serial.print("Minimum ever free heap: ");
  Serial.print(xPortGetMinimumEverFreeHeapSize());
  Serial.println(" bytes");
}
```

**Solusi:**
- Kurangi jumlah task
- Perkecil stack size jika memungkinkan
- Optimasi penggunaan memory

### 3. Priority Inversion
**Gejala:** High priority task terblokir oleh low priority task

**Contoh Masalah:**
```cpp
// SALAH - bisa terjadi priority inversion
SemaphoreHandle_t mutex;

void HighPriorityTask(void *p) {
  xSemaphoreTake(mutex, portMAX_DELAY); // Blocked oleh low priority
  // Critical work
  xSemaphoreGive(mutex);
}

void LowPriorityTask(void *p) {
  xSemaphoreTake(mutex, portMAX_DELAY);
  vTaskDelay(pdMS_TO_TICKS(1000)); // Hold mutex too long!
  xSemaphoreGive(mutex);
}
```

**Solusi:**
```cpp
// BENAR - gunakan mutex (priority inheritance)
SemaphoreHandle_t mutex = xSemaphoreCreateMutex(); // Bukan CreateBinary!

// Atau minimasi critical section
void LowPriorityTask(void *p) {
  // Prepare data outside critical section
  int data = prepareData();
  
  xSemaphoreTake(mutex, portMAX_DELAY);
  // Keep critical section minimal
  writeData(data);
  xSemaphoreGive(mutex);
}
```

### 4. Deadlock
**Gejala:** System hang, beberapa task blocked permanent

**Contoh Masalah:**
```cpp
// Task 1
xSemaphoreTake(mutexA, portMAX_DELAY);
xSemaphoreTake(mutexB, portMAX_DELAY); // Blocked jika Task2 pegang mutexB

// Task 2  
xSemaphoreTake(mutexB, portMAX_DELAY);
xSemaphoreTake(mutexA, portMAX_DELAY); // Blocked jika Task1 pegang mutexA
```

**Solusi:**
```cpp
// Selalu ambil mutex dalam urutan yang sama
// Task 1
xSemaphoreTake(mutexA, portMAX_DELAY); // Urutan: A dulu, B kemudian
xSemaphoreTake(mutexB, portMAX_DELAY);
// ... work
xSemaphoreGive(mutexB);
xSemaphoreGive(mutexA);

// Task 2 - urutan sama!
xSemaphoreTake(mutexA, portMAX_DELAY); // A dulu juga
xSemaphoreTake(mutexB, portMAX_DELAY); // B kemudian
// ... work  
xSemaphoreGive(mutexB);
xSemaphoreGive(mutexA);
```

### 5. Task Tidak Berjalan
**Gejala:** Beberapa task tidak pernah execute

**Debugging:**
```cpp
void taskListDiagnostic() {
  char taskListBuffer[1024];
  vTaskList(taskListBuffer);
  Serial.println("=== TASK LIST ===");
  Serial.println(taskListBuffer);
  
  Serial.println("\n=== RUNTIME STATS ===");
  vTaskGetRunTimeStats(taskListBuffer);
  Serial.println(taskListBuffer);
}
```

**Penyebab & Solusi:**
- **Priority terlalu rendah** → Naikkan priority
- **Task tidak pernah yield** → Tambahkan `vTaskDelay()`
- **Blocking call tanpa timeout** → Gunakan timeout

---

## 📊 Performance Optimization

### 1. Task Priority Assignment
```cpp
// Pedoman prioritas:
#define PRIORITY_CRITICAL    4  // Interrupt-like, time critical
#define PRIORITY_HIGH        3  // Real-time response required  
#define PRIORITY_NORMAL      2  // Regular application tasks
#define PRIORITY_LOW         1  // Background, housekeeping
#define PRIORITY_IDLE        0  // Almost never use this

// Contoh assignment:
xTaskCreate(TaskEmergencyStop, "Emergency", 256, NULL, PRIORITY_CRITICAL, NULL);
xTaskCreate(TaskSensorRead, "Sensor", 256, NULL, PRIORITY_HIGH, NULL);
xTaskCreate(TaskDisplay, "Display", 512, NULL, PRIORITY_NORMAL, NULL);
xTaskCreate(TaskDataLog, "DataLog", 256, NULL, PRIORITY_LOW, NULL);
```

### 2. Memory Optimization
```cpp
// Gunakan stack size yang tepat
void optimizeStackSize() {
  // Terlalu besar = waste memory
  xTaskCreate(SimpleTask, "Simple", 1024, NULL, 1, NULL); // BOROS!
  
  // Terlalu kecil = stack overflow  
  xTaskCreate(ComplexTask, "Complex", 64, NULL, 1, NULL);   // BAHAYA!
  
  // Pas = optimal
  xTaskCreate(SimpleTask, "Simple", 128, NULL, 1, NULL);   // TEPAT
  xTaskCreate(ComplexTask, "Complex", 512, NULL, 1, NULL); // TEPAT
}

// Monitor dan adjust
void monitorStackUsage(TaskHandle_t task) {
  UBaseType_t highWaterMark = uxTaskGetStackHighWaterMark(task);
  Serial.print("Stack unused: ");
  Serial.print(highWaterMark * sizeof(StackType_t));
  Serial.println(" bytes");
  
  // Jika unused terlalu banyak, kurangi stack size
  // Jika unused terlalu sedikit (<100 bytes), perbesar stack size
}
```

### 3. Communication Optimization
```cpp
// Queue vs Task Notification
// Queue: Flexibel, bisa multiple data types
QueueHandle_t queue = xQueueCreate(10, sizeof(MyStruct_t));

// Task Notification: Lebih cepat, hanya untuk simple signaling
void FastProducer(void *p) {
  // 3x lebih cepat dari semaphore!
  xTaskNotifyGive(ConsumerTaskHandle);
}

void FastConsumer(void *p) {
  ulTaskNotifyTake(pdTRUE, portMAX_DELAY);
  // Process notification
}
```

---

## 📚 Best Practices

### 1. Task Design Principles
```cpp
// ✅ GOOD: Single responsibility
void TaskLED(void *p) {
  for (;;) {
    blinkLED();
    vTaskDelay(pdMS_TO_TICKS(500));
  }
}

void TaskSensor(void *p) {
  for (;;) {
    readSensor();
    vTaskDelay(pdMS_TO_TICKS(1000));
  }
}

// ❌ BAD: Multiple responsibilities
void TaskEverything(void *p) {
  for (;;) {
    blinkLED();           // LED control
    readSensor();         // Sensor reading  
    processWiFi();        // Network handling
    updateDisplay();      // Display update
    // Terlalu banyak tanggung jawab!
  }
}
```

### 2. Error Handling
```cpp
// Selalu cek return value
BaseType_t result = xTaskCreate(MyTask, "Task", 256, NULL, 1, NULL);
if (result != pdPASS) {
  Serial.println("Failed to create task!");
  // Handle error appropriately
}

// Gunakan timeout untuk blocking calls
if (xQueueReceive(queue, &data, pdMS_TO_TICKS(5000)) == pdTRUE) {
  // Process data
} else {
  Serial.println("Queue receive timeout!");
  // Handle timeout
}
```

### 3. Resource Management
```cpp
// Gunakan RAII pattern untuk mutex
class MutexGuard {
private:
  SemaphoreHandle_t mutex;
  bool acquired;
  
public:
  MutexGuard(SemaphoreHandle_t m, TickType_t timeout) : mutex(m) {
    acquired = (xSemaphoreTake(mutex, timeout) == pdTRUE);
  }
  
  ~MutexGuard() {
    if (acquired) {
      xSemaphoreGive(mutex);
    }
  }
  
  bool isAcquired() { return acquired; }
};

// Penggunaan:
void safeFunction() {
  MutexGuard guard(serialMutex, pdMS_TO_TICKS(1000));
  if (guard.isAcquired()) {
    Serial.println("Safe serial access");
    // Mutex otomatis dilepas saat keluar scope
  }
}
```
---
