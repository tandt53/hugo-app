---
title: "Selenium Service"
date: 2021-07-08 21:55:21
description: Details in browser service classes in selenium, like ChromeDriverService, FirefoxDriverService,...
draft: true
---

## Introduction 

When learning selenium at first, you would know classes of WebDriver, like ChromeDriver, FirefoxDriver, SafariDriver, etc. Have you ever ask yourself whether there is any class related to those classes? Let's check the javadoc of selenium java client.
<screenshot of javadoc here>

You can see that there are 2 types of related classes, options (ChromeOptions, FirefoxOptions, etc) and service (ChromeDriverService, FirefoxDriverService, etc.)
- As you go to the ancious of options class, you may find the Capabilities interface. It is a collection of all configuration for initiate WebDriver.
- In contrast, what are service classes? Those are concrete classes of an abstract class DriverService which `manages the life and death of a native executable driver server`.

The ChromeDriver instance can be instantiated through ChromeDriverService. Have you ever try to use it? and what is that for?

## Give a try

