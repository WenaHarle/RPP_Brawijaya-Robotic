# 📘 Pemrograman Non-Blocking dengan `millis()` pada Arduino

## 1. Pendahuluan

Biasanya pemula menggunakan fungsi `delay(ms)` untuk membuat jeda waktu.
👉 Masalahnya: `delay()` membuat Arduino **berhenti total** (tidak bisa melakukan hal lain selama delay).

Solusinya: gunakan fungsi **`millis()`**, yaitu "jam tangan internal" Arduino.

* `millis()` mengembalikan jumlah milidetik sejak Arduino dinyalakan.
* Dengan ini, kita bisa membuat **timer non-blocking**: Arduino tetap jalan sambil cek waktu.

---

## 2. Cara Kerja (Bahasa Gampang)

Bayangkan Arduino seperti anak yang punya **jam tangan**:

* Setiap detik dia bisa lihat jam → "Oh, sudah waktunya kerjain ini!".
* Karena pakai jam, dia tidak perlu duduk diam menunggu → bisa sambil kerjain tugas lain.

---

## 3. Contoh 1: Blink 1 LED dengan millis()

```cpp
const int LED = 8;
unsigned long prevMillis = 0;
const long interval = 1000; // 1 detik
bool ledState = LOW;

void setup() {
  pinMode(LED, OUTPUT);
}

void loop() {
  unsigned long currentMillis = millis();

  if (currentMillis - prevMillis >= interval) {
    prevMillis = currentMillis;
    ledState = !ledState;              // toggle ON/OFF
    digitalWrite(LED, ledState);
  }

  // Arduino bisa melakukan hal lain di sini tanpa terganggu
}
```

📌 Penjelasan:

* `prevMillis` = catatan kapan terakhir LED berubah.
* `interval` = target waktu tunggu (1 detik).
* Jika selisih waktu sudah ≥ interval → LED ganti status.

---

## 4. Contoh 2: 1 LED + 1 Tombol

LED akan **toggle** setiap kali tombol ditekan, dengan **debounce** menggunakan millis.

```cpp
const int LED = 8;
const int BUTTON = 2;

bool ledState = LOW;
bool lastButtonState = HIGH;
unsigned long lastDebounceTime = 0;
const long debounceDelay = 50; // ms

void setup() {
  pinMode(LED, OUTPUT);
  pinMode(BUTTON, INPUT_PULLUP); // tombol ke GND
}

void loop() {
  int reading = digitalRead(BUTTON);
  unsigned long currentMillis = millis();

  // cek perubahan tombol
  if (reading != lastButtonState) {
    lastDebounceTime = currentMillis;
  }

  // valid jika stabil lebih dari debounceDelay
  if ((currentMillis - lastDebounceTime) > debounceDelay) {
    if (reading == LOW) {
      ledState = !ledState;
      digitalWrite(LED, ledState);
    }
  }

  lastButtonState = reading;
}
```

📌 Penjelasan:

* `millis()` dipakai untuk filter bouncing tombol.
* Jika tombol ditekan lebih dari 50 ms stabil → dianggap valid.

---

## 5. Contoh 3: 2 LED dengan interval berbeda

```cpp
const int LED1 = 8;
const int LED2 = 9;

unsigned long prevMillis1 = 0;
unsigned long prevMillis2 = 0;

const long interval1 = 1000; // 1 detik
const long interval2 = 500;  // 0.5 detik

bool state1 = LOW;
bool state2 = LOW;

void setup() {
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);
}

void loop() {
  unsigned long currentMillis = millis();

  if (currentMillis - prevMillis1 >= interval1) {
    prevMillis1 = currentMillis;
    state1 = !state1;
    digitalWrite(LED1, state1);
  }

  if (currentMillis - prevMillis2 >= interval2) {
    prevMillis2 = currentMillis;
    state2 = !state2;
    digitalWrite(LED2, state2);
  }
}
```

📌 Penjelasan:

* LED1 kedip tiap 1 detik.
* LED2 kedip tiap 0.5 detik.
* Semua bisa jalan **bersamaan** tanpa saling ganggu.

---

## 6. Keunggulan millis()

✅ Arduino tetap bisa multitasking (sensor, komunikasi, logika).
✅ Tidak menghentikan program seperti `delay()`.
✅ Cocok untuk sistem real-time sederhana.

---

## 7. Kesimpulan

* `millis()` = "jam tangan" Arduino untuk cek waktu.
* Digunakan untuk membuat timer **non-blocking**.
* Bisa dipakai untuk **LED blink, debounce tombol, multitasking sederhana**.

Apakah kamu mau saya bikinkan **flowchart sederhana** juga (misalnya: cek waktu → sudah lewat? → ubah LED) untuk melengkapi README ini biar lebih visual?
