# Tutorial 7 - Profiling

<details>
<summary><b>1. Performance Testing Results</b></summary>

## 1. Performance Testing Results

### Test Plan 1: `/all-student`
View Result In Table
<img width="1600" height="626" alt="test_plan_1_1" src="https://github.com/user-attachments/assets/3e2fdc50-ee9b-452f-ab8d-3956eb04d364" />

Summary Report
<img width="1600" height="396" alt="test_plan_1_2" src="https://github.com/user-attachments/assets/ed175c27-f566-4eb7-9d9e-edf4e5214dc2" />

Graph Result
<img width="1600" height="951" alt="test_plan_1_3" src="https://github.com/user-attachments/assets/65701f60-b1d3-4b03-bfab-69fce453ff5b" />

View Result Tree
<img width="1600" height="948" alt="test_plan_1_4" src="https://github.com/user-attachments/assets/05dcb55c-4550-4c6d-8332-b53d973cd33c" />

CLI Test
<img width="1633" height="233" alt="image" src="https://github.com/user-attachments/assets/293320df-b6ba-465d-8fb9-211e93aa0bb8" />


### Test Plan 2: `/all-student-name`
View Result In Table
<img width="1600" height="947" alt="test_plan_2_1" src="https://github.com/user-attachments/assets/a754b9f2-71e4-4078-8548-fdc75323f80a" />

Summary Report
<img width="1600" height="945" alt="test_plan_2_2" src="https://github.com/user-attachments/assets/a3b14e7c-c2a9-481d-9cd5-ca987074a7c5" />

Graph Result
<img width="1600" height="944" alt="test_plan_2_3" src="https://github.com/user-attachments/assets/d2da9ffa-b62f-44dc-94a0-c534770f5375" />

View Result Tree
<img width="1600" height="591" alt="test_plan_2_4" src="https://github.com/user-attachments/assets/2cef95be-01ed-4dad-953e-470df3cd3b43" />

CLI Test
<img width="1630" height="244" alt="image" src="https://github.com/user-attachments/assets/b15bdb95-353c-4304-89e5-514184935c74" />

### Test Plan 3: `/highest-gpa`
View Result In Table
<img width="1600" height="948" alt="test_plan_3_1" src="https://github.com/user-attachments/assets/e688d7c5-0658-45d0-a974-deb810930bfc" />

Summary Report
<img width="1600" height="945" alt="test_plan_3_2" src="https://github.com/user-attachments/assets/f6648e4e-ede4-49a5-bf40-390b24635cde" />

Graph Result
<img width="1600" height="945" alt="test_plan_3_3" src="https://github.com/user-attachments/assets/4954c50e-2f2f-4b6c-a6dd-58225bc81d9b" />

View Result Tree
<img width="1600" height="951" alt="test_plan_3_4" src="https://github.com/user-attachments/assets/3358c4fb-0a58-4a13-bf97-30a4cf0dd0b4" />

CLI Test
<img width="1583" height="233" alt="image" src="https://github.com/user-attachments/assets/5238401c-da6d-4d83-8b52-0dd89d9373e6" />

</details>

<details>
<summary><b>2. Profiling and Performance Optimization</b></summary>

## 2. Profiling and Performance Optimization Results

### 1. JMeter Test Results (Before vs After Optimization)

#### 1.1 `/all-student` Endpoint
<img width="3456" height="1132" alt="test_plan_1_img" src="https://github.com/user-attachments/assets/fbbd41d4-cdf6-4814-ad67-4571a1396079" />

| Metric | Before Optimization | After Optimization | Improvement |
|---|---|---|---|
| Avg Response Time | **92,552 ms** | **3,737 ms** | **95.9% faster** |
| Min Response Time | **91,834 ms** | **3,451 ms** | **96.2% faster** |
| Max Response Time | **93,041 ms** | **3,841 ms** | **95.8% faster** |

**Optimization Applied:**
Menyelesaikan masalah *N+1 Query Problem*. Sebelumnya, program melakukan *looping* untuk mengambil data mata kuliah per mahasiswa satu per satu ke database. Solusinya, menggunakan method `findAll()` milik repository untuk mengambil semua data relasi sekaligus dari database.

---

#### 1.2 `/all-student-name` Endpoint
<img width="3456" height="1054" alt="test_plan_2_img" src="https://github.com/user-attachments/assets/cba8f32e-6e93-4a61-b6d0-408b9b2aebff" />

| Metric | Before Optimization | After Optimization | Improvement |
|---|---|---|---|
| Avg Response Time | **4,198 ms** | **215 ms** | **94.8% faster** |
| Min Response Time | **3,965 ms** | **195 ms** | **95.0% faster** |
| Max Response Time | **4,425 ms** | **242 ms** | **94.5% faster** |

**Optimization Applied:**
Menghindari penggunaan operator iteratif `+=` untuk tipe data `String` di dalam *looping* yang sangat membebani memori (*immutable string creation*). Digantikan dengan implementasi Java Stream API dan `Collectors.joining(", ")` yang jauh lebih efisien dalam menggabungkan puluhan ribu teks.

---

#### 1.3 `/highest-gpa` Endpoint
<img width="3456" height="1054" alt="test_plan_3_img" src="https://github.com/user-attachments/assets/2d4fa46b-297e-4118-9c59-0e647dfe1ad5" />

| Metric | Before Optimization | After Optimization | Improvement |
|---|---|---|---|
| Avg Response Time | **243 ms** | **13 ms** | **94.6% faster** |
| Min Response Time | **224 ms** | **6 ms** | **97.3% faster** |
| Max Response Time | **264 ms** | **27 ms** | **89.7% faster** |

