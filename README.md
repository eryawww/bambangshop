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

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Sistem desain observer berfokus pada desain komunikasi antara publisher (pemilik data) kepada observers (tertarik kepada data). Observer memiliki 2 jenis model Push Model dan Pull Model. Push Model adalah jenis dimana publisher aktif mengirim data kepada observer yang diam sedangkan Pull Model adalah jenis dimana observer aktif meminta data kepada publisher yang hanya mengirim ketika diminta. Push berguna untuk komunikasi real-time sedangkan pull bergunda untuk informasi yang bersifat on-demand.

Interface digunakan untuk mengeneralisasi komunikasi publisher dan observer agar konsisten dan mudah dikembangkan jika nantinya akan diperlukan banyak fitur yang ingin berkomunikasi. Namun, untuk kasus bambang ini jika hanya fitur subscribe yang ingin berkomunikasi, maka single model structure sudah cukup mendukung simplisitas, kecepatan implementasi dan pembacaan kode. 

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Jika data disimpan pada vektor atau list maka id dari subscriber mungkin bersifat duplikat. Penggunaan DashMap membuat kita menyimpan pasangan product_type dengan subscriber secara efisien dan konsisten dikarenakan key dari DashMap yang harus unik. 

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Penggunaan variabel mutable global seperti subscriber pada desain sistem observer adalah alasan yang paling kuat untuk menggunakan DashMap. DashMap didesain khusus untuk mendukung konkurensi tiggi dengan dukungan read dan update. Penggunaan DashMap sudah didukung dengan fleksibilitas dan thread safe yang tidak membuat kita berpikir ulang pada kasus konkurensi.

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Beberapa alasan dibalik pemisahan service dan repository adalah dengan tujuan meningkatkan maintainability. Beberapa aspek yang mendukung maintainability akibar pemisahan ini antara lain adalah mendukugn Single Responsibility Principle, mempermudah testing, meningkatkan reusability, dan meningkatkan modularity sehingga mudah lebih mudah dibaca.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika hanya menggunakan Model, program akan lebih kompleks menurunkan maintainability. Jiak semua logika bisnis, database, dan komunikasi dilakukan pada satu layer yang sama, maka implementasi yang kita buat akan memiliki potensi untuk deppenden. Perubahan kecil dapat memaksa untuk melakukan perubahan ke semuanya (Shotgun Surgery). Testing juga akan sangat ribet dan berpotensi untuk berubah-ubah setiap pembaruan.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Postman adalah tool yang membantu saya mendebug dan memantau implementasi saya. GUI friendly dari postman adalah kelebihan utamanya. Kemampuan postman untuk export dan import yang baru saya pelajari memungkinkan kita untuk mengirimkan testing dengan cepat. Selain itu, pemahaman struktur API juga dapat lebih cepat dimengerti dengan fitur ini.

#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

Jenis observer yang digunakan pada tutorial kali ini adalah push. Hal ini terlihat dari publisher sebagai pemeran aktif yang mengirim data kepada para penggunannya.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Keuntungan menggunakan pull dari Observer adaah mengurangi keterkaitan antara pengamat dan subjek, memungkinkan fleksibilitas lebih. Kekurangannya adalah dapat menyebabkan peningkatan kompleksitas yang lebih dalam mengelola observer.


3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

Akan menghilangkan sifat real-time dikarenakan proses notifikasi yang dilakukan satu persatu. Efek ini akan terlihat jelas jika jumlah pengamat dalam jumlah sangat besar.