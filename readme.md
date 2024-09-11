﻿# Toko Ungu

**_Disusun oleh : Jeremia Rangga Setyawan | 2306245775 | PBP B_**
**_Link PWS : http://jeremia-rangga-tokoungu.pbp.cs.ui.ac.id_**

## Tugas 2

### 1) Proses Pembuatan Projek Django

##### Check 1 : Membuat sebuah proyek Django baru

1. Buatlah sebuah direktory baru bernama `toko-ungu` di komputer
2. Kemudian, buat juga _repository_ baru di Github bernama sama yaitu `toko-ungu` dengan visibility _public_.
3. Dalam direktory baru tersebut, buka _command prompt_
4. Buatlah _virtual enviroment_ menggunakan python dengan command :
   ```bash
   python -m venv env
   ```
5. Lalu, aktifkan _virtual environement_ dengan command :
   ```bash
   env\Scripts\activate
   ```
6. _Virtual environment_ akan aktif yang ditandai (env) di awal baris input terminal
7. Buatlah file dengan nama `requirements.txt` di dalam direktori yang sama dan tambahkan beberapa _dependencies_ berikut di file tersebut :
   ```text
   django
   gunicorn
   whitenoise
   psycopg2-binary
   requests
   urllib3
   ```
8. Melakukan instalansi _dependencies_ pada `requirements.txt` dengan command :
   ```python
   pip install -r requirements.txt
   ```
9. Buatlah proyek Django baru dengan nama `toko_ungu` dengan command :
   ```bash
   django-admin startproject toko_ungu .
   ```

- Note : Pastikan `.` tertulis dalam baris command

10. Ubahlah isi variabel `ALLOWED_HOSTS` di file `settings.py` dengan menambahkan kode berikut :
    ```python
    ...
    ALLOWED_HOSTS = ["localhost", "127.0.0.1"]
    ...
    ```
11. Sebelumnya, pastikan terdapat file `manage.py` pada direktori yang aktif pada terminal kamu saat ini. Lalu, jalankan _server_ Django dengan command berikut :
    ```bash
    python manage.py runserver
    ```
