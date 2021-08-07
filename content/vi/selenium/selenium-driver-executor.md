---
title: "Selenium Driver Executor"
date: 2021-07-08 21:55:21
description: Bài viết giúp bạn hiểu rõ hơn về các file driver executors (ChromeDriver.exe, GeckoDriver.exe, EdgeDriver.exe).
draft: false
---

# Introduction
Trong bài viết [trước]({{< ref "./selenium-service.md" >}}), ta đã tìm hiểu về Driver Service là các class ChromeDriverService, FirefoxDriverService,... và biết được rằng các service class này sẽ start trước khi WebDriver được khởi tạo và stop Driver Server sau khi driver quit. Để làm được điều này cần phải xác định file thực thi phù hợp với từng loại trình duyệt:
- Chrome yêu cầu `chromedriver.exe` trên Windows, hoặc `chromedriver` trênn Linux và Mac
- Firefox yêu cầu `geckodriver.exe` trên Windows, hoặc `geckodriver` trênn Linux và Mac.
Trong bài viết này sẽ giúp bạn hiểu rõ hơn các file này làm nhiệm vụ gì và tại sao chúng ta lại cần nó khi thực hiện automation test cho web sử dụng Selenium.

# Driver Executor
Trước khi bắt đầu, ta nhắc lại 1 chút về log khi khởi tạo ChromeDriver: 1 ChromeDriver sẽ được start ở 1 port bất kỳ hoặc 1 port được xác định trước.

```
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```

## Thử lần 1
Ta gọi các file chromedriver.exe là các file thực thi (executor), vì vậy hãy thử chạy file này ở command line xem sao.
- chuyển tới thư mục chứa file thực thi: `cd <folder-contain-executor>`
- chạy file thực thi đó: `chromedriver.exe` trên Windows, hoặc `./chromedriver` trên Mac hoặc Linux
```
$ ./chromedriver 
Starting ChromeDriver 91.0.4472.101 (...) on port 9515
ChromeDriver was started successfully.
```
Có thể thấy ChromeDriver server được khởi chạy ở port 9515. Dòng log giống với khi ta chạy code automation ở trên. Từ đó có thể thấy rằng, class ChromeDriverService của Selenium sẽ nhận file từ đường dẫn cho trước, và sẽ dùng chính file đó để start và stop ChromeDriver server. Tương tự như vậy, các class driver service khác cũng làm điều tương tự.

## Thử lần 2 với tham số \--help
Bây giờ ta sẽ khám phá thêm về các cấu hình có thể lựa chọn khi chạy file thực thi, bằng việc chạy câu lệnh với tham số `--help`.

```
$ ./chromedriver --help
Usage: ./chromedriver [OPTIONS]

Options
  --port=PORT                     port to listen on
  --adb-port=PORT                 adb server port
  --log-path=FILE                 write server log to file instead of stderr, increases log level to INFO
  --log-level=LEVEL               set log level: ALL, DEBUG, INFO, WARNING, SEVERE, OFF
  --verbose                       log verbosely (equivalent to --log-level=ALL)
  --silent                        log nothing (equivalent to --log-level=OFF)
  --append-log                    append log file instead of rewriting
  --replayable                    (experimental) log verbosely and don't truncate long strings so that the log can be replayed.
  --version                       print the version number and exit
  --url-base                      base URL path prefix for commands, e.g. wd/url
  --readable-timestamp            add readable timestamps to log
  --enable-chrome-logs            show logs from the browser (overrides other logging options)
  --allowed-ips                   comma-separated allowlist of remote IP addresses which are allowed to connect to ChromeDriver
```
Có khá nhiều lựa chọn cho ta có thể cấu hình khi khởi chạy ChromeDriver server.
Ví dụ:
- Để kiểm tra chromedriver version, ta dùng `--version`
```
$ ./chromedriver --version
ChromeDriver 91.0.4472.101 (...)
```
- Để khởi chạy ChromeDriver server ở 1 port mà mình mong muốn (5353) có thể dùng `--port`
```
 ./chromedriver --port=5353
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```
Và còn rất nhiều các cấu hình khác nữa, các bạn đọc có thể thử để khám phá thêm.

## Selenium Driver cài đặt bởi npm
Thông thường, ta thường tải chromedriver từ trang chủ [Chromium](https://chromedriver.chromium.org/), rồi đặt nó ở 1 thư mục nào đó trên máy của chúng ta. Một số bạn có thể dùng WebDriverManager để tự động tải về.

Bài viết này sẽ giới thiệu thêm 1 cách nữa, đó là dùng node package manager (npm) của nodejs.
Command: `npm i -g chromedriver`.  
- Command này sẽ tải chromedriver về và lưu ở 1 folder nhất định (trên Mac `/usr/local/lib/node_modules/chromedriver/bin/chromedriver`).
- Khi khởi tạo instance WebDriver, ta không cần set thêm thuộc tính `System.setProperty("webdriver.chrome.driver", "/path/to/chromedriver");` (đã test trên Mac - các bạn thử test trên Windows và Linux xem).
- Với cách này ta có thể tạo ra các script (shell, batch,python...) để tải về chromedriver mà không cần tải bằng tay nữa.
- Thêm nữa, việc lựa chọn các version khác nhau cũng có thể chỉnh trong script `npm i chromedriver@<version>`
