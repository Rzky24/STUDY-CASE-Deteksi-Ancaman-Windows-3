# Deteksi-Ancaman-Windows-3
Pelajari bagaimana pelaku ancaman berhasil mempertahankan akses ke host Windows yang telah diretas.

Perkenalan
Bagaimana jika penyerang tidak hanya puas dengan membobol host tetapi juga bertujuan untuk tetap berada di sana, membangun kendali, dan menggunakan sistem sebagai titik awal untuk operasi di masa mendatang? Ruangan ini melengkapi perjalanan deteksi ancaman Windows Anda dan mengeksplorasi bagaimana host yang disusupi dapat menjadi bagian dari serangan yang lebih besar, dengan fokus pada tiga taktik: Perintah dan Kontrol, Keberlangsungan , dan Dampak.

Tujuan pembelajaran
Ingatkan kembali konsep Komando dan Kontrol ( C2 ).
Pelajari mengapa dan bagaimana pelaku ancaman mempertahankan kendali atas korban mereka.
Gunakan log peristiwa Windows untuk mengungkap berbagai metode persistensi .
Lihat bagaimana teknik yang dipelajari bekerja dalam lingkungan praktik langsung.

# Command and Control
Komando dan Kontrol
Anda sudah mempelajari bahwa worm USB dan lampiran phishing dapat "menginfeksi" mesin. Tetapi bagaimana tepatnya mereka melakukannya? Bagaimana pelaku ancaman mengirimkan perintah dan tetap mengendalikan host korban? Tugas ini akan menjelaskan topik ini dan mengeksplorasi taktik Command and Control ( C2 ) MITRE .

Serangan Tanpa C2
Dalam beberapa kasus, C2 sama sekali tidak diperlukan. Misalnya, pelaku ancaman dapat mengetikkan perintah mereka langsung di sesi RDP setelah terjadi pelanggaran RDP . Karena metode ini menjadi tidak tersedia segera setelah RDP ditutup atau diamankan, sebagian besar pelaku ancaman memilih untuk tetap menyiapkan C2 segera setelah pelanggaran terjadi.

<img width="1876" height="723" alt="image" src="https://github.com/user-attachments/assets/91d1d78f-2a32-4653-8a54-9f41416b98ba" />
C2 paling sederhana
Untuk metode Akses Awal lainnya, pelaku ancaman tidak bisa begitu saja menggunakan RDP setiap kali mereka perlu menjalankan perintah, jadi mereka membutuhkan suatu proses yang terhubung kembali ke penyerang dan menunggu perintah mereka 24/7. Dalam kasus paling sederhana, lampiran phishing akan menjadi proses tersebut dan membangun  saluran Perintah dan Kontrol , seperti pada tangkapan layar CobaltStrike C2 di bawah ini.

Dalam kasus yang lebih canggih, lampiran tersebut tidak akan langsung terhubung kembali, melainkan mengunduh malware C2 tambahan , menyembunyikannya di folder seperti C:\Temp, dan menjalankannya sebagai proses tersembunyi yang baru. Metode ini bermanfaat untuk menjaga serangan tetap berjalan jika korban memutuskan untuk menghapus lampiran asli. Lihat bagaimana cara kerjanya https://umbrella.cisco.com/blog/cybersecurity-threat-spotlight-ransomware-trojans-loaders dan selama kampanye phishing APT29 .https://cloud.google.com/blog/topics/threat-intelligence/tracking-apt29-phishing-campaigns#:~:text=Mandiant%20also%20identified%20APT29%20utilizing

<img width="1876" height="724" alt="image" src="https://github.com/user-attachments/assets/aba0fdbe-91d2-4ce5-87b4-85d820a87034" />

Untuk tugas ini, akses VM yang terlampir dan deteksi pengaturan C2 menggunakan log Sysmon :
C:\Users\Administrator\Desktop\Practice\Task 2\ Sysmon . evtx

Jawablah pertanyaan-pertanyaan di bawah ini.
Arsip mencurigakan mana yang diunduh pengguna?

