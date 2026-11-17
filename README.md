# 🚗 Ride-Sharing Platform — Driver Service Round-Robin Load Balancing via Spring Cloud API Gateway

This project demonstrates **client-side Round-Robin load balancing** using **Spring Cloud Gateway** and **Spring Cloud LoadBalancer** (`SimpleDiscoveryClient`) for a ride-sharing platform experiencing heavy incoming traffic.

---

## 📁 Project Structure

```text
ride-sharing-platform/
├── pom.xml                                   (Root parent aggregator pom)
├── mvnw.cmd                                  (Maven wrapper for Windows)
│
├── driver-service/                           (Backend Driver Microservice)
│   ├── pom.xml
│   └── src/
│       └── main/
│           ├── java/
│           │   └── com/example/driver/
│           │       └── DriverServiceApplication.java
│           └── resources/
│               └── application.properties
│
└── api-gateway/                              (API Gateway with Round Robin LB)
    ├── pom.xml
    └── src/
        └── main/
            ├── java/
            │   └── com/example/gateway/
            │       └── ApiGatewayApplication.java
            └── resources/
                └── application.yml
```

---

## ⚙️ How Load Balancing Works

1. **API Gateway (`port 8080`)**:
   - Matches incoming requests with path predicate `Path=/drivers/**`.
   - Routes traffic using the load-balanced URI: `lb://driver-service`.
   - Uses Spring Cloud's built-in `SimpleDiscoveryClient` (`spring.cloud.discovery.client.simple.instances.driver-service`) configured with three instance endpoints:
     - `http://localhost:8081`
     - `http://localhost:8082`
     - `http://localhost:8083`
2. **Spring Cloud LoadBalancer**:
   - Applies the **Round-Robin** algorithm by default.
   - Request 1 ➔ Instance on `8081`
   - Request 2 ➔ Instance on `8082`
   - Request 3 ➔ Instance on `8083`
   - Request 4 ➔ Instance on `8081` (cycles evenly)

---

## 🖥️ How to Import and Run in Spring Tool Suite (STS)

### Step 1: Import Project into STS
1. Open **Spring Tool Suite (STS)**.
2. Click **File** ➔ **Import...**
3. Select **Maven** ➔ **Existing Maven Projects**, then click **Next**.
4. In **Root Directory**, browse and select:
   ```text
   C:\Users\Hp\Documents\ride-sharing-platform
   ```
5. STS will discover:
   - `/pom.xml`
   - `driver-service/pom.xml`
   - `api-gateway/pom.xml`
6. Click **Finish**. Wait for Maven to finish downloading dependencies.

---

### Step 2: Spin Off 3 Driver Service Instances (Ports 8081, 8082, 8083)

To test load balancing, we spin up 3 instances of `DriverServiceApplication` running on different ports:

#### Instance 1 (Port 8081):
1. In STS **Package Explorer**, expand `driver-service` ➔ `src/main/java` ➔ `com.example.driver`.
2. Right-click `DriverServiceApplication.java` ➔ **Run As** ➔ **Spring Boot App**.
3. It starts on the default port **8081** (as configured in `application.properties`).

#### Instance 2 (Port 8082):
1. In STS top menu, click **Run** ➔ **Run Configurations...**
2. In the left panel, find **Spring Boot App** ➔ select **DriverServiceApplication**.
3. Right-click it and choose **Duplicate** (or rename the new configuration to `DriverServiceApplication - 8082`).
4. Switch to the **Arguments** tab.
5. In the **VM arguments** box, type:
   ```text
   -Dserver.port=8082
   ```
6. Click **Apply** ➔ click **Run**.
7. Instance 2 is now running on port **8082**!

#### Instance 3 (Port 8083):
1. Again, open **Run** ➔ **Run Configurations...**
2. Under **Spring Boot App**, duplicate the configuration again (rename to `DriverServiceApplication - 8083`).
3. Switch to the **Arguments** tab.
4. In the **VM arguments** box, type:
   ```text
   -Dserver.port=8083
   ```
5. Click **Apply** ➔ click **Run**.
6. Instance 3 is now running on port **8083**!

---

### Step 3: Run the API Gateway (Port 8080)

1. In STS **Package Explorer**, expand `api-gateway` ➔ `src/main/java` ➔ `com.example.gateway`.
2. Right-click `ApiGatewayApplication.java` ➔ **Run As** ➔ **Spring Boot App**.
3. The API Gateway starts on port **8080**.

---

## 🧪 Verification & Load Distribution Testing

Send requests to the API Gateway on port **8080** via your web browser, Postman, or PowerShell:

### Test URL:
```text
http://localhost:8080/drivers
```

### Expected Output Sequence (Round-Robin Distribution):

* **Request 1:**
  ```text
  Driver Service running on port: 8081
  ```
* **Request 2:**
  ```text
  Driver Service running on port: 8082
  ```
* **Request 3:**
  ```text
  Driver Service running on port: 8083
  ```
* **Request 4:**
  ```text
  Driver Service running on port: 8081
  ```
* **Request 5:**
  ```text
  Driver Service running on port: 8082
  ```

---

## 🔍 Alternative: Running from Terminal (Command Line)

If you ever prefer running from terminal/PowerShell instead of STS:

* **Terminal 1 (Driver Instance 1):**
  ```powershell
  cd C:\Users\Hp\Documents\ride-sharing-platform\driver-service
  mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8081"
  ```
* **Terminal 2 (Driver Instance 2):**
  ```powershell
  cd C:\Users\Hp\Documents\ride-sharing-platform\driver-service
  mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8082"
  ```
* **Terminal 3 (Driver Instance 3):**
  ```powershell
  cd C:\Users\Hp\Documents\ride-sharing-platform\driver-service
  mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8083"
  ```
* **Terminal 4 (API Gateway):**
  ```powershell
  cd C:\Users\Hp\Documents\ride-sharing-platform\api-gateway
  mvn spring-boot:run
  ```

