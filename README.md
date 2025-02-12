# SSL Web Server Implementation

## 1. SSL Certificate Chain File Error

### Issue:
Encountering `SSL_CTX_use_certificate_chain_file error!` due to an expired certificate.

### Steps to Debug:
1. Verify if the certificate and key match:
    ```sh
    openssl verify -CAfile /home/user/Desktop/Cyber_Security_Code_Lab/lab1/7/root-cert.pem /home/user/Desktop/Cyber_Security_Code_Lab/lab1/7/server.pem
    ```
2. The verification may indicate that the certificate has expired:
    ```
    error 10 at 1 depth lookup: certificate has expired
    error 10 at 0 depth lookup: certificate has expired
    ```
3. Regenerate a new certificate:
    ```sh
    openssl genrsa -out server-key.pem 2048
    openssl req -new -key server-key.pem -out server.csr
    openssl x509 -req -days 365 -in server.csr -signkey server-key.pem -out server.pem
    ```

Note: Self-signed certificates may still pass root certificate validation, but HTTP clients (like libcurl) may reject them by default unless explicitly ignored.

---

## 2. Basic Server Execution

### Running the Web Server:
```sh
./MyWebServer
```
### Testing with `curl`:
```sh
curl -k https://localhost:8000
```
If `index.html` is missing, create it manually:
```sh
/home/WebServer/index.html
```
To request additional files:
```sh
curl -k https://localhost:8000/test.txt
```

---

## 3. Checking System Endianness

### Steps to Implement:
1. Add an endpoint `/endianness` to return system byte order information.
2. Implement `HandleEndianness` function in `HttpProtocol.cpp`.
3. Modify `CHttpProtocol::Analyze` to handle `/endianness` requests.
4. Ensure `PREQUEST` struct includes a `BIO*` field for SSL BIO operations.

### Running the Check:
```sh
curl -k https://localhost:8000/endianness
```

---

## 4. Implementing HTTP Methods (HEAD, GET, POST)

### Steps to Implement:
1. Add `HEAD`, `GET`, and `POST` handling functions.
2. Modify `Analyze` function to process these HTTP methods.
3. Define `METHOD_POST` in `common.h`.

### Testing:
```sh
curl -k https://localhost:8000
curl -I -k https://localhost:8000
curl -X POST -k https://localhost:8000 -d "name=test"
```

### Debugging:
If `Content-Length: 0` appears, update request structure to store `content_length`:
1. Add `int content_length;` to `request` structure.
2. Parse `Content-Length` in `Analyze` function.
3. Update `HandlePost` to use `content_length`.

---

## 5. Boyer-Moore (BM) Algorithm

### Running the Algorithm:
```sh
curl -X POST -k https://localhost:8000 -d "This is a sensitive test string."
```

---

## 6. Implementing HTTP Client

### `HTTP_CLIENT.h`
```cpp
#ifndef HTTP_CLIENT_H
#define HTTP_CLIENT_H

#include <string>
#include <unordered_map>
#include <vector>

// Callback function for receiving server responses
size_t WriteCallback(void* contents, size_t size, size_t nmemb, std::string* s);

// Function to send a POST request
void sendPostRequest(const std::string& url, const std::string& data, int id);

// Multithreaded execution of HTTP requests
void performRequests(const std::string& url, const std::string& data, int numRequests);

#endif // HTTP_CLIENT_H
```

---

## 7. Modifying Makefile

Update the `Makefile` to compile and link necessary files for building the HTTP server and client.

---

## Summary
This project involves setting up an SSL-enabled web server that supports `HEAD`, `GET`, and `POST` requests. It also includes checking system endianness, implementing an HTTP client, and debugging SSL certificate issues.