URGENT!.zip

Jawaban yang Benar
Di mana penyerang menyembunyikan file malware C2?

C:\Users\Administrator\AppData\Roaming\update.exe

Jawaban yang Benar
Apa domain dari server Command and Control?

tetap di folder yang sama - kita cari dns id 22 yaitu - dns query - lihat detail 
maka akan muncul seperti contoh di bawah ini :
<img width="1349" height="671" alt="image" src="https://github.com/user-attachments/assets/2f126cd5-4169-4d7f-9bf1-0e0eaa5fc7a2" />

jawaban : route.m365officesync.workers.dev

Jawaban yang Benar

# Persistence Overview
Gambaran Umum Ketahanan
Infeksi pencuri data biasanya memiliki masa hidup yang sangat singkat: mereka membobol korban, mengumpulkan data, mengekstraknya, dan keluar - semuanya dalam hitungan menit. Namun, untuk sebagian besar serangan lainnya, mempertahankan akses ke korban selama berhari-hari atau bahkan berbulan-bulan setelah Akses Awal sangat penting. Taktik mempertahankan akses yang andal dan jangka panjang ke target yang dapat bertahan dari reboot dan perubahan kata sandi disebut Persistensi - topik besar dan menarik yang akan segera Anda temukan.

<img width="971" height="285" alt="image" src="https://github.com/user-attachments/assets/d0ab5603-c4f7-4643-a998-d7e45deb9249" />

Mempertahankan koneksi melalui RDP
Banyak pelanggaran keamanan Windows terjadi karena layanan yang terekspos: RDP dengan kata sandi yang lemah, server email yang rentan, atau aplikasi web yang salah konfigurasi. Untuk skenario seperti itu, pelaku ancaman dapat mengakses mesin melalui layanan yang terekspos yang sama berulang kali hingga kerentanan tersebut diperbaiki. Namun, pelaku ancaman sering kali menggunakan metode Persistensi tambahan , misalnya:

Buat kerentanan tersembunyi tambahan pada layanan yang diretas (misalnya, backdoor atau  web shell ).
Buat pengguna baru ( T1136 ), jadikan administrator ( T1098 ), dan gunakan untuk login RDP selanjutnya.
Sekarang mari kita fokus pada metode kedua dan lihat bagaimana Anda atau pelaku ancaman dapat mengelola pengguna di Windows. Opsi pertama adalah menggunakan utilitas grafis dengan mencari "Manajemen Komputer" atau dengan menjalankan  lusrmgr.msc. Opsi kedua adalah menggunakan baris perintah, seperti pada contoh di bawah ini:

Perintah CMD dan PowerShell untuk Mengelola Pengguna
# 1. Two methods to create the "mr.backd00r" user
CMD C:\> net user "mr.backd00r" "p@ssw0rd!" /add
PS  C:\> New-LocalUser "mr.backd00r" -Password [...]

# 2. Two methods to add the user to Administrators 
CMD C:\> net localgroup Administrators "mr.backd00r" /add
PS  C:\> Add-LocalGroupMember "Administrators" -Member "mr.backd00r"
Mendeteksi Pengguna yang Menggunakan Pintu Belakang
Saatnya kembali ke log peristiwa keamanan! Setiap peristiwa pembuatan pengguna dicatat sebagai ID peristiwa keamanan 4720,  yang telah Anda pelajari di ruang Pencatatan Windows untuk SOC . Karena pelaku ancaman dapat sangat kreatif dalam menamai akun yang disusupi, Anda tidak boleh hanya mengandalkan deteksi nama mencurigakan seperti "hacker" tetapi juga menyelidiki:

Siapa yang membuat akun tersebut? Dapatkah orang tersebut mengkonfirmasi pembuatan akun?
Apa alamat IP sumber dan waktu login pembuatnya? Apakah ini sesuai harapan?
Peristiwa mencurigakan apa lagi yang dapat Anda lihat di sesi pembuatnya?
Memberikan Hak Akses Istimewa kepada Pengguna

