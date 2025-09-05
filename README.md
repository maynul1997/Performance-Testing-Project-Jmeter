# Performance-Testing-Project-Jmeter

## Overview

This repository includes JMeter test plans and reports for performance testing of the Restful Booker API, as well as functional testing of the Dmoney API

## Pre-requisites
- **Apache JMeter** installed (Latest Version Recommended)
- **Java 8 or later** installed
- **GitHub** installed
- **Internet Connection** (for running API requests)

## Folder Structure
```
.
├── booking.jmx                  # JMeter test plan for Restful Booker API
├── dmoney.jmx                   # JMeter test plan for Dmoney API
├── Resources/
│   ├── deposit.csv              # CSV file for agent deposits
│   ├── sendMoney.csv            # CSV file for customer money transfers
│   ├── payment.csv              # CSV file for customer payments
├── booking-api-test-report.xlsx  # Load & stress test reports in Excel format
├── Report/                      # HTML test reports (ignored in Git)
├── .gitignore                    # Excludes dmoney.jtl and Report folder
├── README.md                     # This file
```

## Instructions to Run Tests
### Task 01: Restful Booker API Performance Testing
1. Open **JMeter**.
2. Load the **bookingApi_test.jmx** file.
3. Configure test execution:
   - Set thread count based on your desired user load.
   - Ensure **Gaussian Random Timer** (Deviation: 2000ms, Constant Delay: 500ms) is used.

4. Run the test and observe the results.

5. Generate reports:
   - Load Test (5min, 10min, 20min load steps)
   - Stress Test (gradually increasing load to find bottleneck)
   - Generate an **HTML Report** and save it.
   - Run the following command in your terminal to execute the JMeter test in **non-GUI mode**: ``` jmeter -n -t .\bookingApi_test.jmx -l .\bookingApi_test.jtl -e -o Reports ```
  
   #### 📌 Command Breakdown:
- `-n` → Non-GUI mode (for faster execution)  
- `-t .\bookingApi_testing.jmx` → Load the test script (`bookingApi_testing.jmx`)  
- `-l .\bookingApi_testing` → Store results in `bookingApi_testing`  
- `-e` → Enable HTML report generation  
- `-o Reports` → Save reports to the `Reports` folder


### Task 2: Dmoney API Functional Testing
1. Open JMeter.
2. Load the dmoney.jmx file.
3. Ensure CSV files (deposit.csv, sendMoney.csv, payment.csv) are correctly linked.
4. Run the test to validate transactions.
5. Verify successful assertions in JMeter results.
6. Generate an HTML report. Run the following command in your terminal to execute the JMeter test in non-GUI mode: jmeter -n -t .\dmoney.jmx -l .\dmoney.jtl -e -o Reports



## Reports & Screenshots
**Scenario:**
120,000 users over a 12-hour period log in, create a booking, and search for the
booking.

#### Task Overview: 
This test plan (`bookingApi_testing.jmx`) automates API testing for the **RESTful Booker API**, covering:  
✔ Authentication  
✔ Booking creation with **randomized data**  
✔ Booking search and validation  


#### Test Steps & Configuration: 
1️⃣ **Set Headers** (Using HTTP Header Manager)  
   - `Accept: */*`  
2️⃣ **Login & Authentication**  
   - **Endpoint:** `https://restful-booker.herokuapp.com/auth`  
   - **Method:** `POST`  
   - **Request Body:**  
     ``` json:
         {
            "username": "admin",
            "password": "password123"
      
         }
3️⃣ **Create Booking**

   - **Endpoint:**  
   `https://restful-booker.herokuapp.com/booking`
   
   - **Method:**  
   `POST`
   
   **Request Body (Dynamic Data):**  
   ```json
{
   "firstname": "Generate Random FirstName",
   "lastname": "Generate Random LastName",
   "totalprice": "Generate random amount",
   "depositpaid": true,
   "bookingdates": {
      "checkin": "2024-01-01",
      "checkout": "2024-01-02"
   }
}
```
**Extract Booking ID:**  
Use JSON Extractor to capture `bookingid` for validation.

