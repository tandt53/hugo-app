---
title: Sample
date: 2019-12-20T12:00:06+09:00
description: Sample article showcasing basic Markdown syntax and formatting for HTML elements.
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Choi
authorEmoji: 🤖
tags:
- markdown
- css
- html
- themes
categories:
- themes
- syntax
series:
- Themes Guide
image: images/feature1/markdown.png
---


## Custom Element

Với mục đích khắc phục 1 số vấn đề của
- PageFactory.initElements (Tất cả các biến phải được khai báo kiểu dữ liệu WebElement nên không linh hoạt xử lý các trường hợp khác nhau).
- Việc sử dụng wait: lúc nào dùng implicitlyWait, lúc nào dùng explicitWait.

Idea:
- Tạo ra 1 kiểu dữ liệu element mới, không phải kiểu WebElement, ta sẽ không findElement mà chỉ lưu tạm các locator vào 1 attribute của 1 kiểu dữ liệu mới.
- Chỉ khi nào gọi các method (click, sendKey, getText..) lúc đó mới thực hiện findElement
- Việc findElement có thể kết hợp find với các strategy của explicitWait


## Design


- Tạo 1 interface Element (hoặc Element tuỳ việc đặt tên): gồm  
  - các method tương tự như WebElement.
  - các method có tham số timeout.
  - 1 method format để hỗ trợ linh hoạt xử lý case xpath có %s.
- Tạo 1 concrete MobileElement implement Element (có thể tạo ra WebElement cho web)
- Tạo 1 annotation tương tự như `@FindBy` để khởi tạo element 
- Các page object sẽ gọi tới method ElementFactory.initElements