Selanjutnya, pengguna baru saja tidak akan memberikan banyak keuntungan bagi penyerang, karena izin pengguna default tidak mengizinkan login jarak jauh ( RDP ) atau memberikan hak istimewa administratif pada mesin tersebut. Untuk mengatasi hal ini, pelaku ancaman akan menambahkan akun yang telah disusupi backdoor ke salah satu grup istimewa, yang dilacak oleh ID peristiwa keamanan 4732. Grup yang paling sering dieksploitasi adalah Administrator dan Pengguna Desktop Jarak Jauh .

Mengatur Ulang Kata Sandi

Terakhir, dalam kasus yang lebih canggih, pelaku ancaman mungkin hanya mengatur ulang kata sandi beberapa akun lama atau yang tidak digunakan dan menggunakannya alih-alih membuat akun baru. Anda dapat mendeteksinya dengan ID peristiwa keamanan 4724. Singkatnya, di bawah ini Anda dapat melihat bagaimana tampilan ID peristiwa yang dijelaskan :

<img width="1313" height="630" alt="image" src="https://github.com/user-attachments/assets/c078d738-930a-4196-8276-d4ba9b86fb24" />

Untuk tugas ini, coba deteksi persistensi melalui akun pengguna yang disusupi:
C:\Users\Administrator\Desktop\Practice\Task 3\ Security.evtx

Jawablah pertanyaan-pertanyaan di bawah ini.
Berapa kali pelaku ancaman gagal masuk ke akun Administrator?

caranya kita masuk dulu di eventviewer di task 3  - di folder save log - security - amati bagian id 4738 -berapakali user account management muncul- di sini pelaku ancaman mengakses beberapa login yang mengarah ke target user name administrator 

jawaban : 6

Jawaban yang Benar

Setelah berhasil masuk, pengguna backdoor mana yang dibuat oleh penyerang?
lihat di detail user account management yaitu event id 4738 - amati di bagian detail - target user name - suport

kurang lebih seperti ini isi nya  :
EventData 

  Dummy - 
  TargetUserName support 



jawaban : support

Jawaban yang Benar

Ke grup istimewa mana pengguna backdoor ditambahkan?

lihat di task category - security group management - amati bagian general 
<img width="1306" height="486" alt="image" src="https://github.com/user-attachments/assets/5ba69982-ae27-41d5-894c-5e4cd0dc6fae" />



jawaban : Administrators

Jawaban yang Benar

# Ketahanan Malware
Ketahanan Malware
Persistensi melalui pengguna yang disusupi berfungsi dengan baik jika Anda dapat masuk ke pengguna tersebut dari jarak jauh melalui RDP , tetapi jika serangan dimulai melalui serangan phishing atau infeksi USB, itu bukan pilihan. Untuk skenario ini, pelaku ancaman membutuhkan malware yang aktif berjalan dan mempertahankan koneksi dengan server C2 mereka bahkan setelah sistem di-restart. Bagaimana mereka dapat mencapai persistensi malware ?

Layanan dan Tugas
Sayangnya bagi para pembela, ada ratusan atau lebih metode untuk melakukan serangan persisten pada mesin Windows. Sebagai analis SOC L1 atau L2, Anda tidak perlu mengetahui semuanya, tetapi mari kita mulai dengan dua metode yang paling umum:

Metode Ketekunan	Contoh Serangan	Pencatatan ID Peristiwa
Buat Layanan Windows
(Berjalan setelah sistem operasi dimulai)	sc create "BadService" binpath= "C:\malware.exe" start= auto	
Peluncuran sc.exe: Sysmon / 1
Pembuatan layanan: Security / 4697

