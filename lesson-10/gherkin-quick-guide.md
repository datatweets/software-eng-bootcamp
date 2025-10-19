# Gherkin Language: Complete Guide and Reference

## 1. Understanding the Basics

### What is Gherkin?
Gherkin is a **plain-text language** used to write test scenarios in BDD. It's designed to be readable by everyone - developers, testers, business analysts, and stakeholders.

### Core Philosophy
- Write tests in **natural language**
- Focus on **behavior**, not implementation
- Create **living documentation** that stays current with code

## 2. The Five Keywords

### **Given** - Setup/Context
Sets up the initial state before an action occurs.

```gherkin
Given the user is logged in
Given the shopping cart is empty
Given I am on the homepage
```

### **When** - Action
Describes the action or event that triggers the behavior.

```gherkin
When I click the "Add to Cart" button
When I enter "john@example.com" in the email field
When I submit the form
```

### **Then** - Assertion/Outcome
Specifies the expected result or outcome.

```gherkin
Then I should see a success message
Then the cart should contain 1 item
Then I should be redirected to the dashboard
```

### **And/But** - Additional Steps
Used to add more steps of the same type for readability.

```gherkin
Given the user is logged in
And the user has items in their cart
And the user has a valid payment method

When I click "Checkout"
And I confirm the order

Then I should see "Order Confirmed"
But I should not see any error messages
```

### **Background** - Common Setup
Runs before each scenario in a feature (like setup that applies to all tests).

```gherkin
Feature: User Account Management

  Background:
    Given the database is clean
    And the user "john@example.com" exists

  Scenario: Login with valid credentials
    When I login with "john@example.com"
    Then I should see the dashboard

  Scenario: Login with invalid credentials
    When I login with "wrong@example.com"
    Then I should see an error message
```

## 3. Complete Feature File Structure

```gherkin
Feature: Short description of the feature
  As a [role]
  I want to [feature]
  So that [benefit]

  Background:
    Given some common setup step

  Scenario: Description of the first scenario
    Given some initial context
    When some action occurs
    Then some testable outcome is achieved
    And some other outcome

  Scenario: Description of the second scenario
    Given a different context
    When a different action occurs
    Then a different outcome is achieved
```

## 4. Scenario Outlines (Data-Driven Tests)

Use **Scenario Outline** with **Examples** to test multiple inputs:

```gherkin
Feature: Login Validation

  Scenario Outline: Login with various credentials
    Given I am on the login page
    When I enter username "<username>"
    And I enter password "<password>"
    And I click the login button
    Then I should see "<result>"

    Examples:
      | username          | password  | result          |
      | valid@email.com   | correct123| Dashboard       |
      | valid@email.com   | wrong     | Invalid password|
      | invalid@email.com | correct123| User not found  |
      | empty             | empty     | Required fields |
```

This creates **4 separate test scenarios** automatically!

## 5. Best Practices

### ✅ DO: Write Declarative Steps

```gherkin
# Good - Describes WHAT, not HOW
Given I have a premium account
When I download a file
Then the download should start immediately

# Bad - Too implementation-focused
Given I navigate to "https://example.com/account"
And I click the element with id "premium-badge"
When I click the button with class "download-btn"
Then the HTTP response should be 200
```

### ✅ DO: Use Business Language

```gherkin
# Good - Business language
Given the customer has an active subscription
When the billing cycle completes
Then the customer should be charged $9.99

# Bad - Technical jargon
Given the user.subscription.status == "active"
When cron.job.billing.execute()
Then assert payment_processor.charge(9.99)
```

### ✅ DO: Keep Scenarios Independent

```gherkin
# Good - Each scenario is self-contained
Scenario: Add item to cart
  Given the cart is empty
  When I add a product
  Then the cart should have 1 item

Scenario: Remove item from cart
  Given the cart has 1 item
  When I remove the item
  Then the cart should be empty

# Bad - Second scenario depends on first
Scenario: Add item to cart
  When I add a product
  Then the cart should have 1 item

Scenario: Remove item from cart
  When I remove the item
  Then the cart should be empty
```

### ✅ DO: Use Meaningful Scenario Names

```gherkin
# Good
Scenario: Guest user can browse products without logging in
Scenario: Premium users get free shipping on orders over $50
Scenario: System prevents duplicate email registration

# Bad
Scenario: Test 1
Scenario: Shopping cart test
Scenario: Check if it works
```

### ❌ DON'T: Add Implementation Details

```gherkin
# Bad - Mentions CSS selectors, URLs, technical details
Given I navigate to "/users/login"
When I type "john@example.com" into "#email-input"
And I click the button with class ".submit-btn"

# Good - Focuses on behavior
Given I am on the login page
When I login with valid credentials
Then I should be logged in
```

## 6. Learning Path

