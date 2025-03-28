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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enoughh?

- Pada implementasi BambangShop, saya hanya punya satu jenis Subscriber, yaitu entitas yang menyimpan id dan url. Karena belum ada variasi lain dalam implementasi subscriber, maka menurut saya penggunaan trait tidak terlalu diperlukan untuk saat ini. Sebuah Model struct tunggal sudah cukup, karena semua subscriber akan memiliki properti dan perilaku yang sama. Meskipun Single Model Struct sudah cukup untuk memenuhi kebutuhan saat ini, penggunaan trait tetap lebih baik untuk meningkatkan fleksibilitas dan maintainability. Dengan trait, saya bisa dengan mudah menambahkan jenis Subscriber lain di masa depan tanpa harus mengubah banyak kode yang sudah ada.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

- Dalam implementasi saat ini, id dalam Program dan url dalam Subscriber harus bersifat unik. Kalau saya pakai Vec (list biasa), saya harus melakukan pencarian manual (misalnya dengan .iter().find()) setiap kali ingin memastikan id/url tidak duplikat, sehingga program akan berjalan dengan kompleksitas O(N).Sementara itu, DashMap adalah HashMap thread-safe, yang memungkinkan akses cepat dan aman dalam lingkungan multi-threading. Dengan DashMap Setiap operasi insert, delete, dan lookup jauh lebih cepat dibandingkan Vec karena menggunakan hashing. Kompleksitas yang dimiliki DashMap adalah O(1) untuk pencarian berdasarkan key, sehingga sudah jelas lebih efisien dibandingkan Vec. Jadi, dalam kasus ini DashMap lebih cocok dibandingkan Vec karena memastikan performa tetap optimal dan menghindari kemungkinan data race dalam multi-threading.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

- Menurut saya, DashMap tetap merupakan pilihan terbaik. DashMap memungkinkan operasi concurrent yang thread-safe, yang berarti beberapa thread bisa mengakses dan memodifikasi data secara bersamaan tanpa race condition.Sementara itu, Singleton Pattern hanya memastikan ada satu instance dari sebuah objek, tetapi tidak otomatis membuatnya aman untuk multi-threading. Kalau saya hanya menggunakan Singleton tanpa mekanisme tambahan seperti Mutex/RwLock, ada risiko race condition.Jadi, meskipun Singleton bisa digunakan, saya tetap perlu DashMap untuk memastikan thread safety dalam pengelolaan Subscriber

#### Reflection Publisher-2
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
- Dalam pola desain Model-View-Controller (MVC), Model biasanya mencakup data storage dan business logic, tetapi jika semua tanggung jawab ini dibebankan ke Model, kode akan sulit dikelola. Oleh karena itu, dalam implementasi BambangShop, saya memisahkan Service dan Repository dari Model agar kode lebih modular dan mudah diperluas. Repository hanya bertanggung jawab pada akses data, sementara Service menangani logika bisnis seperti proses subscribe dan unsubscribe. Dengan pemisahan ini, saya bisa melakukan perubahan pada cara penyimpanan data tanpa harus mengubah logika bisnis atau controller, sehingga sistem lebih fleksibel dan maintainable. Penerapan ini juga sesuai dengan prinsip Good Design.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?
- Jika hanya menggunakan Model tanpa memisahkan Service dan Repository, kode akan menjadi lebih rumit karena Model akan menangani terlalu banyak hal. Dalam kasus BambangShop, jika Program, Subscriber, dan Notification langsung berinteraksi di dalam Model, setiap Model harus mengetahui detail Model lainnya. Hal ini akan menciptakan ketergantungan yang tinggi, sehingga ketika ada perubahan di satu Model, Model lainnya juga harus ikut diubah. Saya membayangkan bahwa tanpa pemisahan ini, setiap kali saya ingin menambahkan fitur baru, saya harus menyusun ulang banyak bagian dalam kode, yang akan memperlambat pengembangan dan meningkatkan risiko bug.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
- Postman sangat membantu saya dalam menguji API BambangShop, terutama dalam memastikan fitur subscribe dan unsubscribe berjalan dengan benar. Postman dapat digunakan untuk menguji API melalui berbagai HTTP METHOD, salah dua yang paling familiar untuk saya adalah GET dan POST. Postman telah membantu saya mengerjakan projek - projek IT sebelum ini dan untuk masa depan juga karena praktis dan mudah digunakan.

#### Reflection Publisher-3
