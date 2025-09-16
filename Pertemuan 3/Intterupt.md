# 📘 Interrupt pada Arduino

## 1. Pendahuluan

Pada mikrokontroler, **interrupt** adalah mekanisme yang memungkinkan perangkat keras “menyela” program utama ketika ada kejadian penting.
Alih-alih terus-menerus mengecek kondisi (polling), kita bisa menggunakan interrupt agar mikrokontroler **langsung bereaksi** terhadap event.

Contoh umum:

* Tombol ditekan
* Sinyal sensor masuk
* Pulsa dari encoder
* Data datang dari komunikasi serial

---

## 2. Konsep Dasar (Bahasa Gampang)

Bayangkan Arduino itu seperti anak yang lagi **ngerjain PR**.
Tiba-tiba mamanya panggil: “Nak, bukain pintu!”.
👉 Dia langsung **berhenti sebentar**, buka pintu, lalu **balik lagi ngerjain PR**.

* **Program utama (`loop`)** = ngerjain PR
* **Interrupt** = panggilan mendadak (misalnya tombol ditekan)
* **ISR (Interrupt Service Routine)** = aksi cepat untuk merespons (misalnya nyalakan lampu)

---

## 3. Fungsi Penting: `attachInterrupt()`

```cpp
attachInterrupt(digitalPinToInterrupt(pin), ISR, mode);
```

* **pin** → pin Arduino yang bisa dipakai interrupt (misal pin 2, 3 di Arduino UNO).
* **ISR** → nama fungsi yang dipanggil ketika interrupt terjadi.
* **mode** → kapan interrupt dipicu:

  * `RISING` → saat sinyal naik dari LOW → HIGH
  * `FALLING` → saat sinyal turun dari HIGH → LOW
  * `CHANGE` → setiap kali ada perubahan (naik atau turun)
  * `LOW` → selama sinyal LOW

---

## 4. Contoh Program 1: Toggle LED dengan Tombol

```cpp
const int LED = 13;
const int BUTTON = 2;
volatile bool ledState = LOW;

void setup() {
  pinMode(LED, OUTPUT);
  pinMode(BUTTON, INPUT_PULLUP); // tombol ke GND
  attachInterrupt(digitalPinToInterrupt(BUTTON), toggleLED, FALLING);
}

void loop() {
  digitalWrite(LED, ledState);
}

// ISR: fungsi yang dipanggil saat tombol ditekan
void toggleLED() {
  ledState = !ledState;
}
```

📌 Penjelasan:

* Tombol dihubungkan ke pin 2 dengan `INPUT_PULLUP`.
* Saat ditekan, sinyal jatuh dari HIGH → LOW (**FALLING**).
* Arduino langsung loncat ke fungsi `toggleLED()` untuk ubah status LED.

---

## 5. Contoh Program 2: Hitung Jumlah Tekanan Tombol

```cpp
volatile int counter = 0;  

void setup() {
  Serial.begin(9600);
  pinMode(2, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(2), buttonPressed, FALLING);
}

void loop() {
  Serial.print("Tombol ditekan: ");
  Serial.println(counter);
  delay(1000);
}

void buttonPressed() {
  counter++;
}
```

📌 Penjelasan:

* Variabel `counter` akan bertambah tiap kali tombol ditekan.
* ISR (`buttonPressed`) hanya berisi logika singkat (menambah angka).
* Nilai counter dicetak tiap detik di `loop()`.

---

## 6. Aturan Penting Saat Pakai Interrupt

* **Jangan lama-lama di ISR** → hanya lakukan hal cepat (ubah variabel, set flag).
* **Gunakan `volatile`** untuk variabel yang diakses ISR + `loop()`.
* **Hati-hati dengan fungsi berat** seperti `Serial.print()` di ISR → bisa bikin error.
* **Debounce tombol**: kadang tombol bergetar → terbaca lebih dari sekali. Bisa diatasi dengan software debounce atau delay kecil di ISR.

---

## 7. Aplikasi Nyata Interrupt

* Tombol darurat (emergency stop).
* Hitung pulsa dari sensor (encoder roda, sensor flow).
* Bangunkan mikrokontroler dari mode hemat energi.
* Menangani komunikasi real-time (UART, I2C, SPI).

---

## 8. Kesimpulan

* **Interrupt** = cara Arduino bereaksi cepat terhadap kejadian mendadak.
* Lebih efisien daripada polling (cek tombol terus-menerus).
* Harus digunakan dengan hati-hati agar tidak mengganggu program utama.

---

👉 Dengan contoh di atas, siswa bisa langsung praktek:

* **Contoh 1**: LED toggle pakai tombol
* **Contoh 2**: Counter jumlah tekan

---
