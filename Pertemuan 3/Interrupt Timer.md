## 1. Pendahuluan

Arduino (misalnya **UNO dengan ATmega328P**) memiliki **perangkat keras internal** bernama **Timer/Counter**.

* **Timer** → menghitung detakan clock.
* **Counter** → bisa juga menghitung event eksternal (misalnya pulsa dari sensor).
* Timer ini bisa diatur agar memicu **interrupt** secara periodik, sehingga program dapat menjalankan tugas otomatis tanpa harus menunggu.

👉 **Timer Interrupt** sangat berguna untuk:

* Membuat program real-time.
* Menghindari blocking dari `delay()`.
* Memberikan **presisi waktu lebih tinggi** dibanding `millis()`.

---

## 2. Konsep Dasar Timer

Mikrokontroler punya **clock utama** (misalnya 16 MHz di Arduino UNO).
Timer bekerja dengan cara:

1. Menghitung setiap detakan clock (atau clock yang sudah dibagi prescaler).
2. Jika hitungan mencapai nilai tertentu (compare match atau overflow), timer memicu **interrupt**.
3. Interrupt ini memanggil **ISR (Interrupt Service Routine)**, yaitu fungsi khusus yang kita definisikan.

---

## 3. Jenis Timer di Arduino UNO (ATmega328P)

| Timer  | Resolusi | Digunakan untuk                                         |
| ------ | -------- | ------------------------------------------------------- |
| Timer0 | 8-bit    | dipakai Arduino untuk `millis()`, `micros()`, `delay()` |
| Timer1 | 16-bit   | cocok untuk interval panjang, PWM presisi tinggi        |
| Timer2 | 8-bit    | bisa digunakan untuk tone() atau tugas waktu lainnya    |

📌 **Rekomendasi belajar:** gunakan **Timer1**, karena 16-bit → bisa lebih fleksibel, dan tidak bentrok dengan `millis()`.

---

## 4. Mode Operasi Timer

Arduino Timer dapat bekerja dalam beberapa mode (tingkat lanjut, biasanya diatur lewat register):

* **Normal mode** → hitungan naik terus sampai overflow.
* **CTC (Clear Timer on Compare Match)** → reset timer setiap kali mencapai nilai pembanding tertentu.
* **PWM mode** → menghasilkan sinyal PWM dengan duty cycle tertentu.

Untuk praktikum awal, kita biasanya pakai **CTC mode** dengan nilai pembanding (OCR1A).

---

## 5. Dasar Pemrograman Timer Interrupt

### 5.1 Struktur ISR

Arduino menyediakan makro `ISR()` untuk mendefinisikan fungsi interrupt:

```cpp
ISR(TIMER1_COMPA_vect) {
  // kode yang dijalankan otomatis saat Timer1 match
}
```
### 5.2 Setup Timer Interrupt

```cpp
// Atur Timer1 dalam mode CTC
noInterrupts();           // 1. matikan interrupt global sementara
TCCR1A = 0;               // 2. reset register kontrol A
TCCR1B = 0;               // 3. reset register kontrol B

OCR1A = 15624;            // 4. nilai compare match (1 Hz dengan 16MHz clock & prescaler 1024)
TCCR1B |= (1 << WGM12);   // 5. aktifkan mode CTC (Clear Timer on Compare Match)
TCCR1B |= (1 << CS12) | (1 << CS10);  // 6. pilih prescaler = 1024
TIMSK1 |= (1 << OCIE1A);  // 7. enable interrupt saat Timer1 == OCR1A
interrupts();             // 8. hidupkan interrupt global kembali
```

#### `noInterrupts();`

* Ini **mematikan semua interrupt global sementara**.
* Tujuannya: supaya saat kita mengatur register, tidak ada interrupt lain yang “nyelonong” masuk.

---

#### `TCCR1A = 0;` dan `TCCR1B = 0;`

* **TCCR1A** dan **TCCR1B** = register kontrol untuk **Timer1**.
* Diset `0` dulu agar **bersih** → menghindari setting lama yang masih tersisa.

---

#### `OCR1A = 15624;`

* **OCR1A (Output Compare Register A)** adalah nilai target pembanding.
* Timer1 akan menghitung dari 0 naik terus.
* Begitu nilai timer = `OCR1A`, maka **event compare match terjadi** → ISR dipanggil.
* Angka `15624` didapat dari rumus:

