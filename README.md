# Web Application Load Test Suite with Dynamic Login Mechanism using '__RequestVerificationToken' extraction

## Overview
This project is a performance testing suite built with Apache JMeter to simulate user interactions with a web application. It includes a login mechanism that extracts a request verification token from the home page response and uses it to authenticate users, followed by navigation to various pages of the application to mimic real user behavior.

### Key Features
- **Login Mechanism**: 
  - Sends a GET request to the web application’s home page.
  - Extracts the `__RequestVerificationToken` from the response using a Regular Expression Extractor in JMeter.
  - Saves the token to a variable (`token`).
  - Sends a POST request to the `/login` endpoint with the token, username, and password.
- **Navigation**: 
  - Simulates user navigation to multiple pages after successful login.
- **Session Management**: 
  - Uses HTTP Cookie Manager and HTTP Header Manager to maintain session state and handle headers.

## Setup Instructions
1. **Download or Clone the Project**:
   - Obtain the JMeter test plan file from the project repository or directory.
2. **Install JMeter**:
   - Download and install Apache JMeter from [jmeter.apache.org](https://jmeter.apache.org/).
3. **Open the Test Plan**:
   - Launch JMeter and open the `.jmx` file.
4. **Configure Variables**:
   - In the `User Defined Variables` section, set:
     - `BASE_URL_1`: The base URL of the web application (e.g., `orangehrm.com`).
     - `USERNAME`: A valid username for login.
     - `PASSWORD`: The corresponding password.

## Test Plan Structure
The test plan is structured as follows:
- **Thread Group**: Controls the number of users (threads), ramp-up period, and iterations.
- **HTTP Request Defaults**: Defines default settings (e.g., protocol: `https`, server: `${BASE_URL_1}`).
- **HTTP Cookie Manager**: Manages cookies to maintain session state post-login.
- **HTTP Header Manager**: Sets headers for requests (e.g., `Content-Type`).
- **Samplers**:
  - **Go to Home Page**: GET request to `${BASE_URL_1}/`.
    - **Regular Expression Extractor**: Extracts `__RequestVerificationToken` into the `token` variable.
  - **Login Request**: POST request to `${BASE_URL_1}/login` with token, username, and password.
  - **Navigation Requests**: GET requests to pages like `/Privacy`, `/Clients`, `/News`, etc.
- **Listeners**:
  - **View Results Tree**: Displays detailed request/response data.
  - **Summary Report**: Provides a summary of test performance metrics.

## Login Mechanism Details
The login process is implemented in the following steps:

1. **GET Home Page**:
   - **URL**: `${BASE_URL_1}/`
   - **Method**: GET
   - **Purpose**: Retrieves the home page, which contains the `__RequestVerificationToken` in its HTML.

2. **Extract Request Verification Token**:
   - **Component**: Regular Expression Extractor (child of the GET Home Page sampler).
   - **Configuration**:
     - **Reference Name**: `token`
     - **Regular Expression**: `name="__RequestVerificationToken" type="hidden" value="(.+?)"`
     - **Template**: `$1$`
     - **Match No.**: 1
   - **Purpose**: Extracts the token value from the response and stores it in the `token` variable.

3. **POST Login Request**:
   - **URL**: `${BASE_URL_1}/login`
   - **Method**: POST
   - **Parameters**:
     - `__RequestVerificationToken`: `${token}`
     - `Name`: `${USERNAME}`
     - `Password`: `${PASSWORD}`
   - **Headers**:
     - `Content-Type`: `application/x-www-form-urlencoded`
   - **Purpose**: Submits the login form with the token and credentials to authenticate the user.

4. **Navigation**:
   - After successful login, the test suite sends GET requests to various pages (e.g., `${BASE_URL_1}/news/index`, `${BASE_URL_1}/companydetails/1`) to simulate user navigation.

## Notes
- The test suite assumes the web application uses a standard anti-forgery token mechanism (e.g., ASP.NET’s `__RequestVerificationToken`).
- Adjust the `Thread Group` settings (e.g., number of threads, ramp-up period) based on the desired load testing scenario.