Buat Tugas Terjadwal
(Jalankan setelah sistem operasi dimulai)	schtasks /create /tn "BadTask" /tr "C:\malware.exe" /sc onstart /ru System	Peluncuran schtasks.exe: Sysmon / 1
Pembuatan tugas terjadwal: Security / 4698
Mendeteksi Layanan
Banyak komponen penting Windows seperti klien DNS atau Pusat Keamanan adalah layanan. Anda dapat melihat layanan dengan menjalankan services.msc atau mencari "Layanan", tetapi Anda memerlukan hak administratif dan perintah sc.exe untuk membuat atau memodifikasi layanan.

Pelaku ancaman dapat membuat layanan berbahaya mereka sendiri yang akan menjalankan program yang ditentukan saat startup, dan mereka sering melakukannya, seperti yang dapat Anda baca dalam contoh MITRE . Dalam log, Anda dapat mendeteksi layanan berbahaya dengan tiga cara:

Mendeteksi peluncuran perintah sc.exe createmelalui ID peristiwa Sysmon  1
Deteksi pembuatan layanan melalui ID peristiwa keamanan 4697  atau ID peristiwa sistem 7045.
Mendeteksi proses mencurigakan dengan  services.exeproses induk.

<img width="1175" height="370" alt="image" src="https://github.com/user-attachments/assets/d5db2795-1104-41f4-bd1d-0a7af161fea2" />

Mendeteksi Tugas
Tugas terjadwal adalah fitur Windows lain yang banyak digunakan baik oleh sistem operasi maupun aplikasi eksternal (misalnya untuk memeriksa pembaruan atau membuat cadangan setiap jam). Dari GUI, Anda dapat mengelola tugas dengan menjalankan  taskschd.msc atau mencari "Task Scheduler". Dari baris perintah, Anda dapat menggunakan  perintah schtasks.exe .

Tidak seperti layanan, tugas terjadwal sangat mudah dikonfigurasi dan disembunyikan, itulah sebabnya tugas terjadwal menjadi metode persistensi yang paling umum digunakan oleh pelaku ancaman, seperti pada serangan APT28  dan APT41 ini . Mirip dengan layanan, Anda dapat mendeteksi tugas terjadwal dengan tiga cara:

Mendeteksi peluncuran perintah schtasks.exe /createmelalui ID peristiwa Sysmon  1
Mendeteksi dan menganalisis peristiwa pembuatan tugas terjadwal melalui ID peristiwa keamanan 4698.
Mendeteksi proses mencurigakan dengan  svchost.exe [...] -s Scheduleinduknya

<img width="1175" height="370" alt="image" src="https://github.com/user-attachments/assets/4727dce2-d52d-4c95-b4af-8933d402fa56" />

Penyerang meninggalkan dua pintu belakang dan memulai ulang sistem.
Bisakah Anda mengungkap semuanya menggunakan log Keamanan dan Sysmon ?
C:\Users\Administrator\Desktop\Practice\Task 4\

Jawablah pertanyaan-pertanyaan di bawah ini.
Layanan Windows mana yang dibuat untuk mempertahankan malware Nessie?
lihat di folder security all time - event viewer id 4697 - ada dua point yang harus di perhatikan - 

ServiceName Data Protection Service 
  ServiceFileName C:\Windows\Help\nessie.exe 

<img width="1366" height="380" alt="image" src="https://github.com/user-attachments/assets/e0e789da-90b0-4373-800c-364d681f58f7" />


jawaban : Data Protection Service

Jawaban yang Benar
Tugas terjadwal mana yang dibuat untuk mempertahankan malware Troy?
di folder event viewer - security alltime - lihat di genaral task information - task name  - other object access events 4698
jawaban : AmazonSync

Jawaban yang Benar
Bendera apa yang Anda dapatkan setelah menemukan dan menjalankan malware Troy?
CARA :BUKA SYSMON FOLDER -CARI Information	7/4/2025 10:14:07 PM	Sysmon	1	Process Create (rule: ProcessCreate) 
LIHAT DETAIL : DAN CARI                           
ParentCommandLine C:\Windows\system32\svchost.exe -k netsvcs -p -s Schedule 
BUKA TROY.EXE DI :  C , PROGRAM FILES , COMMON FILES , CARI TROY.EXE ,DAN PASTEKAN ParentCommandLine C:\Windows\system32\svchost.exe -k netsvcs -p -s Schedule                             <img width="1332" height="501" alt="image" src="https://github.com/user-attachments/assets/e355a341-f84c-45ac-979e-3dbe4562ae2c" />
                  

