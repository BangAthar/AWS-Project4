<!-- Project Logo -->
<br/>
<div align="center">
    <img src="https://logos-world.net/wp-content/uploads/2021/08/Amazon-Web-Services-AWS-Logo.png" alt="Logo" width="100">
    <h3 align="center">School Project - AWS Project 4</h3>
     <p align="center">
        Rangkuman dan catatan ini dikhususkan untuk tugas AWS Project 4
    </p>
    <br />
</div>

# Tentang Project
<p align="center">
    Github repositori ini merupakan catatan, rangkuman, dan dokumen pada video tutorial berjudul <a href="https://www.youtube.com/watch?v=JmUMBKs3Y0c">Konfigurasi Load Balancing, Auto Scaling , Rsync EFS Di EC2 Amazon Web Services - AWS eps-4</a> dikembangkan oleh <a href="https://github.com/OmTegar/">OmTegar</a>. Repositori ini dikhususkan untuk tugas sekolah AWS-Project 4. Kalian bisa simpan catatan ini untuk dijadikan referensi dan perkembangan ilmu di dunia Cloud Computing (AWS).
</p>

# Topologi Project
<div align="center">
<img src="images/topologi.png" alt="Logo" width="500">
</div>

Ini merupakan contoh topologi yang akan kita gunakan untuk praktek di dalam AWS Management Console.
Berikut beberapa service AWS yang kita butuhkan untuk praktek :

- **VPC**
- **AWS EC2**
- **EFS**
- **RDS**
- **AWS Auto Scaling**
- **Auto Balancing**

# Membuat VPC

Silahkan buka service VPC lalu pilih `Create VPC`. Setelah itu silahkan buat VPC dengan contoh konfigurasi seperti dibawah ini :

- **Name**          : Project4
- **IP CIDR**       : 192.168.1.0/24
- **AZs**           : 2 (US-1a and US-1b)
- **Subnets**       : 2 (2 public and 2 private) 
- **NAT Gateways**  : 1 per AZ
- **VPC endpoints** : None
<div align="center">
<img src="images/vpc.png" alt="Logo">
</div>

# Membuat Database

Silahkan buka service RDS lalu pilih `Create database`. Setelah itu silahkan buat database dengan contoh konfigurasi seperti dibawah ini
- **Engine**  : MySQL, Memilih MySQL karena lebih mudah digunakan dan umum
- **Templates**  : Free Tier, Menggunakan Free Tier karena database ini hanya digunakan praktek dan eksperimen
- **DB Instance Name** : db-project4, Nama database servernya bebas
- **Master Username**  : admin, Nama username bebas
- **Master Password**  : 12345678, Password username bebas
- **DB Instance Class**  : db.t3.micro, Menggunakan type instance t3.micro
- **Storage Type**  : General Purpose SSD (gp2), Berikut perbedaan jenis storage gp2 dan gp3
<img src="images/jenis-storage.png" alt="Logo">

- **Allocate Storage** : 20GiB, Menggunakan storage SSD dengan kapasitas sekitar 20GB
- **Storage Autoscaling** : Enable, Otomatis menambah kapasitas storage ketika penuh hingga mencapai batas autoscaling yaitu sekitar 1000GB / 1TB
- **Compute Resource** : Don't connect to an EC2 compute storage, Memilih untuk tidak mengsetup database ke EC2 secara langsung
- **Network Type** : IPv4, Hanya menggunakan IP dengan versi 4
- **VPC** : project4-vpc, Pilih VPC yang berusan kita buat tadi
- **DB Subnet Group** : Create New, Membuat subnet group baru
- **Public Access** : No, Karena RDS bertepatan pada subnet private AZ US-1a sesuai dengan gambar topologi diatas
- **VPC Security Group** : Create New, Membuat security group baru untuk konektivitas
- **Security Group Name** : RDS-sg, Untuk nama terserah kalian
- **Availibity Zone** : us-east-1a, Memilih us-1a karena subnet tersebut merupakan subnet private dari AZ us-east-1a sesuai dengan gambar topologi 

<h3>Additional Configuration RDS</h3>

Karena pembuatan database ini hanya digunakan untuk praktek atau eksperimen, kita perlu mematikan beberapa fitur dari _additional configuration rds_ yaitu
- **auto backup** : disable, mematikan sistem auto backup
- **encryption** : disable, mematikan sistem enkripsi pada database
- **auto minor** : disable, mematikan sistem maintenance yaitu minor version upgrade

# Membuat Instance EC2 dan autoscaling

<img src="images/templet.png" align="right" 
alt="Size Limit logo by Anton Lovchikov">
Silahkan buka service EC2, jika halaman dashboard sudah terbuka dibagian sidebar atau panel sebelah kiri terdapat menu `Launch Templates`, klik menu tersebut dan kita akan diarahkan untuk membuat EC2 launch template dan klik `Create launch template`.