**Optimization Applied:**
Mencegah penarikan 20.000 data mahasiswa ke memori aplikasi (Java) hanya untuk mencari satu nilai tertinggi. Optimasi dilakukan dengan memindahkan beban kerja komputasi tersebut ke sisi database (*Database Level Filtering*) menggunakan *custom query* khusus untuk mencari satu entri dengan IPK tertinggi secara langsung.

</details>

<details>
<summary><b>3. Reflection Questions</b></summary>

## 3. Reflection

**1. What is the difference between the approach of performance testing with JMeter and profiling with IntelliJ Profiler in the context of optimizing application performance?**
JMeter melakukan pendekatan *black-box testing* dari luar aplikasi. Alat ini berfungsi untuk mensimulasikan beban ribuan pengguna nyata dan mengukur hasil akhir performanya secara kuantitatif, seperti *response time* dan *throughput*. Sebaliknya, IntelliJ Profiler melakukan pendekatan *white-box testing* dari dalam. Profiler membedah "mesin" aplikasi secara granular untuk melihat proses mana yang memakan waktu eksekusi CPU atau memori terbanyak, sehingga kita tahu persis baris kode mana yang menjadi *bottleneck*. Keduanya saling melengkapi; JMeter menumbuhkan kesadaran bahwa aplikasi kita lambat, sementara Profiler menunjukkan secara spesifik mengapa aplikasi tersebut lambat.

**2. How does the profiling process help you in identifying and understanding the weak points in your application?**
Proses *profiling* menghilangkan elemen tebak-tebakan dalam mencari celah performa. Melalui visualisasi data seperti *Flame Graph* atau *Method List*, profiler memetakan hirarki pemanggilan fungsi dan durasi spesifik masing-masing fungsi. Berkat alat ini, kelemahan aplikasi dapat diidentifikasi dengan cepat dan sangat akurat—misalnya, mendeteksi secara langsung bahwa fungsi komputasi penggabungan *string* atau masalah iterasi *N+1 query* di dalam *looping* Java adalah penyedot performa terbesar.

**3. Do you think IntelliJ Profiler is effective in assisting you to analyze and identify bottlenecks in your application code?**
Sangat efektif. IntelliJ Profiler menyajikan antarmuka visual yang intuitif terintegrasi langsung dengan IDE, sehingga transisi dari mencari masalah hingga memperbaiki kodenya terasa mulus. Profiler ini tidak hanya memberikan data kuantitatif terkait metrik komputasi CPU dan memori, tetapi juga melacak *query* SQL yang dieksekusi oleh Hibernate di belakang layar, menjadikannya alat diagnostik komprehensif untuk mendeteksi *bottleneck*. 

**4. What are the main challenges you face when conducting performance testing and profiling, and how do you overcome these challenges?**
Tantangan utamanya adalah konsistensi pengukuran. Hasil JMeter dan Profiler dapat berfluktuasi secara drastis dipengaruhi oleh *background process* sistem operasi, aktivitas memori, atau mekanisme *caching* pada database dan JVM (*Just-In-Time Compiler*). Untuk mengatasinya, penting untuk memastikan eksekusi uji coba tidak hanya dilakukan sekali. Saya melakukan proses "pemanasan" aplikasi terlebih dahulu, menjalankan pengujian JMeter melalui antarmuka *Command Line* (CLI) agar terhindar dari disrupsi RAM grafis GUI, serta berfokus meneliti metrik rata-rata waktu respons, bukan eksekusi tercepat atau terlambat secara absolut.

**5. What are the main benefits you gain from using IntelliJ Profiler for profiling your application code?**
Manfaat terbesar adalah efisiensi waktu diagnostik. Profiler memberikan kejelasan dan presisi dalam memetakan target optimasi, memisahkan *total execution time* dengan *CPU time* murni, dan menyediakan fitur perbandingan untuk memvalidasi secara pasti bahwa perubahan logika kode yang diaplikasikan memang sukses meringankan beban *resource* komputasi. 

**6. How do you handle situations where the results from profiling with IntelliJ Profiler are not entirely consistent with findings from performance testing using JMeter?**
Ketidaksesuaian hasil sering terjadi karena kedua *tool* ini mengukur variabel berbeda; Profiler murni menghitung beban operasional kode di mesin lokal, sedangkan JMeter mempertimbangkan metrik komunikasi lintas ekosistem seperti latensi *server*, responsibilitas jaringan lokal, hingga latensi pengembalian *database*. Cara menanganinya adalah dengan menyelaraskan indikator—misalnya menganalisa *throughput* pada JMeter sembari memastikan *CPU time* pada Profiler benar-benar telah berkurang secara objektif. Jika JMeter masih mengukur respons yang lambat sementara CPU Time profiler rendah, hal ini mengisyaratkan bahwa masalahnya tidak lagi ada di eksekusi kode internal, melainkan di faktor eksternal (seperti antrian *thread connection* pada database).

**7. What strategies do you implement in optimizing application code after analyzing results from performance testing and profiling? How do you ensure the changes you make do not affect the application's functionality?**
Strategi utama saya bertumpu pada pelimpahan komputasi (*offloading*). Saya memindahkan beban sortir atau filter data ke lapisan relasional *database* (seperti `ORDER BY ... LIMIT`) ketimbang menarik semua data menuju memori JVM untuk diolah. Saya juga memodifikasi kueri persisten demi membasmi inefisiensi iterasi *N+1 query*, serta memanfaatkan fitur spesifik tipe data modern seperti Java Streams API ketimbang komputasi repetitif *looping*. Untuk memastikan tidak ada fungsionalitas aplikasi yang rusak akibat perubahan fundamental ini, saya melakukan komparasi struktural respons pada hasil *endpoint* dan menulis skenario *unit test* regresi dasar. 
</details>