JAWABAN : THM{c2_is_on_schedule!}

Jawaban yang Benar

Persistence: Run Keys and Startup
Menjalankan Tombol dan Memulai
Layanan dan tugas terjadwal biasanya dijalankan saat sistem booting dan memerlukan hak akses administrator untuk dikonfigurasi. Namun, bagaimana jika sebuah program hanya perlu dijalankan saat pengguna tertentu masuk? Untuk kasus seperti itu, Windows menyediakan beberapa metode persistensi per pengguna yang aktif digunakan baik oleh alat yang sah maupun malware:

Metode Ketekunan	Contoh Serangan	Pencatatan ID Peristiwa
Tambahkan malware ke Folder Startup
(Berjalan saat pengguna masuk)	copy C:\malware.exe
"%AppData%\Microsoft\Windows\Start Menu\Programs\Startup\malware.exe"	Item startup baru: ID Peristiwa Sysmon  11
Tambahkan malware ke tombol "Jalankan"
(Berjalan saat pengguna masuk)	reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"
/v BadKey /t REG_SZ /d "C:\malware.exe"	Nilai registri baru: ID Peristiwa  Sysmon 13
Mendeteksi Startup
Folder startup dirancang sebagai cara mudah bagi pengguna yang kurang berpengalaman untuk mengkonfigurasi program agar berjalan saat login. Anda cukup membuka folder startup, memindahkan program atau pintasan program Anda ke sana, dan melihat bagaimana program tersebut secara otomatis berjalan saat Anda login di masa mendatang. Anda dapat mengakses folder startup Anda melalui jalur di bawah ini:

C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
Or for all users: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
Folder startup bukanlah pilihan umum untuk program yang sah, jadi biasanya folder tersebut kosong. Namun, pelaku ancaman sering kali menempatkan malware mereka di sana ( contohnya Lumma Stealer ), dan Anda dapat mendeteksinya dengan memantau peristiwa pembuatan file (ID Peristiwa Sysmon 11 ) di dalam Folder Startup. Selain itu, perlu diperhatikan bahwa program yang diluncurkan melalui startup akan memiliki induk explorer.exe, sehingga mungkin sulit untuk membedakannya dari aktivitas pengguna yang sah atau serangan yang telah Anda pelajari di Deteksi Ancaman Windows 1 :

<img width="1175" height="370" alt="image" src="https://github.com/user-attachments/assets/ee5bc273-3d44-4f1b-a30d-f9ad3ffc15ef" />

Mendeteksi Tombol Run
Keberlangsungan kunci Run sangat mirip dengan folder startup; keduanya bahkan menggunakan teknik MITRE yang sama ! Satu-satunya perbedaan utama adalah cara entri ditambahkan di sana. Alih-alih hanya menyalin program ke folder startup, Anda perlu membuat nilai baru di registri Windows "Run" dan memasukkan jalur ke program Anda di sana:

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
Or for all users: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
Untuk melihat entri "Run", Anda dapat meluncurkan regedit.exeatau mencari "Registry Editor" dan masuk ke jalur yang ditunjukkan di atas. Untuk mendeteksi entri berbahaya dari log, Anda dapat memantau peristiwa perubahan registri ( Sysmon Event ID 13 ) yang memengaruhi kunci Run:
<img width="1175" height="370" alt="image" src="https://github.com/user-attachments/assets/589f2a3d-e1de-4c59-b203-f561f3203e85" />

Gunakan semua alat yang Anda ketahui untuk mengungkap celah keamanan yang baru Anda pelajari!
C:\Users\Administrator\Desktop\Practice\Task 5\

Jawablah pertanyaan-pertanyaan di bawah ini.
Apa citra proses induk dari malware "Odin"?