$$
\text{OCR1A} = \frac{F_{CPU}}{\text{Prescaler} \times \text{Frekuensi}} - 1
$$

Dengan:

* `F_CPU = 16 MHz` (Arduino UNO clock)
* Prescaler = `1024`
* Frekuensi = `1 Hz` (1 detik)

$$
OCR1A = \frac{16.000.000}{1024 \times 1} - 1 = 15624
$$

---

#### `TCCR1B |= (1 << WGM12);`

* Mengatur **WGM12 (Waveform Generation Mode bit 2)** = 1.
* Artinya, Timer1 bekerja dalam **CTC mode (Clear Timer on Compare)**.
* Jadi, setiap kali timer mencapai OCR1A → timer reset ke 0 lagi.

---

#### `TCCR1B |= (1 << CS12) | (1 << CS10);`

* Ini mengatur **prescaler clock** untuk Timer1.
* Bit CS12 = 1 dan CS10 = 1 → prescaler = 1024.
* Artinya, timer menghitung setiap **1024 detak clock utama**.

Clock asli = 16 MHz → setelah prescaler 1024 → kecepatan hitung timer = 16.000.000 / 1024 = **15625 Hz**.

---

#### `TIMSK1 |= (1 << OCIE1A);`

* Mengaktifkan **Output Compare Interrupt Enable A (OCIE1A)**.
* Artinya: setiap kali Timer1 = OCR1A → interrupt `TIMER1_COMPA_vect` dipanggil.

---

#### `interrupts();`

* Mengaktifkan kembali interrupt global.
* Jadi sekarang sistem siap memanggil ISR saat timer match.


#### 🔎 Apa itu CS10?

* **CS** = Clock Select.
* **CS10** adalah **bit ke-0** di register kontrol `TCCR1B`.
* Bersama dengan **CS11** (bit ke-1) dan **CS12** (bit ke-2), 3 bit ini menentukan **sumber clock / prescaler** untuk Timer1.

| CS12 | CS11 | CS10 | Sumber Clock Timer1             |
| ---- | ---- | ---- | ------------------------------- |
| 0    | 0    | 0    | Timer berhenti (No clock)       |
| 0    | 0    | 1    | No prescaling (langsung 16 MHz) |
| 0    | 1    | 0    | Prescaler 8                     |
| 0    | 1    | 1    | Prescaler 64                    |
| 1    | 0    | 0    | Prescaler 256                   |
| 1    | 0    | 1    | Prescaler 1024                  |
| 1    | 1    | 0    | External clock, falling edge    |
| 1    | 1    | 1    | External clock, rising edge     |

---

## 📘 Jadi, fungsi **CS10** adalah:

* Kalau sendirian (`CS10 = 1`, CS11 = 0, CS12 = 0) → Timer pakai clock **tanpa prescaler** (langsung 16 MHz).
* Kalau dipakai bersama CS12 (`CS12 = 1` dan `CS10 = 1`) → Timer pakai prescaler **1024** (16 MHz ÷ 1024 = 15625 Hz).

👉 Itulah yang dipakai di contoh:

```cpp
TCCR1B |= (1 << CS12) | (1 << CS10);
```

Artinya kita set **CS12 = 1** dan **CS10 = 1** → Timer1 jalan dengan **prescaler 1024**.

---

📌 Aturan ISR:

* Jangan pakai `delay()` atau `Serial.print()` di dalam ISR.
* Buat sesingkat mungkin → misalnya hanya ubah variabel `flag` atau `counter`.

---

### 5.2 Contoh: Blink LED dengan Timer1 (tanpa library)

```cpp
const int LED = 13;

void setup() {
  pinMode(LED, OUTPUT);

  // Atur Timer1 dalam mode CTC
  noInterrupts();           // matikan interrupt global sementara
  TCCR1A = 0;               // reset register kontrol
  TCCR1B = 0;

  OCR1A = 15624;            // nilai compare match (1 Hz dengan 16MHz clock & prescaler 1024)
  TCCR1B |= (1 << WGM12);   // CTC mode
  TCCR1B |= (1 << CS12) | (1 << CS10);  // prescaler 1024
  TIMSK1 |= (1 << OCIE1A);  // enable Timer1 compare interrupt
  interrupts();             // hidupkan interrupt global
}

ISR(TIMER1_COMPA_vect) {
  digitalWrite(LED, !digitalRead(LED)); // toggle LED
}

void loop() {
  // Loop kosong → LED tetap berkedip otomatis
}
```

