---
title: "How to write a test case properly in automation?"
date: 2021-11-26 21:55:21
description: Bài viết giúp bạn hiểu rõ các việc cần làm khi triển khai 1 test case automation
tags: ["general", "automation-test", "test-case"]
draft: false
---

# Introduction 
Recently I've been asked many questions that related to how to organize code in automation test case? Should assert be placed in test and page object as well? 
In my experience, I have created and review thousands of test cases, and I see that many people got struggle in code structure/flow. Thus I made this topic to share my point of view to answer "how effectively organize and manage automation test case?"

## Requirement 
I will give an example of business requirement as following: `As normal user, I want to calculate sum of 2 number, and the result should be displayed` 
- Developer will implement a method to meet the functional requirement, and create unit test as well.
- Tester will verify the sum function on the UI of Web or Mobile

# The principles of unit test 
Junit and testNG are most popular test frameworks that java developers usually use to create unit test to verify components in their libraries or frameworks. I believe Junit is use more than testNG. 

To define a method is a test case, it will be annotated with `@Test`. Then Junit can distinguish those has test annotation as test case and other do not as normal method.

Inside test method, developers usually follow the conventions of arranging instructions. It is `AAA`:
- `Arrange`: is create, instantiate or setup the conditions before next steps to be executed. It can be called as `Precondition` the term much more familiar with testers.
- `Actions` are the main steps in a test case. The `sum` method will be invoked with needed parameters.
- `Assert` are verify commands to compare the expected result and actual result that is returned from method(s) under test.

E.g: `sum` method and its unit test 
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

# How test cases are created?
Now turn back to normal tasks of testers, creating test cases on test management system, like excel or Testrail, etc. 
- **Test case**  

| id | test case name | purpose | precondition | steps | expected result |  
| -- | ------------- | -------- | -------------------- | ------ | ----------------------- |  
| 01 | happy case | verify sum of 2 numbers | the 1st number is 4 </br> the 2nd number is 6 | 1. Input 2 numbers in 2 text boxes </br>2. Click on sum button | Result of sum is 10 should be displayed |  


Above is a typical test scenario that will be executed manually on Web or Mobile UI. When convert it to automation, there're some important things like:
- Precondition
- Steps
- Expected result
Now let's create an automated test for above case, assuming that we will e

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
  int expected = 10;

  // Act
  page.sum(a, b);
  result = page.getResult();

  // Assert 
  Assert.assertEquals(result, expected)

  // Release
  driver.quit();
}
```

Beside `AAA` as previous example, this example has 1 more stage `Release`. The questions are why do we need this stage? and why does unit test have not?. 
The answers are:
- Automation tests we usually create are integration or system level. Our tests might cause some changes in our system under test, so to ensure the independence of tests, we should release or roll back the status like before tests.
- Unit tests are for small components, and do not impact to SUT status, so no need to have this stage.


## Summary
- The principle `AAA` is still good, but not enough to automation test, we should add 1 more stage `Release` to be `AAAR`.
- Test cases (automation or manual) present the business requirement, they are not the step-by-step instructions. So the `Act` stage should be carefully created. E.g.
```java 
@Test
public void loginTest(){

  // Act: Don't
  loginPage.enterUsername("username");
  loginPage.enterPassword("password");
  loginPage.clickLogin();

  // Act: Do 
  loginPage.login("username", "password"); // method login() in LoginPage class contains 3 above commands

}
```

- Each test should have only 1 check point that match with the test purpose. We can add 1 or more assert commands at the final `Assert` stage. So don't mix `Act - Assert - Act - Assert`.
- Automated cases should be stopped right away when failure occurs. The root cause of failure can be a bug in code or changes in SUT that cases need to update. 
- `Assert` is the stage in test method, not the page object method.






