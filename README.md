# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Menurut sayaa untuk kasus BambangShop saat ini satu struct Subscriber saja sudah cukup, ada Observer pattern, interface biasanya dipakai kalau ada banyak jenis observer dengan perilaku yang berbeda-beda tetapi tetap punya method umum yang sama, seperti update().Namun karena di BambangShop, semua subscriber pada dasarnya punya fungsi yang sama, yaitu menerima notifikasi ke endpoint tertentu. Karena itu, belum ada kebutuhan khusus untuk membuat trait.
Jadi menurut saya penggunaan trait di sini masih belum perlu dan struct saja sudah cukup. Trait baru akan lebih berguna kalau nanti ada banyak jenis subscriber dengan cara kerja yang berbeda.

2. Menurut saya, Vec sebenarnya bisa saja dipakai, tetapi kurang cocok untuk kasus ini. Alasannya karena id pada Program dan url pada Subscriber harus unik. Kalau menggunakan Vec, kita harus mengecek satu per satu isi list untuk memastikan datanya belum ada. Ini kurang efisien.

Kalau menggunakan DashMap, data bisa langsung disimpan dengan key yang unik, misalnya url subscriber. Jadi pencarian, penambahan, dan penghapusan data jadi lebih mudah dan lebih cepat. Menurut saya, untuk kasus yang memang butuh uniqueness, DashMap lebih tepat dibanding Vec.

3. Menurut saya Singleton pattern saja tidak cukup. Singleton hanya memastikan bahwa instance data yang dipakai itu cuma satu secara global. Tetapi Singleton tidak otomatis membuat data tersebut aman diakses oleh banyak thread sekaligus.
Dalam aplikasi seperti BambangShop, bissa ada banyak request yang mengakses data subscriber secara bersamaan. Karena itu, kita tetap butuh struktur data yang thread-safe. Di sinilah DashMap diperlukan. Jadi, Singleton dan DashMap sebenarnya punya fungsi yang berbeda yaitu Singleton untuk satu instance global, sedangkan DashMap untuk akses data yang aman secara concurrent. DashMap tetap diperlukan, dan tidak bisa digantikan hanya dengan Singleton pattern.

#### Reflection Publisher-2

1. Pemisahan Service dan Repository dari Model dilakukan untuk mengikuti prinsip separation of concerns. Dalam MVC klasik, Model memang mencakup data dan business logic, tetapi kalau semua digabung dalam satu tempat, kode bisa jadi terlalu kompleks dan sulit dikelola. jadi nanti model hanya untuk representasi data, repo untuk akses dan penyimpanan data dan service untuk bisnis logic kode jadi lebih modular, mudah dibaca, dan lebih mudah di-maintain. Selain itu, perubahan di satu bagian misalnya cara penyimpanan data tidak terlalu mempengaruhi bagian lain.

2. Kalau semua logic dimasukkan ke dalam Model, maka setiap Model (Program, Subscriber, Notification) akan menangani terlalu banyak hal sekaligus: data, logic, dan juga interaksi dengan model lain. Akibatnya model jadi fat model, banyak dependensi antar model, kode jadi sulit dipahami dan sulit di debug, perubahan kecil bisa berdampak ke banyak bagian.

3. Menurut saya, Postman sangat membantu untuk testing API yang sedang kita buat. Dengan Postman kita bisa langsung mencoba endpoint seperti subscribe dan unsubscribe tanpa perlu membuat frontend. Contohnya kita bisa mengirim request http get post dengan mudah
bisa lihat respong langsung, debug api lebih mudah,  fitur yang berguna seperti collection
untuk mengelompokkan endpoint, env variables untuk menyimpan url atau parameter, request body editor memudahkan kirim json dan history melihat requst sebelumnya, dll.

#### Reflection Publisher-3

1. pada tutorial ini kita menggunakan Push model. Alasannya karena publisher secara langsung mengirim data notifikasi ke subscriber saat ada event tertentu, misalnya saat product dibuat, dihapus, atau dipromosikan. Jadi, subscriber tidak perlu meminta data sendiri. Publisher langsung mendorong (push) informasi yang dibutuhkan.

2. Kalau pada kasus ini kita memakai Pull model, maka subscriber tidak akan langsung menerima semua detail data dari publisher. Subscriber hanya akan tahu bahwa ada perubahan, lalu subscriber sendiri yang mengambil data yang dibutuhkan dari publisher.

Kelebihan Pull model:

-Data yang dikirim awal bisa lebih sederhana

-Subscriber bisa mengambil hanya data yang benar-benar dibutuhkan

-Publisher jadi tidak perlu mengirim payload yang lengkap ke semua subscriber

Kekurangan Pull model

-Subscriber jadi lebih bergantung pada publisher karena harus request lagi untuk ambil data

-Proses notifikasi bisa jadi lebih lambat karena perlu request tambahan

-Implementasi jadi lebih kompleks dibanding Push model

-Menambah traffic komunikasi antara subscriber dan publisher

untuk kasus bambangshop Push model lebih cocok karena notifikasi yang dibutuhkan sederhana dan bisa langsung dikirim tanpa langkah tambahan.

3. Kalau tidak memakai multi-threading, maka proses notifikasi akan berjalan secara satu per satu sequential. Artinya, publisher harus menunggu pengiriman notifikasi ke satu subscriber selesai dulu sebelum lanjut ke subscriber berikutnya.

Akibatnya:

Response program bisa jadi lebih lambat
Kalau ada subscriber yang lambat atau tidak merespons, proses notifikasi lain ikut tertunda
Performa aplikasi menurun, terutama jika jumlah subscriber banyak. tanpa multi-threading, proses notifikasi bisa menjadi bottleneck. Dengan multi-threading, beberapa notifikasi bisa dikirim secara bersamaan, sehingga proses jadi lebih cepat dan tidak terlalu menghambat request utama.