📌 Penjelasan:

* Dengan clock 16 MHz dan prescaler 1024, 1 detik = 16.000.000 / 1024 ≈ 15625 hitungan.
* Jadi kita set `OCR1A = 15624` → interrupt dipanggil tiap 1 detik.

---

## 6. Contoh Lanjutan

### 6.1 Stopwatch sederhana (hitung detik)

```cpp
volatile unsigned long seconds = 0;

void setup() {
  Serial.begin(9600);
  noInterrupts();

  TCCR1A = 0;
  TCCR1B = 0;
  OCR1A = 15624;              // hitungan 1 detik
  TCCR1B |= (1 << WGM12);
  TCCR1B |= (1 << CS12) | (1 << CS10);
  TIMSK1 |= (1 << OCIE1A);

  interrupts();
}

ISR(TIMER1_COMPA_vect) {
  seconds++;
}

void loop() {
  Serial.print("Waktu: ");
  Serial.print(seconds);
  Serial.println(" detik");
  delay(1000);
}
```

👉 Timer1 otomatis menambah variabel `seconds` tiap detik, loop hanya mencetak nilainya.

---

### 6.2 Sampling Sensor Periodik (misalnya baca sensor suhu tiap 100 ms)

```cpp
volatile bool sampleFlag = false;

void setup() {
  Serial.begin(9600);
  noInterrupts();

  TCCR1A = 0;
  TCCR1B = 0;
  OCR1A = 1562;               // 100 ms (16MHz/1024 ≈ 15625 per detik → 1/10 detik = 1562)
  TCCR1B |= (1 << WGM12);
  TCCR1B |= (1 << CS12) | (1 << CS10);
  TIMSK1 |= (1 << OCIE1A);

  interrupts();
}

ISR(TIMER1_COMPA_vect) {
  sampleFlag = true; // set flag → tandai siap sampling
}

void loop() {
  if (sampleFlag) {
    sampleFlag = false;
    int sensorValue = analogRead(A0);
    Serial.println(sensorValue);
  }
}
```

👉 ISR hanya set `flag`, proses bacaan sensor dilakukan di `loop()`.

---

## 7. Perbandingan `millis()` vs Timer Interrupt

| Aspek        | millis()                        | Timer Interrupt                        |
| ------------ | ------------------------------- | -------------------------------------- |
| Implementasi | Pakai fungsi Arduino siap pakai | Akses register atau library tambahan   |
| Presisi      | ms (cukup untuk blink/umum)     | bisa µs (lebih presisi, real-time)     |
| Beban CPU    | Cek manual di `loop()`          | Otomatis, non-blocking                 |
| Cocok untuk  | LED blink, debounce tombol      | Sampling sensor cepat, PID, PWM custom |

---

## 8. Aturan Penting

* ISR harus **secepat mungkin** → jangan isi dengan logika rumit.
* Variabel yang dipakai di ISR + `loop()` harus diberi kata kunci **`volatile`**.
* Jangan ganggu **Timer0** (karena dipakai `millis()`/`delay()`).
* Gunakan **Timer1** atau **Timer2** untuk percobaan.
* Untuk kemudahan, gunakan library:

  * [TimerOne](https://github.com/PaulStoffregen/TimerOne)
  * [MsTimer2](https://github.com/PaulStoffregen/MsTimer2)

---

## 9. Aplikasi Nyata

* **Sistem kendali motor** → jalankan kontrol PID tiap 1 ms.
* **Sistem sampling sensor** → ambil data periodik dengan presisi.
* **Jam digital / stopwatch** → hitung detik real-time.
* **PWM custom** → buat sinyal persegi untuk komunikasi atau driver khusus.

---

## 10. Kesimpulan

* **Timer Interrupt = alarm internal** di mikrokontroler.
* Memberikan **presisi tinggi** dan **non-blocking**.
* Lebih kuat dibanding `millis()`, tapi juga lebih rumit (perlu paham register).
* Sangat berguna untuk sistem real-time yang butuh **jadwal periodik stabil**.

---
