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

##### 1. Do we still need an interface (or trait in Rust) in this BambangShop case, or is a single Model struct enough?

In the Observer design pattern, an interface (or trait in Rust) is typically used to define a contract for the Subscriber to ensure that all subscribers implement a common method, such as update(). However, in this BambangShop case, since all subscribers are external Rocket instances and the notification is sent via HTTP POST requests, there is no need for a trait. A single Subscriber struct is sufficient to represent the subscriber's data (e.g., url and name) because the notification mechanism does not rely on polymorphism or method overriding.

##### 2. Is using Vec (list) sufficient or is DashMap (map/dictionary) necessary for unique id in Program and url in Subscriber?

Using DashMap is necessary in this case because it provides efficient lookups and ensures uniqueness of keys (id for Program and url for Subscriber). A Vec would require manual iteration to check for duplicates, which is less efficient and error-prone. Additionally, DashMap allows concurrent access, making it suitable for a multi-threaded environment where multiple threads might add or remove subscribers simultaneously.

##### 3. Do we still need DashMap or can we implement the Singleton pattern instead?

While the Singleton pattern ensures a single instance of the SUBSCRIBERS variable, it does not inherently provide thread safety or efficient concurrent access. DashMap is specifically designed for thread-safe operations, allowing multiple threads to read and write concurrently without additional synchronization mechanisms. Therefore, DashMap is a better choice for this use case, as it combines the benefits of a thread-safe data structure with the Singleton pattern (via lazy_static!).

#### Reflection Publisher-2

##### 1. Why do we need to separate “Service” and “Repository” from a Model in the MVC pattern?

Separating "Service" and "Repository" from the Model adheres to the Single Responsibility Principle (SRP), which states that a class or module should have only one reason to change.

* The Repository is responsible for data access and persistence, abstracting the database or storage layer.
* The Service handles business logic, ensuring that the application logic is decoupled from data storage.
By separating these concerns, we achieve better modularity, testability, and maintainability. Changes in the database structure or business rules will not directly affect the Model, reducing the risk of introducing bugs.

##### 2. What happens if we only use the Model?

If we only use the Model to handle both data storage and business logic, the code becomes tightly coupled and harder to maintain.

* Increased Complexity: Each Model would need to handle its own data access, validation, and business logic, leading to bloated and complex code.

* Code Duplication: Common logic shared across models (e.g., validation or notification logic) would likely be duplicated, increasing the risk of inconsistencies.

* Difficult Interactions: Interactions between models (e.g., Program, Subscriber, and Notification) would require direct dependencies, making the codebase harder to refactor and test.
Overall, this approach would violate SRP and make the system less scalable.

##### 3. How does Postman help in testing your current work?

Postman is an invaluable tool for testing REST APIs. It allows us to simulate HTTP requests and verify the responses without needing a frontend or additional setup.

Features I find helpful:
* Collections: Organizing API endpoints into collections helps manage and test multiple endpoints systematically.
* Environment Variables: Using variables for URLs and parameters makes it easy to switch between development and production environments.
* Automated Testing: Writing test scripts in Postman ensures that endpoints behave as expected after changes.
* Mock Servers: Simulating API responses helps test integrations without relying on a live backend.
These features make Postman a powerful tool for debugging, testing, and ensuring the reliability of APIs in both individual and group projects.

#### Reflection Publisher-3

##### 1. Which variation of Observer Pattern do we use in this tutorial case?

In this tutorial case, we use the Push model of the Observer Pattern. The publisher (BambangShop) actively pushes data (notifications) to the subscribers by sending HTTP POST requests with the relevant information (e.g., product details and status) to the subscriber's URL.

##### 2 . What are the advantages and disadvantages of using the other variation of Observer Pattern (Pull model) for this tutorial case?

If we were to use the Pull model, the subscribers would need to actively request (pull) data from the publisher instead of the publisher pushing notifications.

* Advantages of Pull model:
    * Subscribers have more control over when they retrieve data, reducing unnecessary notifications if they are not interested in certain updates.
    * It can reduce the load on the publisher, as it does not need to send notifications to all subscribers.

* Disadvantages of Pull model:
    * Increased complexity for subscribers, as they need to implement logic to periodically poll the publisher for updates.
    * Higher latency, as subscribers may not receive updates immediately unless they poll frequently.
    * Inefficient for real-time notifications, as subscribers may miss critical updates if they do not poll in time.
    Overall, the Pull model would not be ideal for this case, as the goal is to provide real-time notifications to subscribers.

##### 3. What will happen to the program if we decide to not use multi-threading in the notification process?

If we do not use multi-threading in the notification process:

* Performance Impact: The program will process each subscriber's notification sequentially. If there are many subscribers or if the HTTP POST requests take time to complete, the notification process will become a bottleneck, significantly slowing down the system.
* Blocking Behavior: The main thread will be blocked while sending notifications, potentially delaying other operations (e.g., handling new requests or processing other tasks).
* dScalability Issues: As the number of subscribers grows, the time required to notify all of them will increase linearly, making the system less scalable.
Using multi-threading allows the program to send notifications concurrently, improving performance and ensuring that the system remains responsive even with a large number of subscribers.