### Week 1: Fundamentals
1. Read the [Cucumber Gherkin Reference](https://cucumber.io/docs/gherkin/reference/)
2. Practice writing simple scenarios with Given-When-Then
3. Convert existing test cases to Gherkin format

### Week 2: Real Examples
```gherkin
Feature: E-commerce Product Search

  Scenario: Search with exact product name
    Given I am on the home page
    And the following products exist:
      | name          | price | category    |
      | iPhone 15     | 999   | Electronics |
      | Samsung TV    | 1200  | Electronics |
      | Coffee Maker  | 80    | Appliances  |
    When I search for "iPhone 15"
    Then I should see 1 search result
    And I should see "iPhone 15" in the results

  Scenario: Filter search results by price range
    Given I am on the search results page
    When I filter by price range 100 to 1000
    Then I should see "iPhone 15"
    But I should not see "Samsung TV"
```

### Week 3: Advanced Patterns
```gherkin
Feature: Multi-step Checkout Process

  Background:
    Given the following products in my cart:
      | product      | quantity | price |
      | Laptop       | 1        | 999   |
      | Mouse        | 2        | 25    |

  Scenario: Complete checkout with credit card
    Given I am on the checkout page
    When I select "Credit Card" as payment method
    And I enter valid card details:
      | field      | value           |
      | number     | 4532123456789012|
      | expiry     | 12/25           |
      | cvv        | 123             |
    And I confirm the order
    Then I should see "Order Confirmed"
    And I should receive a confirmation email
    And my cart should be empty

  @smoke @regression
  Scenario: Apply discount code during checkout
    Given I have a valid discount code "SAVE20"
    When I apply the discount code
    Then the total should be reduced by 20%
    And I should see "Discount applied: $205.80"
```

## 7. Common Patterns & Templates

### Login/Authentication
```gherkin
Scenario: Successful login
  Given I am a registered user
  When I login with valid credentials
  Then I should see my dashboard
  And I should see a welcome message

Scenario: Failed login with invalid password
  Given I am a registered user
  When I login with incorrect password
  Then I should see "Invalid credentials"
  And I should remain on the login page
```

### CRUD Operations
```gherkin
Scenario: Create a new item
  Given I am on the items page
  When I click "New Item"
  And I fill in the item details:
    | field       | value           |
    | title       | Test Item       |
    | description | Test Description|
  And I save the item
  Then I should see "Item created successfully"
  And the item should appear in the list

Scenario: Delete an existing item
  Given an item "Test Item" exists
  When I delete the item
  And I confirm the deletion
  Then the item should not appear in the list
```

### Form Validation
```gherkin
Scenario Outline: Email validation
  Given I am on the registration page
  When I enter email "<email>"
  And I submit the form
  Then I should see "<message>"

  Examples:
    | email              | message                |
    | valid@email.com    | Registration successful|
    | invalid            | Invalid email format   |
    | @example.com       | Invalid email format   |
    | user@              | Invalid email format   |
```

## 8. Tools to Practice

### Online Gherkin Editors
1. **[Gherkin Editor Online](https://www.gurukul.education/gherkin-editor)** - Practice writing Gherkin with syntax highlighting
2. **[Cucumber Studio](https://cucumber.io/tools/cucumber-studio/)** - Collaborative BDD tool

### Practice Exercises
Create Gherkin scenarios for:
- ATM withdrawal system
- Library book checkout
- Restaurant order system
- Hotel booking platform
- Social media post creation

## 9. Quick Practice Exercise

**Task**: Write Gherkin scenarios for a simple calculator.

<details>
<summary>Click to see example solution</summary>

```gherkin
Feature: Basic Calculator Operations

  Scenario: Add two positive numbers
    Given I have a calculator
    When I add 5 and 3
    Then the result should be 8

  Scenario: Divide by zero
    Given I have a calculator
    When I divide 10 by 0
    Then I should see an error "Cannot divide by zero"

  Scenario Outline: Multiple operations
    Given I have a calculator
    When I <operation> <num1> and <num2>
    Then the result should be <expected>

    Examples:
      | operation  | num1 | num2 | expected |
      | add        | 2    | 3    | 5        |
      | subtract   | 10   | 4    | 6        |
      | multiply   | 3    | 7    | 21       |
      | divide     | 20   | 4    | 5        |
```
</details>

## 10. Resources

### Official Documentation
- [Cucumber Gherkin Reference](https://cucumber.io/docs/gherkin/reference/)
- [BDD 101](https://cucumber.io/docs/bdd/)

### Books
- *"The Cucumber Book"* by Matt Wynne and Aslak Hellesøy
- *"BDD in Action"* by John Ferguson Smart

### Practice
- Write scenarios for apps you use daily
- Convert existing test cases to Gherkin
- Review and critique scenarios with peers

---

## Summary Cheat Sheet

| Keyword | Purpose | Example |
|---------|---------|---------|
| `Feature:` | Describes the feature | `Feature: User Login` |
| `Scenario:` | Describes one test case | `Scenario: Login with valid credentials` |
| `Given` | Initial context | `Given I am logged out` |
| `When` | Action/event | `When I enter my credentials` |
| `Then` | Expected outcome | `Then I should see the dashboard` |
| `And/But` | Additional steps | `And I should see my name` |
| `Background:` | Common setup | Runs before each scenario |
| `Scenario Outline:` | Template for multiple tests | With `Examples:` table |

**Remember**: Gherkin is about **communication**, not code. Write it so anyone can understand what the software should do!