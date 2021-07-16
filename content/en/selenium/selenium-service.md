---
title: "Selenium Driver Service"
date: 2021-07-08 21:55:21
description: Details in browser service classes in selenium, like ChromeDriverService, FirefoxDriverService,...
tags: ["selenium", "selenium-webdriver", "driver-service", "ChromeDriverService", "FirefoxDriverService", "SafariDriverService", "EdgeDriverService"]
draft: false
---

## Introduction 

In this post, I would like to introduce the different ways to initialize instance of WebDriver. It will help you understand more clearly about [Selenium Architecture](https://www.selenium.dev/documentation/en/webdriver/understanding_the_components/).

This post focuses on ChromeDriver constructors:
- No argument constructor: `public ChromeDriver()`
- Constructor with 1 parameter ChromeDriverService: `public ChromeDriver(ChromeDriverService service)`

## No argument constructor
Firstly, I created a sample source code for initialize ChromeDriver instance:
```java
String chromeDriverFilePath = "chromedriver"; // I downloaded chromedriver and put it in the root directory of project. On mac, the executor is chromedriver, but on Windows, using chromedriver.exe instead. 
System.setProperty("webdriver.chrome.driver", chromeDriverFilePath); 
WebDriver driver = new ChromeDriver();
driver.get("https://google.com.vn");
```

Above codes will start a selenium session and launch Google Chrome browser. Then it navigate to home page of google search. Let's run programm, we can see the log as follow:
```
Starting ChromeDriver 91.0.4472.101 (...) on port 8945
ChromeDriver was started successfully.
```
It shows that ChromeDriver was started successfully on port 8945. It means that there is some server was started on my machine on port 8945. But I did not set any thing like port or server options in our code. Where did they come from? Is ChromeDriver a server?
We will answer those question in next session.

## Constructor with 1 parameter ChromeDriverService
Let's create an instance of ChromeDriverService via Builder and then create ChromeDriver with that.
```java
String chromeDriverFilePath = "chromedriver";
ChromeDriverService.Builder builder = new ChromeDriverService.Builder();
builder.usingAnyFreePort();
builder.usingDriverExecutable(new File(chromeDriverFilePath));
ChromeDriverService service = builder.build();
WebDriver driver = new ChromeDriver(service);
driver.get("https://google.com.vn");
```

The code shows that `service` will be built after set 2 information for `builder` (usingAnyFreePort, and usingDriverExecutable is a file of chromedriver). Then that instance is passed into constructor of ChromeDriver then navigate to Google Search. Now run the program to see the behavior nothing different but log:

```
Starting ChromeDriver 91.0.4472.101 (...) on port 29284
ChromeDriver was started successfully.
```

Let's change a little bit in our code:
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
There are 4 lines updated:
- line 2: we changed builder using any free port by a specific port number (5353).
- line 5: service is started before create instance of ChromeDriver.
- line 7: driver is quit after navigate to Google search.
- line 8: service is stopped after driver quit.
Now run program once again and check the console log:

```
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```

This time, ChromeDriver server was started on port 5353, as we set in `builder` of service. Now we understand that ChromeDriverService will be incharged of initiate ChromeDriver server using configured informations (port and driver executable file). And we can control those configuration (starting server in different port, or using different version of chromedriver) as we want.

## Summary
From above code, we see that out test scripts will start `an driver server` (ChromeDriver server, GeckoDriver server, etc.) whenever we call new instance of WebDriver for corresponding browser. Then, every command (click, sendKeys, getText, etc.) will be transformed from script (selenium client) to that server to interact with browser, and finaly get result.

In [next]({{< ref "./selenium-driver.md" >}}) post, I will present about driver executable file (chromedriver.exe, geckodriver.exe) for more details.