C:\Windows\explorer.exe

Jawaban yang Benar
Apa baris terakhir yang dikeluarkan oleh malware "Odin"?

Done doing bad stuff!

Jawaban yang Benar
Bendera apa yang Anda dapatkan setelah menemukan dan menjalankan malware "Kitten"?

THM{persisting_in_basket!}

Jawaban yang Benar

# Impact and Threat Detection Recap
Kebutuhan akan Ketekunan
Pada tugas-tugas sebelumnya, Anda telah mempelajari bagaimana pelaku ancaman dapat tetap aktif di sistem. Tetapi mengapa mereka membutuhkannya? Mengapa tidak hanya mencuri data dan keluar dari sistem sebelum terdeteksi? Ada beberapa alasan, tetapi yang utama adalah:

Tambahkan host ke botnet dan gunakan untuk serangan lebih lanjut.
Sama seperti bagaimana Botnet Kraken  menggabungkan kemampuan penambang kripto, pencuri data, dan C2.
Memata-matai korban sebagai bagian dari kampanye yang disponsori negara.
Sama seperti Volt Typhoon yang tidak terdeteksi di jaringan listrik AS selama hampir setahun.
Gunakan korban sebagai titik masuk ke jaringan, yang pembobolannya bisa memakan waktu berbulan-bulan.
Seperti dalam kasus di mana pelaku ancaman menghabiskan 29 hari untuk membobol seluruh jaringan.
Active Directory dan Ransomware
Mari kita perhatikan lebih dekat poin ketiga. Dalam kebanyakan kasus, jaringan Windows berarti Active Directory yang besar yang membawa serangan, deteksi, dan ancaman tersendiri - yang utama adalah ransomware . Ransomware paling menakutkan bagi bisnis. Mengapa? Karena dapat menghentikan seluruh perusahaan, seperti yang terjadi pada rumah sakit McLaren, yang memengaruhi 743.000 pasien . Bayangkan saja server Anda dienkripsi, data dicuri, dan catatan tebusan dicetak secara otomatis di semua printer kantor:

<img width="1258" height="605" alt="image" src="https://github.com/user-attachments/assets/359e96e0-ea5c-4334-aa36-b7e204ad5476" />


Rekap Deteksi Ancaman
Active Directory dan ransomware adalah topik yang kompleks, tetapi semua serangan kompleks dimulai dari satu pelanggaran sederhana. Di ruang Deteksi Ancaman Windows, Anda telah mempelajari bagaimana pelanggaran dimulai, bagaimana penyerang mencuri data, dan bagaimana mereka tetap tidak terdeteksi selama bertahun-tahun. Sekarang Anda siap menggunakan pengetahuan yang diperoleh untuk mendeteksi dan menghentikan serangan sebelum ransomware menyebabkan  dampak yang mengerikan , sebaiknya segera setelah Akses Awal. Berikut ringkasan singkat dari apa yang telah Anda pelajari sejauh ini (ditandai dengan warna kuning):

<img width="1851" height="626" alt="image" src="https://github.com/user-attachments/assets/6a8db410-176d-43c2-8f8c-5de7e40f9ea9" />

Jawablah pertanyaan-pertanyaan di bawah ini.
Apa ancaman terbesar bagi sebagian besar jaringan Windows perusahaan?

Ransomware

Jawaban yang Benar
Pada tahap manakah serangan (misalnya, eksfiltrasi) paling baik dideteksi dan dihentikan?

Initial Access

Jawaban yang Benar

# Kesimpulan : 
Di ruangan ini, Anda telah mempelajari tentang Komando dan Kontrol, Ketahanan , dan Dampak - tiga taktik yang diamati selama kampanye serangan tingkat lanjut. Anda mempelajari mengapa penyerang membangun saluran C2 , bagaimana mereka mempertahankan akses jangka panjang menggunakan tugas terjadwal, folder startup, layanan, dan kunci eksekusi, serta bagaimana mendeteksi tindakan ini melalui log peristiwa Windows.
