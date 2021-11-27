---
title: "How to write a test case properly in autoamtion?"
date: 2021-11-26 21:55:21
description: Bài viết giúp bạn hiểu rõ các việc cần làm khi triển khai 1 test case automation
tags: ["general", "automation-test", "test-case"]
draft: false
---

<!-- ---
title: "Selenium Driver Service"
date: 2021-07-08 21:55:21
description: Details in browser service classes in selenium, like ChromeDriverService, FirefoxDriverService,...
tags: ["selenium", "selenium-webdriver", "driver-service", "ChromeDriverService", "FirefoxDriverService", "SafariDriverService", "EdgeDriverService"]
draft: false
--- -->



# Giới thiệu
Gần đây mình nhận được 1 số câu hỏi của các bạn về việc viết automation test, như làm sao để viết test case cho đúng? đặt assert ở đâu cho phù hợp? etc. Trong quá trình mình làm automation cũng review và nhiều bạn cũng gặp khó khăn trong việc tổ chức các test case và page object sao cho phù hợp. Vậy nên mình sẽ có 1 số bài về các chủ đề này. Và đây là topic đầu tiên: làm để viết 1 test case automation hiệu quả, đầy đủ và hợp lý.

## Requirement 
Đầu tiên ta sẽ đưa ra 1 yêu cầu về nghiệp vụ như sau: `Là 1 user, tôi muốn tính tổng 2 số tự nhiên, kết quả được hiển thị trên màn hình.`  
- Developer sẽ phải viết 1 method để thực hiện yêu cầu kia, và viết cả unit test cho method đó nữa.
- Tester sẽ phải test tính năng cộng này trên giao diện website hoặc mobile.  
  
*** Tạm bỏ qua các developer front end khác để sử dụng method kia với front end nhé.

# Developer viết Unit Test như thế nào?
Junit và TestNG là 2 thư viện cho việc viết unit test mà developer thường làm. Họ sử dụng 2 thư viện này để viết các test case cho các method hay class trong thư viện/framework/application của họ. 
Annotation cơ bản và giống nhau của Junit và TestNG đều là `@Test` để xác định 1 method test, phân biệt với các method khác, không phải là method test. Hay nôm na, ta hiểu nó là 1 test case. 
Trong method test, các developer thường có những quy chuẩn về việc nên sắp xếp các câu lệnh, hay gọi các method khác để phục vụ các nhiệm vụ nhất định. Quy chuẩn thông thường mà developer hay dùng đó là `AAA`. 3 chữ A tương ứng là:
- `Arrange`: là cài đặt hay khai báo các giá trị biến ở thời điểm ban đầu, hoặc cũng có thể laf thiết lập 1 trạng thái nào đó trước khi bắt đầu các bước tiếp theo.
- `Actions`: Là các bước chính cho 1 test case. Tại đây gọi tới các method chính mà mình mong muốn để test (method under test).
- `Assert`: kiểm tra hay verify kết quả trả về của method hoặc trạng thái sau khi gọi tới method cần test.
Ví dụ: ta có 1 hàm tính tổng và ta có 1 hàm unit test cho nó

```java
// Method under test
public int sum(int a, int b){
  return a + b;
}

// Method test 
@Test
public void happyCase(){
  // Arrange 
  int a = 4;
  int b = 6;
  int result;
  int expected = 10;

  // Actions 
  result = sum(a, b);

  // Assert
  Assert.assertEquals(result, expected)

}
```

# Tester viết test case và automation như thế nào?
Giờ ta trở lại với công việc thường ngày của 1 tester hay phải tiếp xúc đó là test case trên excel hay test management system nào đó.  
- **Test case**  

| id | tên test case | mục đích | điều kiện tiên quyết | Các bước thực hiện | kết quả mong muốn |  
| -- | ------------- | -------- | -------------------- | ------ | ----------------------- |  
| 01 | happy case | test tính năng cộng 2 số tự nhiên | số thứ nhất là 4 </br> số thứ 2 là 6 | 1. Nhập 2 số đó vào 2 text box </br>2. Click vào button tính tổng | Giá trị tổng số hiển thị là 10 |  

Khi chuyển sang automation test, những điều ta cần quan tâm nhất đó chính là:
- Điều kiện tiên quyết
- Các bước thực hiện hay nghiệp vụ 
- Kết quả mong muốn  

Các thông tin khác chỉ mang tính bổ sung và ta sẽ tạm bỏ qua ở bài viết này. 
Giờ ta hãy so sánh 3 điểm trên có tương đồng với quy chuẩn khi viết unit test của developer không? Tất nhiên là có vì bản chất việc test vẫn như vậy, vẫn chỉ 1 requirement mà thôi, chỉ khác ở cách thể hiện, vì đối tượng cần test khác nhau. 

Hãy xem 1 test case automation mẫu sau: 
```java

@Test
public void happyCase(){
  // Arrange 
  System.setProperty("webdriver.chrome.driver", "chromedriver.exe");
  WebDriver driver = new ChromeDriver();
  CalculationPage page = new CalculationPage(driver);
  int result;
  int a = 4;
  int b = 6;
  int exxpected = 10;

  // Actions
  page.sum(4, 6);
  resul = page.getResult();

  // Assert 
  Assert.assertEquals(result, expected)
}

```








