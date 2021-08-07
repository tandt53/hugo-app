---
title: "Selenium Driver Service"
date: 2021-07-08 21:55:21
description: Cách khởi tạo instance WebDriver với các service class với selenium, như ChromeDriverService, FirefoxDriverService,...
tags: ["selenium", "selenium-webdriver", "driver-service", "ChromeDriverService", "FirefoxDriverService", "SafariDriverService", "EdgeDriverService"]
draft: false
---

## Introduction 
Trong bài viết này sẽ giới thiệu cách cách khác nhau để khởi tạo instance của WebDriver, giúp cho bạn hiểu rõ hơn về [kiến trúc của Selenium](https://www.selenium.dev/documentation/en/webdriver/understanding_the_components/).

Bài viết sẽ tập trung vào các constructor của ChromeDriver: 
- Constructor không tham số: `public ChromeDriver()`
- Constructor có 1 tham số ChromeDriverService: `public ChromeDriver(ChromeDriverService service)`

## Constructor không tham số
Đầu tiên, hãy tạo một đoạn code thông thường để khởi tạo ChromeDriver:
```java
String chromeDriverFilePath = "chromedriver"; // I downloaded chromedriver and put it in the root directory of project. On mac, the executor is chromedriver, but on Windows, using chromedriver.exe instead. 
System.setProperty("webdriver.chrome.driver", chromeDriverFilePath); 
WebDriver driver = new ChromeDriver();
driver.get("https://google.com.vn");
```
Đoạn code trên sẽ khởi tạo 1 session selenium và mở trình duyệt Chrome trên máy local. Sau đó sẽ chyển tới tran Google search. Giờ hãy xem log khi chạy đoạn code này:
```
Starting ChromeDriver 91.0.4472.101 (...) on port 8945
ChromeDriver was started successfully.
```
Đây là phần log đã được rút gọn, chỉ thể hiện phần nội dung mà chúng ta sẽ quan tâm trong bài viết này.
Đoạn log ở trên cho ta thấy ChromeDriver đã được khởi chạy thành công ở port 8945. Tức là đang có 1 server nào đó được khởi tạo ở máy local lắng nghe ở port 8945. Nhưng trong code chúng ta không hề thấy dòng nào là start server cả, chúng ta chỉ khởi tạo instance của ChromeDriver và mở trình duyệt thôi mà. Vậy đoạn log này là do đâu? ChromeDriver có phải là 1 server?
Phần tiếp theo chúng ta sẽ đi tìm câu trả lời cho các câu hỏi này.

## Constructor có 1 tham số ChromeDriverService
Lần này chúng ta sẽ thay đổi code 1 chút, khởi tạo instance của ChromeDriver với constructor 1 tham số.

```java
String chromeDriverFilePath = "chromedriver";
ChromeDriverService.Builder builder = new ChromeDriverService.Builder();
builder.usingAnyFreePort();
builder.usingDriverExecutable(new File(chromeDriverFilePath));
ChromeDriverService service = builder.build();
WebDriver driver = new ChromeDriver(service);
driver.get("https://google.com.vn");
```
Chương trình trên cho thấy 1 `service` sẽ được tạo thành từ `builder` sau khi đã set 2 thông tin (`usingAnyFreePort()`, và `usingDriverExecutable()` đường dẫn tới file `chromedriver`). Sau đó instance service đó được truyền vào tham số của ChromeDriver để khởi tạo instance driver. Tiếp theo chúng ta sẽ chạy thử để xem có gì khác biệt so với code ở phần trước không.

```
Starting ChromeDriver 91.0.4472.101 (...) on port 29284
ChromeDriver was started successfully.
```
Ta thấy ChromeDervice đã được khởi chạy thành công, nhưng ở port khác. Tiếp theo ta sẽ thử thay đổi code 1 chút nữa:

```java
ChromeDriverService.Builder builder = new ChromeDriverService.Builder();
builder.usingPort(5353); // change 
builder.usingDriverExecutable(new File("chromedriver"));
ChromeDriverService service = builder.build();
service.start(); // new line 
WebDriver driver = new ChromeDriver(service);
driver.get("https://google.com.vn");
driver.quit();  // new line
service.stop(); // new line
```

Có 4 sự thay đổi ở đoạn code ở trên:
- Dòng 2: chỉ đích danh port 5353 cho `builder`.
- Dòng 5: service được `start` trước khi khởi tạo instance của ChromeDriver.
- Dòng 7: gọi method `driver.quit()` sau khi đã mở trình duyệt và chuyển tới trang google search.
- Dòng 8: service được `stop` sau khi driver quit.
Tiếp tục chạy lần nữa để xem log nhé:

```
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```
Lần này ChromeDriver server đã được khởi chạy ở port 5353, đúng như số port ta đã set trong code. Điều này cho thấy ChromeDriverService sẽ đảm nhiệm việc khởi chạy ChromeDriver server, với cấu hình cho trước (hoặc mặc định). Vì vậy, chúng ta hoàn toàn có thể điều chỉnh các cấu hình này để khởi chạy server ở các port khác nhau, sử dụng các chromedriver file khác nhau.

## Summary
Từ việc thử nghiệm các đoạn code ở trên, chúng ta có thể thấy test script sẽ khởi chạy `1 driver server` (có thể là ChromeDriver server, hoặc GeckoDriver server...) ngay khi khởi tạo instance của WebDriver. Server này chúng ta hoàn toàn có thể điều chỉnh cho phù hợp với mục đích của mình. 
Sau khi driver server được khởi chạy, các lệnh như click, sendKeys, getText... từ script (selenium client) sẽ được chuyển thành các request gửi tới driver server để tương tác với browser và trả về kết quả.

Trong bài viết [tiếp theo]({{< ref "./selenium-driver-executor.md" >}}), ta sẽ tìm hiểu chi tiết hơn về các file driver executor (chromedriver.exe, geckodriver.exe,...)
