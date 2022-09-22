---
title: "State management in automation test scenario"
date: 2022-9-19 21:55:21
description: The post will give a hint to manage state efficiently and well-organized 
tags: ["general", "automation-test", "state-management"]
draft: false
---

Besides well-organized code in a scenario, it is necessary to manage values going through that scenario, especially in End-2-End scenario. Thus, this post will suggest an flow of those values inside an automation test case.
![State-Management](/images/auto-test/state-management-overview.png)

There are 2 main levels:
- Test: includes all steps that are behavior(s) and verifications. And the values will be call as `entities or DAO`.
- Page Object: as defined in [Martin Fowler's post](https://martinfowler.com/bliki/PageObject.html), it includes methods inside a page or fragment/layout of a page.

## State
The term `state` is related to status of application during execution. It can be represented by individual values or combined properties to form object(s).
In a test case, it is usually created, transferred, updated for business actions and checked for verification.

## Example 01
Let's take a simple example:
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

  // Actions
  page.sum(a, b);
  result = page.getResult();

  // Assert 
  Assert.assertEquals(result, expected)
}
```

As can be seen from above code, the variables `a`, `b` and `result` are considered as states.
- `a` and `b` are created and initialized in Arrange stage.
- Then these variables are passed to `sum()` method of `CalculationPage`
- After method consumes variables, it will return the `result`.
- The returned values is compared with the expected value, so `expected` variable is created and instantiated but it is not a state.

## Example 02
Let's create a more complicated example.
Assume that we have to create a test case for making payment flow. So it is required to create some classes below:
```java
// using lombok annotation
@Setter
@Getter
public class TransactionInfo {
    private String name;
    private String id;
    private double amount;
    private String note;
    private String currency;
}
```

Then we have test like this:
```java
@Test
public void transactionTest() {
    // Arrange
    TransactionInfo txn = new TransactionInfo();
    txn.setAmount(10000);
    txn.setCurrency("USD");
    txn.setNote("testing transaction");

    WebDriver driver = new ChromeDriver();
    TransactionPage txnPage = new TransactionPage(driver);
    TransactionResultPage txnResultPage = new TransactionResultPage(driver); // this page will fully display all transaction info

    // Act
    String txnId = txnPage.make(txn);
    txn.setId(txnId);

    TransactionInfo actualTxn = txtResultPage.getTransaction();

    // Assert
    Assert.assertEquals(txn, actualTxn);
}
```

Now state contains properties of TransactionInfo object. As you can see:
- Some properties of `txn` are defined first, in arrange stage. 
- Then `txn` variable are passed to method `make()` to get back transaction id and set to the `id`.
- Next is getting a new instance of transaction info in `txtResultPage.getTransaction()`.
- Finally, compare state `txn` with `actualTxn`.  
Because putting assert command in page object is not good practice, so we will get transaction from page object level to test level.

## Summary 

After 2 examples above, hope that you can have clear view of state management in automation test. There are some principles that you can follow:
- State management is just within only 1 test case, because all test cases should be independent each other.
- State should be created from the beginning of test stage.
- State can be shared between steps in a scenario, but should not share between pages directly. If you want to share stage from page 1 (of step 1) to page 2 (of step 2), page 1 should return to step 1, and then step 2 will take state and pass to page 2.
```
- Don't: page 1 -> page 2
- Do: page 1 -> step 1 -> step 2 > page 2
```
- Stage is normally provided by steps and consumed by pages. Sometimes, the state comes from returned values of page object like Example 2 getTransaction().