12. Buka link [http://localhost:8000](http://localhost:8000/) pada browser dan pastikan muncul gambar roket yang menandakan bahwa Django berhasil diinstal. (seperti gambar berikut)
    ![Django Rocket](https://global.discourse-cdn.com/business7/uploads/djangoproject/original/3X/6/b/6b2c3c21ef8b9458b7eb6bdc843333e154b477d2.png)
13. Hentikan server dengan cara menekan `Ctrl+C` pada cmd.
14. Non aktifkan virtual environment (env) dengan command :
    ```bash
    deactivate
    ```

##### Check 2 : Membuat aplikasi dengan nama `main` pada proyek tersebut.

15. Buatlah aplikasi baru dengan nama **main** dengan menjalankan perintah berikut :
    ```bash
    python manage.py startapp main
    ```
16. Bukalan berkas `settings.py` di dalam direktori proyek `toko_ungu`.
17. Tambahkanlah `'main'` ke dalam variabel `INSTALLED_APPS` seperti contoh berikut :
    ```python
    INSTALLED_APPS = [
        ...,
        'main'
    ]
    ```

##### Check 3 : Melakukan _routing_ pada proyek agar dapat menjalankan aplikasi `main`

18. Bukalah berkas `urls.py` di dalam direktori proyek `toko_ungu`, **bukan yang ada di direktori `main`.**
19. Lakukan perubahan pada isi berkas tersebut seperti contoh kode berikut:

    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('main.urls')),
    ]
    ```

##### Check 4 : Membuat model pada aplikasi `main` dengan nama Product dan memiliki atibut wajib.

20. Di direktori `main`, bukalah berkas `models.py`
21. Isilah berkas `models.py` dengan kode berikut :

    ```python
    from django.db import models

    class Product(models.Model):
        name = models.CharField(max_length=255)
        description = models.TextField()
        price = models.IntegerField()
        quantity = models.IntegerField()

        @property
        def is_available(self):
            return self.quantity > 0
    ```

22. Lakunlah migrasi model dengan menjalankan perintah berikut.
    ```bash
    python manage.py makemigrations
    ```
23. Terapkanlah migrasi ke dalam basis data lokal dengan menjalankan perintah berikut.
    ```bash
    python manage.py migrate
    ```

- Note: Setiap ada perubahan pada `models.py`, silahkan lakukan langkah 22 dan 23 kembali.

##### Check 5 : Membuat sebuah fungsi pada `views.py` untuk dikembalikan ke dalam sebuah _template_ html yang menampilkan nama aplikasi serta nama dan kelas kamu.

24. Bukalah berkas `views.py` yang terletak pada direktori `main`.
25. Gantilah isi berkas tersebut dengan kode berikut :

    ```python
    from django.shortcuts import render

    def show_main(request):
        context = {
            'app' : 'Toko Ungu',
            'name': 'Jeremia Rangga',
            'class': 'PBP B'
        }

        return render(request, "main.html", context)
    ```

26. Buatlah direktori baru bernama `templates` dalam direktori `main`.
27. Di dalam direktori `templates`, buatlah berkas baru dengan nama `main.html`. Isi berkas `main.html` dengan kode berikut :

    ```html
    <h1>{{ app }}</h1>

    <h5>Name:</h5>
    <p>{{ name }}</p>
    <p></p>
    <h5>Class:</h5>
    <p>{{ class }}</p>
    <p></p>
    ```

##### Check 6 : Membuat sebuah _routing_ pada `urls.py` aplikasi `main` untuk memetakan fungsi yang telah dibuat pada `views.py`.

28. Buatlah berkas baru bernama `urls.py` di dalam direktori main.
29. Isi berkas tersebut dengan kode berikut :

    ```python
    from django.urls import path
    from main.views import show_main

    app_name = 'main'

    urlpatterns = [
        path('', show_main, name='show_main'),
    ]
    ```

###### RUNNING DAN TESTING PROYEK

30. Jalankan proyek Django dengan perintah berikut.
    ```bash
    python manage.py runserver
    ```
31. Bukalah laman http://localhost:8000/ dan lihat perubahannya.
32. Di direktori `main`, bukalah berkas `tests.py`.
33. Isi berkas tersebut dengan kode berikut :

    ```python
    from django.test import TestCase, Client
    from .models import Product

    class mainTest(TestCase):
        def test_main_url_is_exist(self):
            response = Client().get('')
            self.assertEqual(response.status_code, 200)

        def test_main_using_main_template(self):
            response = Client().get('')
            self.assertTemplateUsed(response, 'main.html')

        def test_nonexistent_page(self):
            response = Client().get('/skibidi/')
            self.assertEqual(response.status_code, 404)

        def test_available(self):
            product = Product.objects.create(
              name="Baju",
              description="Warna putih",
              price = 100000,
              quantity = 10,
            )
            self.assertTrue(product.is_available)
    ```

34. Jalankanlah berkas tes menggunakan perintah berikut :
    ```bash
    python manage.py test
    ```
35. Jika tes berhasil dan aman, maka akan muncul informasi berikut :

    ```bash
    Found 4 test(s).
    Creating test database for alias 'default'...
    System check identified no issues (0 silenced).
    ..
    ----------------------------------------------------------------------
    Ran 4 tests in 0.016s

    OK
    Destroying test database for alias 'default'...
    ```

##### Check 7: Membuat _deployment_ ke PWS terhadap aplikasi yang sudah dibuat sehingga nantinya dapat diakses oleh teman-teman.

###### ADD, COMMIT, PUSH GITHUB :

36. Lakukan inisiasi direktori lokal `toko-ungu` sebagai repositori git dengan menjalankan perintah berikut pada terminal :
    ```bash
    git init
    ```
37. Kemudian, tandai semua _file_ yang berada pada direktori tersebut sebagai _file_ yang akan di-_commit (tracked)_ dengan perintah berikut :
    ```bash
    git add .
    ```
38. Lanjutkan membuat pesan _commit_ yang sesuai dengan perubahan atau pembaharuan dengan perintah berikut :
    ```bash
    git commit -m "<PESAN KAMU>"
    ```
39. Pastikan saat ini kamu berada pada branch _main_ dengan menjalankan perntah berikut :
    ```bash
    git branch -M main
    ```
40. Hubungkan repositori lokal (direktori saat ini) dengan repositori di Github kamu dengan perintah berikut :
    ```bash
    git remote add origin <URL_REPO>
    ```

- Note : ubah `<URL_REPO>` dengan url github yang baru kamu buat.

41. Kemudian, push seluruh berkas ke repositori github dengan perintah berikut :
    ```bash
    git push -u origin main
    ```

###### PUSH PWS :

42. Buka halaman PWS pada https://pbp.cs.ui.ac.id/ , kemudian buatlah akun atau _Register_ menggunakan akun SSO kamu.
43. Lakukan _login_ menggunakan akun yang baru saja kamu buat.
44. Buatlah proyek baru dengan menekan tombol `Create New Project`. Kamu akan berpindah ke halaman untuk membuat proyek baru. Silahkan isi `Project Name` dengan tokoungu. Setelah itu, tekan tombol `Create New Project` yang berwarna biru.
45. Simpan informasi _Project Credentials_ di tempat yang dan pastikan tidak hilang karena akan digunakan di langkah selanjutnya. **Jangan jalankan dulu instruksi _Project Command_.**
46. Lakukanlah update pada berkas `settings.py` di proyek Django kamu, tambahkan URL _deployment PWS_ pada variabel `ALLOWED_HOSTS` seperti kode berikut :
    ```python
    ...
    ALLOWED_HOSTS = ["localhost", "127.0.0.1", <NAMA DEPAN>-<NAMA TENGAH>-tokoungu.pbp.cs.ui.ac.id]
    ...
    ```

- Note : ganti `<NAMA DEPAN>` dan `<NAMA TENGAH>` sesuai akun SSO kamu. Misal : jeremia-rangga-tokoungu.pbp.cs.ui.ac.id .

47. Jalankan perintah berikut untuk melakukan push repositori lokal ke PWS kamu (jalankan satu per satu tiap baris) :

    ```bash
    git remote add pws http://pbp.cs.ui.ac.id/<USERNAME PWS>/tokoungu

    git branch -M master

    git push pws master
    ```

48. Setelah menjalankan perintah tersebut, kamu akan diminta `username` dan `password`. Gunakan _Project Credentials_ yang telah kamu simpan (ingat kembali langkah 45).
49. Setelah menjalankan perintah tersebut, silahkan kembalikan branch ke main dengan perintah berikut :
    ```bash
    git branch -M main
    ```
50. Cari proyek kamu di laman PWS, kemudian cek statusnya. Jika status `Building` maka tunggu beberapa saat hingga status berubah menjadi `Running`.
51. Jika sudah `Running`, silahkan tekan tombol `View Project` lalu copy linknya dan buka di aplikasi Google Chrome. Pastikan https:// diganti dengan http:// pada link tersebut.

### 2) Bagan _request client_ ke web pada Django

![Bagan request & response Django](https://media.discordapp.net/attachments/1144094248665370624/1282015001766395945/BAGAN_PBP.png?ex=66ddd103&is=66dc7f83&hm=c296e3f9ea94c7b1084bde785a88609e3748a6059ecaa520a224952b8eb231e0&=&format=webp&quality=lossless&width=825&height=463)

Bagan di atas menggambarkan siklus _request-response_ dalam aplikasi web Django. Berikut adalah penjelasan keterkaitan antara `urls.py`, `views.py`, `models.py`, dan berkas HTML dalam proses ini:

1.  **Client Request**: _Request_ dimulai dari klien (browser atau aplikasi lain dari pengguna) yang mengirimkan permintaan ke server.
2.  **urls.py**: Berkas ini berfungsi sebagai pengarah lalu lintas dalam aplikasi Django atau _routing_. Berkas ini yang menentukan URL mana yang perlu dipanggil dan view mana yang menanganinya. Ketika ada permintaan yang diterima, Django akan mencocokan URL dari permintaan dengan URL yang didefinisikan di `urls.py`.
3.  **views.py**: Setelah `urls.py` menentukan view mana yang harus merespons, kontrol dialihkan ke fungsi atau kelas di berkas `views.py`.
    View bertugas untuk memproses data yang diterima dan memberikan repsons yang sesuai kepada klien. Ini mungkin melibatkan pemanggilan data dari database melalui berkas model.
4.  **models.py**: View dapat berinteraksi dengan `models.py` untuk meminta data dari database.
    Berkas model pada Django mendefinisikan struktur data, menyediakan alat untuk manajemen database seperti mengambil, menambah atau mengubah data (query data).
5.  **Database**: berfungsi untuk menyimpan data yang nantinya akan diambil maupun ditambah.
6.  **models.py**: Data yang diambil dari database dikembalikan ke `models.py`, yang kemudian mengirimkannya kembali ke `views.py`.
7.  **views.py**: Setelah data diterima dari model, view memproses data tersebut dan mempersiapkan konten (biasanya berupa berkas HTML) untuk dikirim kembali ke klien.
8.  **HTML Template**: HTML templat diisi dengan data atau konteks yang disediakan oleh view. Templat ini kemudian di-render menjadi HTML lengkap yang siap dikirim ke klien melalui browser.
9.  **Client Response**: HTML yang sudah di-render dikirim kembali ke klien sebagai respons atas permintaan awal mereka.

### 3) Jelaskan fungsi git dalam pengembangan perangkat lunak!

GIT adalah alat yang digunakan oleh developer dan programmer sebagai sistem kontrol dalam pengembangan perangkat lunak. Tujuan utama dari GIT adalah untuk mengatur versi dari kode sumber, memungkinkan penentuan baris dan kode yang perlu ditambahkan atau diubah. Git dikembangkan oleh Linus Torvalds pada tahun 2005.

Berikut adalah fungsi git dalam pengembagan perangkat lunak :

1.  **Kontrol Versi**: Git memungkinkan _developer_ untuk menyimpan versi berbeda dari sebuah proyek (versi lama atau versi terkini). Hal ini memudahkan _developer_ untuk kembali ke versi sebelumnya, jika _software_ atau aplikasi mengalami masalah pada versi terbaru.
2.  **Kolaborasi**: Git sangat memudahkan kerja tim antar _developer_. Beberapa _developer_ dapat bekerja secara bersamaan pada proyek yang sama tanpa mengganggu pekerjaan orang lain. Git dapat mengelola perubahan dari semua _developer_ dan membantu menggabungkan atau _merging_ pekerjaan mereka secara efisien.
3.  **Pelacakan Perubahan**: Git mencatat setiap perubahan yang dibuat pada kode sumber atau kode utama. Ini mencakup informasi tentang siapa yang membuat perubahan, kapan perubahan itu dibuat, dan detail tentang apa yang diubah.
4.  **Pengembangan Paralel**: Git mendukung pembuatan cabang (_branches_) yang memungkinkan pengembang untuk bekerja pada fitur atau perbaikan secara terpisah dari kode utama (_main branch_). Cabang-cabang ini kemudian dapat digabungkan (_merge_) kembali ke cabang utama setelah pekerjaan selesai. Hal ini memudahkan _developer_ jika ingin mengembangkan fitur baru tanpa mengubah kode sumber atau kode utama.
5.  **Pemulihan**: Tersimpannya semua versi kode, memudahkan _developer_ untuk mengakses dan mengembalikan kode ke keadaan semula jika terjadi kesalahan atau data hilang.
6.  **Kemudahan Penggunaan**: Meskipun menyediakan fungsi kontrol versi yang canggih, Git dirancang untuk mudah digunakan. Ini memungkinkan _developer_ untuk fokus pada pengembangan perangkat lunak daripada mengelola perubahan kode.
7.  **Integrasi dengan Alat Lain**: Git dapat berintegrasi dengan baik dengan berbagai alat pengembangan perangkat lunak lainnya, termasuk _platform_ hosting seperti GitHub, GitLab, dan Bitbucket, serta alat pelacakan isu.
8.  **Kinerja Tinggi**: Git dirancang untuk memberikan kinerja yang cepat dan efisien bahkan dalam proyek dengan riwayat yang sangat besar atau bahkan banyak cabang (_branch_).

Pada intinya, git sangat berguna untuk pengembangan perangkat lunak. _Developer_ memungkinkan melakukan banyak hal seperti kolaborasi hingga penyimpanan perubahan kode dengan sangat mudah.

### 4) Menurut Anda, dari semua framework yang ada, mengapa framework Django dijadikan permulaan pembelajaran pengembangan perangkat lunak?

Menurut saya, Django menjadi pilihan pertama karena kita sebagai mahasiswa FASILKOM UI sudah mempelajari dasar bahasa yang digunakan Django yaitu python di semester 1. Hal ini tentu mempermudah dan mempercepat pembelajaran framework Django tanpa perlu mempelajari lagi python. Selain itu, Django juga memiliki struktur yang terorganisir berdasarkan pola desain "Model-View-Template" (MVT), yang memudahkan pemula memahami bagaimana aplikasi web dapat berinteraksi dengan database, mengelola logika, dan menampilkan data. Django juga menawarkan library yang sangat lengkap, mencakup banyak fitur bawaan seperti sistem autentikasi dan formulir, yang mengurangi kebutuhan untuk menulis kode dari nol.

Tentu masih banyak lagi keunggulan dari Django. Namun, hal yang sudah saya sebutkan diatas mencakup keseluruhan keunggulan utama dari Django.

### 5) Mengapa model pada Django disebut sebagai ORM?

Model dalam Django disebut sebagai ORM, yang merupakan singkatan dari "Object-Relational Mapping". ORM memungkinkan _developer_ untuk berinteraksi dengan database menggunakan kode yang berorientasi objek, bukan dengan menggunakan SQL langsung.

Berikut adalah beberapa alasan mengapa model Django disebut sebagai ORM:

1.  **Abstraksi**: ORM di Django menyediakan lapisan abstraksi yang memungkinkan _developer_ untuk berinteraksi dengan database melalui objek Python. Ini mengabstraksi kompleksitas SQL, sehingga _developer_ dapat bekerja lebih banyak dengan konsep Python daripada dengan detail database.
2.  **Manajemen Data**: Dengan ORM, objek dalam kode Python dapat dengan mudah dibuat, dibaca, diperbarui, dan dihapus melalui database tanpa perlu menulis kueri SQL secara eksplisit.
3.  **Portabilitas**: Kode yang menggunakan ORM lebih portabel. Artinya, aplikasi Django dapat beralih antar jenis database dengan perubahan konfigurasi minimal.
4.  **Keamanan**: ORM juga membantu dalam meningkatkan keamanan aplikasi. Penggunaan ORM mengurangi risiko serangan injeksi SQL, karena kueri yang dibangun melalui ORM lebih terstruktur dan dikontrol ketat oleh sistem.
5.  **Efisiensi Pengembangan**: Menggunakan ORM mempercepat proses pengembangan karena mengurangi jumlah kode yang perlu ditulis dan diuji. Ini juga membantu dalam memelihara kode yang lebih bersih dan lebih mudah untuk dimengerti dengan menggunakan hanya satu bahasa.