4️⃣ Search Booking

    - **Endpoint:**  
    `https://restful-booker.herokuapp.com/booking/<booking_id>`

    - **Method:**  
    `GET`

**Validation:**  
Ensure the response contains the correct booking details.

#### Load Test & Stress Test Results
- **Request Summary**
 - 1st step: 5 min load with 833 Users <img width="1920" height="823" alt="1st image" src="https://github.com/user-attachments/assets/d52585d4-676e-4fc9-b1be-df318b061841" />

 - 2nd step: 10 min load with 1662 Users <img width="1915" height="794" alt="2nd image" src="https://github.com/user-attachments/assets/5eba707d-99c5-4e8c-a763-2d0354341ce1" />

 - 3rd step: 20 min load with 3333 Users <img width="1658" height="647" alt="3rd pic" src="https://github.com/user-attachments/assets/c7acd641-340c-469b-8d74-1f42ae950cd3" />

 Stress Test Statistics from HTML Report Identified the bottleneck throughput by conducting a stress test. 
![stress testing](https://github.com/user-attachments/assets/d20f55c5-5bae-4904-8635-a3c3d4fb4d24)

#### Load Test & Stress Test Reports (Excel)
  - [Excel Reports](https://docs.google.com/spreadsheets/d/1Wmkd7em77pIm0UQhoejxlk7c3eAdRWC1PpxQQ3drjlA/edit?usp=sharing)

<img width="963" height="446" alt="load test sheet" src="https://github.com/user-attachments/assets/2c89e6b1-508a-40cb-8e66-62555c984169" />
<img width="1043" height="421" alt="Stress test sheet" src="https://github.com/user-attachments/assets/3d9f16bb-947d-48d2-994c-607ce974db62" />


### Task 2: Dmoney API
#### Scenario Overview:
This test simulates various financial transactions in the **Dmoney API** using JMeter to evaluate performance under concurrent load.

#### API Documentation
- User [(http://dmoney.roadtocareer.net/api-docs/user/)]
- Transactions [http://dmoney.roadtocareer.net/api-docs/transaction/]
#### Test Scenario:
- **5 agents** perform **deposits** for **10 customers**.  
- **5 customers** send money to **another 10 customers**.  
- **5 customers** make **payments** to **2 merchants**.  

#### Implementation Steps:
1. **Authentication**  
   - Log in as an **admin** and generate a token.  
   - Use this token across all threads for secure transactions.  

2. **Test Configuration**  
   - Create **3 Thread Groups**:
     - **Agent Transactions (Deposit)**  
     - **Customer Transactions (Send Money)**  
     - **Merchant Transactions (Payment)**  
   - Use **CSV Data Set Config** for dynamic user data:
     - `deposit.csv` → Agent & Customer account details  
     - `sendMoney.csv` → Sender & Receiver customer details  
     - `payment.csv` → Customer & Merchant details  

3. **Dynamic Transaction Amount**  
   - Implement **Random Variable Controller** to assign random small amounts to prevent zero balance issues.  

4. **Performance Settings**  
   - Each thread has a **ramp-up time of 120 seconds** to simulate real-world usage.  

5. **Assertions for Validation**  
   - Ensure that **all transactions are successful** using assertions.  

#### Functional Test Results
- **Request Summary**
  ![image](https://github.com/user-attachments/assets/95b00971-9dad-4906-8465-0b599633ed8f)


## Notes
- The `dmoney.jtl` file and the `Report/` folder are **excluded from Git** to keep the repository clean.
- The **Excel file contains the detailed steps** for both Load & Stress tests of the **Restful Booker API**.

## Conclusion
This project ensures:
- Performance validation of the **Restful Booker API** under realistic load.
- Successful functional validation of the **Dmoney API**.




