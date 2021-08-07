---
title: "Selenium Driver Executor"
date: 2021-07-08 21:55:21
description: Understand what really deriver executors (ChromeDriver.exe, GeckoDriver.exe, EdgeDriver.exe) are.
draft: false
---

# Introduction
As in [previous]({{< ref "./selenium-service.md" >}}) post, we discussed about Driver Service (like ChromeDriverService, FirefoxDriverService, etc.). It is to start and stop Driver Server before WebDriver initialized and after quit. So that, service needs to be configured driver executable file (via method `useDriverExecutable(File file)`). When working with selenium, you should have a driver executor for coresponding browser, like:
- Chrome browser requires `chromedriver.exe` on Windows, or `chromedrive` on Linux and Mac.
- Firefox browser requires `geckodriver.exe` on Windows, `geckodriver` on Linux and Mac.
- Edge browser requires `edgedriver.exe` on Windows, `edgedriver` on Mac.

So, what are they responsible for? Why do we need it in Selenium web automation?

# Driver Executor
Before we go further, I would remind the log when we start ChromeDriver. It will start a server on free port or on pre-defined port.
```
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```

## First try
Browser drivers are executable, so we can try to run it in command line and check the result.
```
$ ./chromedriver 
Starting ChromeDriver 91.0.4472.101 (...) on port 9515
ChromeDriver was started successfully.
```

Wow, ChromeDriver server was started on port 9515. It was same with using ChromeDriverService in previous post.  
From that, we can see that ChromeDriverService get driver executable file and it will start ChromeDriver server internally in selenium code. Then the other services(FirefoxDriverService, EdgeDriverService) do the same way to start browser Server.

## Find more with \--help
Now, we will explore chromedriver by run it with parameter `--help` to understand more options.

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

There are so many options available that driver server can start with.  
For example:
- To check chromedriver version, use `--version`
```
$ ./chromedriver --version
ChromeDriver 91.0.4472.101 (...)
```
- To start ChromeDriver server with specific port (5353), use `--port`
```
 ./chromedriver --port=5353
Starting ChromeDriver 91.0.4472.101 (...) on port 5353
ChromeDriver was started successfully.
```

## Selenium Driver installed by npm
Normally, you would search for chromedriver on [Chromium site](https://chromedriver.chromium.org/) and download it and put it somewhere on your local machine. Some engineers use WebDriverManager to install automatically.

In some cases, you might install chromedriver or geckodriver via node package manager (npm).  
Command: `npm i -g chromedriver`.  
This command will download `chromedriver` executor and put it somewhere in your machine (on Mac: `/usr/local/lib/node_modules/chromedriver/bin/chromedriver`).
This way shows some advantages as following: 
- chromedriver can be installed automatically by scripts.
- Effortless version selection by command `npm i chromedriver@<version>`
- During initializing WebDriver for Chrome, you should not need set property `System.setProperty("webdriver.chrome.driver", "/path/to/chromedriver");`


