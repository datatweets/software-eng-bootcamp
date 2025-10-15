# Introduction to Software Engineering and Definition and Key Concepts


## Learning Objectives

By the end of this lesson, you will be able to:

1. **Define software engineering** using the IEEE standard definition and explain how it differs from simple programming
2. **Apply core principles** of modularity, abstraction, encapsulation, and reusability to improve code quality and maintainability
3. **Identify the fundamental components** of software engineering including requirements analysis, system design, and testing strategies
4. **Evaluate software quality** using established engineering principles like KISS, DRY, and YAGNI
5. **Recognize the systematic approach** that distinguishes professional software engineering from ad-hoc development

## Introduction

Software engineering is more than just writing code—it's the systematic application of engineering principles to software development. While you might already be comfortable writing functions and building features, understanding software engineering transforms you from someone who writes code into someone who architects solutions [1].

The IEEE defines software engineering as "the application of a systematic, disciplined, quantifiable approach to the development, operation, and maintenance of software" [2]. This definition highlights three critical aspects: it's systematic (follows established processes), disciplined (adheres to standards and best practices), and quantifiable (measurable and predictable). These characteristics separate professional software engineering from casual programming.

Why does this matter? Consider that approximately 70% of software projects fail due to poor requirements, inadequate design, or lack of systematic approach [3]. Software engineering provides the frameworks, principles, and practices that help teams build reliable, efficient, and maintainable systems. Whether you're working on a microservice architecture, a mobile application, or a data pipeline, these foundational concepts apply universally.

In today's fast-paced development environment, understanding software engineering principles helps you make better architectural decisions, write more maintainable code, and collaborate effectively with teams. This lesson focuses on the core concepts that every intermediate developer should master to elevate their craft from coding to engineering.

## Core Content

### Understanding Software Engineering as a Discipline

Software engineering emerged as a distinct discipline in the 1960s when the "software crisis" revealed that traditional programming approaches couldn't scale to meet growing complexity. Unlike programming, which focuses on writing code to solve specific problems, software engineering encompasses the entire lifecycle of software products—from conception through retirement [2].

The discipline operates within a framework of principles, best practices, and methods that have evolved over decades. These aren't arbitrary rules but proven approaches that lead to reliable, efficient, and effective products. Software engineering integrates knowledge from computer science, project management, quality assurance, and domain expertise to create solutions that meet user needs while remaining maintainable over time [1].

Think of it this way: a programmer might build a bridge using whatever materials are at hand, while an engineer calculates load-bearing requirements, selects appropriate materials, and follows building codes. Similarly, a software engineer doesn't just make code work—they ensure it's testable, maintainable, scalable, and meets quality standards.

The systematic nature of software engineering means following established processes for requirements gathering, design, implementation, testing, and maintenance. The disciplined aspect requires adherence to coding standards, design patterns, and quality metrics. The quantifiable component involves measuring code quality, test coverage, performance metrics, and project progress [2].

### Core Principle: Modularity and Component Design

Modularity is the practice of breaking software into smaller, independent components that can be developed, tested, and maintained separately [4]. This principle is fundamental to managing complexity in modern software systems. Each module should have a single, well-defined responsibility and minimal dependencies on other modules.

<img width="562" height="162" alt="image" src="https://github.com/user-attachments/assets/4de9946e-0862-4e10-a3ae-41029829e63f" />


Consider this example of modular design in Python:

```python
# Non-modular approach - everything in one function
def process_user_order(user_id, items):
    # Validate user
    if not user_id or user_id < 0:
        return None
    # Calculate total
    total = sum(item['price'] * item['quantity'] for item in items)
    # Apply discount
    if total > 100:
        total *= 0.9
    # Save to database
    # Send email
    # Update inventory
    return total

# Modular approach - separated concerns
class OrderValidator:
    """Handles validation logic for orders"""
    def validate_user(self, user_id):
        """Verify user exists and is active"""
        return user_id and user_id > 0
    
    def validate_items(self, items):
        """Ensure items are valid and in stock"""
        return all(item.get('price', 0) > 0 for item in items)

class PricingEngine:
    """Calculates order totals and applies business rules"""
    def calculate_subtotal(self, items):
        """Sum item prices considering quantities"""
        return sum(item['price'] * item['quantity'] for item in items)
    
    def apply_discounts(self, subtotal, user_tier):
        """Apply tier-based discounts to subtotal"""
        discount_rates = {'gold': 0.15, 'silver': 0.10, 'bronze': 0.05}
        return subtotal * (1 - discount_rates.get(user_tier, 0))

class OrderProcessor:
    """Orchestrates the order processing workflow"""
    def __init__(self):
        self.validator = OrderValidator()
        self.pricing = PricingEngine()
    
    def process_order(self, user_id, items, user_tier='bronze'):
        """Process complete order with validation and pricing"""
        # Each module handles its specific responsibility
        if not self.validator.validate_user(user_id):
            raise ValueError("Invalid user")
        
        if not self.validator.validate_items(items):
            raise ValueError("Invalid items")
        
        subtotal = self.pricing.calculate_subtotal(items)
        final_total = self.pricing.apply_discounts(subtotal, user_tier)
        
        return final_total
```

The modular approach offers several advantages. First, each class has a single responsibility, making it easier to understand and test. Second, you can modify the pricing logic without touching validation code. Third, you can reuse these modules in different contexts—perhaps the `PricingEngine` is used in both order processing and quote generation [4].

Modularity also enables parallel development. Different team members can work on `OrderValidator` and `PricingEngine` simultaneously without conflicts. When bugs appear, you can isolate them to specific modules rather than debugging a monolithic function.

### Core Principle: Abstraction and Information Hiding

Abstraction involves hiding implementation details and exposing only the necessary functionality to other parts of the system [4]. This principle reduces complexity by allowing developers to work with high-level concepts without worrying about low-level details. Good abstraction creates clear interfaces between components.

Here's an example demonstrating abstraction layers:

```python
from abc import ABC, abstractmethod

# Abstract interface - defines WHAT without specifying HOW
class PaymentProcessor(ABC):
    """Abstract base class defining payment processing interface"""
    
    @abstractmethod
    def process_payment(self, amount, currency, payment_details):
        """Process payment and return transaction ID"""
        pass
    
    @abstractmethod
    def refund_payment(self, transaction_id, amount):
        """Refund a previous payment"""
        pass

# Concrete implementations - each handles HOW differently
class StripePaymentProcessor(PaymentProcessor):
    """Stripe-specific payment implementation"""
    
    def process_payment(self, amount, currency, payment_details):
        """Process payment through Stripe API"""
        # Implementation details hidden from caller
        stripe_token = self._create_stripe_token(payment_details)
        charge = self._create_stripe_charge(amount, currency, stripe_token)
        return charge['id']
    
    def refund_payment(self, transaction_id, amount):
        """Refund through Stripe"""
        return self._create_stripe_refund(transaction_id, amount)
    
    def _create_stripe_token(self, payment_details):
        """Private method - internal implementation detail"""
        # Stripe-specific token creation logic
        pass

class PayPalPaymentProcessor(PaymentProcessor):
    """PayPal-specific payment implementation"""
    
    def process_payment(self, amount, currency, payment_details):
        """Process payment through PayPal API"""
        # Different implementation, same interface
        paypal_order = self._create_paypal_order(amount, currency)
        approval = self._get_customer_approval(paypal_order)
        return self._capture_payment(approval)
    
    def refund_payment(self, transaction_id, amount):
        """Refund through PayPal"""
        return self._create_paypal_refund(transaction_id, amount)

# High-level code works with abstraction, not implementation
class CheckoutService:
    """Service that uses payment processor abstraction"""
    
    def __init__(self, payment_processor: PaymentProcessor):
        # Depends on abstraction, not concrete implementation
        self.payment_processor = payment_processor
    
    def complete_checkout(self, cart, payment_details):
        """Complete checkout using any payment processor"""
        total = cart.calculate_total()
        
        try:
            # Same code works with Stripe, PayPal, or any future processor
            transaction_id = self.payment_processor.process_payment(
                amount=total,
                currency='USD',
                payment_details=payment_details
            )
            return {'success': True, 'transaction_id': transaction_id}
        except Exception as e:
            return {'success': False, 'error': str(e)}

# Usage - swap implementations without changing checkout logic
stripe_checkout = CheckoutService(StripePaymentProcessor())
paypal_checkout = CheckoutService(PayPalPaymentProcessor())
```

This abstraction provides multiple benefits. The `CheckoutService` doesn't need to know whether it's using Stripe or PayPal—it just calls `process_payment()`. If you need to add a new payment provider, you implement the `PaymentProcessor` interface without modifying existing code. Testing becomes easier because you can create mock payment processors [4].

Abstraction also protects against changes. If Stripe changes their API, you only update `StripePaymentProcessor`. The rest of your application remains unaffected because it depends on the abstract interface, not the concrete implementation.

### Core Principle: Encapsulation and Data Protection

Encapsulation bundles data and the methods that operate on that data into a single unit, while restricting direct access to internal state [4]. This principle prevents external code from making invalid modifications and maintains object consistency. Proper encapsulation creates clear boundaries and enforces invariants.

<img width="650" height="556" alt="image" src="https://github.com/user-attachments/assets/9ee2939e-5f34-4b54-83b4-bf0e3c263789" />


Here's an example showing encapsulation in practice:

```python
class BankAccount:
    """Encapsulates account data and enforces business rules"""
    
    def __init__(self, account_number, initial_balance=0):
        # Private attributes - not directly accessible
        self._account_number = account_number
        self._balance = initial_balance
        self._transaction_history = []
        self._is_frozen = False
    
    @property
    def balance(self):
        """Read-only access to balance"""
        return self._balance
    
    @property
    def account_number(self):
        """Read-only access to account number"""
        return self._account_number
    
    def deposit(self, amount):
        """Controlled way to modify balance - enforces rules"""
        if self._is_frozen:
            raise ValueError("Account is frozen")
        
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        
        # Internal state change with validation
        self._balance += amount
        self._record_transaction('deposit', amount)
        return self._balance
    
    def withdraw(self, amount):
        """Controlled withdrawal with business rule enforcement"""
        if self._is_frozen:
            raise ValueError("Account is frozen")
        
        if amount <= 0:
            raise ValueError("Withdrawal amount must be positive")
        
        if amount > self._balance:
            raise ValueError("Insufficient funds")
        
        # Maintain invariant: balance never goes negative
        self._balance -= amount
        self._record_transaction('withdrawal', amount)
        return self._balance
    
    def freeze_account(self):
        """Administrative action to freeze account"""
        self._is_frozen = True
        self._record_transaction('freeze', 0)
    
    def _record_transaction(self, transaction_type, amount):
        """Private method - internal implementation detail"""
        from datetime import datetime
        self._transaction_history.append({
            'type': transaction_type,
            'amount': amount,
            'timestamp': datetime.now(),
            'balance_after': self._balance
        })
    
    def get_transaction_history(self):
        """Provide read-only copy of transaction history"""
        # Return copy to prevent external modification
        return list(self._transaction_history)

# Usage demonstrates encapsulation benefits
account = BankAccount("ACC-12345", initial_balance=1000)

# Valid operations through public interface
account.deposit(500)  # Balance: 1500
account.withdraw(200)  # Balance: 1300

# Direct access to internal state is prevented
# account._balance = 999999  # Bad practice - bypasses validation
# account.balance = 5000     # Error - balance is read-only property

# Business rules are enforced automatically
try:
    account.withdraw(2000)  # Raises ValueError - insufficient funds
except ValueError as e:
    print(f"Transaction failed: {e}")

# Internal state remains consistent
print(f"Balance: ${account.balance}")  # Always valid
print(f"Transactions: {len(account.get_transaction_history())}")
```

Encapsulation ensures that the account balance can never become negative because all modifications go through validated methods. External code can't accidentally corrupt the transaction history or bypass the frozen account check. The object maintains its invariants regardless of how it's used [4].

This principle becomes critical in larger systems. Imagine if 50 different parts of your application could directly modify `_balance`. Tracking down a bug where the balance becomes negative would be nearly impossible. With encapsulation, you know all balance changes happen through `deposit()` and `withdraw()`, making debugging straightforward.

### Core Principle: Reusability and DRY (Don't Repeat Yourself)

Reusability means creating components that can be used in multiple contexts, saving development time and reducing bugs [4]. The DRY principle states that "every piece of knowledge must have a single, unambiguous, authoritative representation within a system" [5]. When you violate DRY, you create maintenance nightmares—fixing a bug requires updating code in multiple places.

Here's an example showing the transformation from repetitive to reusable code:

```python
# Violates DRY - repeated validation logic
def create_user(username, email, age):
    if not username or len(username) < 3:
        raise ValueError("Username must be at least 3 characters")
    if not email or '@' not in email:
        raise ValueError("Invalid email format")
    if age < 18:
        raise ValueError("User must be 18 or older")
    # Create user...

def update_user(user_id, username, email, age):
    if not username or len(username) < 3:
        raise ValueError("Username must be at least 3 characters")
    if not email or '@' not in email:
        raise ValueError("Invalid email format")
    if age < 18:
        raise ValueError("User must be 18 or older")
    # Update user...

# Follows DRY - reusable validation components
class Validator:
    """Reusable validation logic"""
    
    @staticmethod
    def validate_username(username):
        """Validate username meets requirements"""
        if not username or len(username) < 3:
            raise ValueError("Username must be at least 3 characters")
        return username
    
    @staticmethod
    def validate_email(email):
        """Validate email format"""
        if not email or '@' not in email:
            raise ValueError("Invalid email format")
        return email
    
    @staticmethod
    def validate_age(age, minimum=18):
        """Validate age meets minimum requirement"""
        if age < minimum:
            raise ValueError(f"Age must be at least {minimum}")
        return age

class UserService:
    """Service using reusable validators"""
    
    def __init__(self):
        self.validator = Validator()
    
    def create_user(self, username, email, age):
        """Create user with validation"""
        # Single source of truth for validation rules
        username = self.validator.validate_username(username)
        email = self.validator.validate_email(email)
        age = self.validator.validate_age(age)
        # Create user...
        return {'username': username, 'email': email, 'age': age}
    
    def update_user(self, user_id, username, email, age):
        """Update user with same validation"""
        # Reuses validation logic - no duplication
        username = self.validator.validate_username(username)
        email = self.validator.validate_email(email)
        age = self.validator.validate_age(age)
        # Update user...
        return {'user_id': user_id, 'username': username}

# Even better - reusable across different entities
class ProductService:
    """Different service reusing same validators"""
    
    def __init__(self):
        self.validator = Validator()
    
    def create_product_review(self, username, email, rating):
        """Product reviews use same username/email validation"""
        username = self.validator.validate_username(username)
        email = self.validator.validate_email(email)
        # Product-specific validation
        if not 1 <= rating <= 5:
            raise ValueError("Rating must be between 1 and 5")
        return {'username': username, 'email': email, 'rating': rating}
```

The reusable approach offers significant advantages. When requirements change (e.g., username must be 5 characters), you update one place. The validation logic can be thoroughly tested once and reused confidently. New features automatically benefit from existing validation [5].

Reusability extends beyond functions to entire components. Consider a logging utility, error handling middleware, or data transformation pipeline—these can be designed once and used throughout your application. The key is identifying common patterns and extracting them into reusable abstractions.

### Essential Engineering Principles: KISS, YAGNI, and SOLID Foundations

Beyond the core concepts, several guiding principles help engineers make better design decisions. KISS (Keep It Simple, Stupid) advocates for simplicity over complexity [5]. Simple solutions are easier to understand, test, and maintain. When faced with a design choice, prefer the simpler option unless complexity is justified.

YAGNI (You Aren't Gonna Need It) warns against building features you might need someday [5]. This principle combats over-engineering—the tendency to add flexibility and features "just in case." Build what you need now, not what you might need later. You can always refactor when requirements actually change.

Here's an example contrasting these principles:

```python
# Violates KISS and YAGNI - over-engineered solution
class ConfigurationManager:
    """Overly complex configuration with unused features"""
    
    def __init__(self):
        self.configs = {}
        self.config_history = []  # YAGNI - never used
        self.config_validators = {}  # YAGNI - never used
        self.config_transformers = {}  # YAGNI - never used
    
    def get_config(self, key, default=None, transform=None, validate=None):
        """Too many parameters for simple config retrieval"""
        value = self.configs.get(key, default)
        if validate and key in self.config_validators:
            value = self.config_validators[key](value)
        if transform and key in self.config_transformers:
            value = self.config_transformers[key](value)
        self.config_history.append({'key': key, 'value': value})
        return value

# Follows KISS and YAGNI - simple, focused solution
class SimpleConfig:
    """Simple configuration that does what's needed"""
    
    def __init__(self, config_dict=None):
        self._config = config_dict or {}
    
    def get(self, key, default=None):
        """Get configuration value with optional default"""
        return self._config.get(key, default)
    
    def set(self, key, value):
        """Set configuration value"""
        self._config[key] = value
    
    # Add features only when actually needed
    # If you need validation later, add it then
```

The simple version does exactly what's required—nothing more, nothing less. It's easy to understand, test, and use. If you later need configuration history or validation, you can add it when the requirement is real, not hypothetical [5].

## SOLID Principles: Building Maintainable Object-Oriented Systems

The SOLID principles represent five fundamental guidelines for object-oriented design that work synergistically to create maintainable, flexible, and robust software systems. Introduced and popularized by Robert C. Martin, these principles aren't arbitrary rules but distilled wisdom from decades of software engineering experience. When applied consistently, they complement the core concepts of modularity, abstraction, encapsulation, and reusability, creating a comprehensive framework for professional software development.

### The Five SOLID Principles

SOLID is an acronym where each letter represents a distinct principle:

- **S**ingle Responsibility Principle (SRP)
- **O**pen/Closed Principle (OCP)
- **L**iskov Substitution Principle (LSP)
- **I**nterface Segregation Principle (ISP)
- **D**ependency Inversion Principle (DIP)

These principles address different aspects of software design but share a common goal: reducing coupling, increasing cohesion, and making code easier to understand, test, and modify. Let's explore each principle with practical examples that demonstrate both violations and correct implementations.

### Single Responsibility Principle (SRP)

**Definition:** A class should have one, and only one, reason to change. Each class should have a single, well-defined responsibility within the system.

The Single Responsibility Principle is perhaps the most intuitive yet frequently violated SOLID principle. It states that a class should focus on doing one thing well rather than handling multiple unrelated concerns. When a class has multiple responsibilities, changes to one responsibility can affect or break the others, creating fragile code that's difficult to maintain.

Consider what "reason to change" means in practice. If business rules change, database schema evolves, and reporting formats are updated, how many classes need modification? Ideally, each change should affect only one class. When a class handles user authentication, database persistence, and email notifications, any change to any of these concerns requires touching the same class—a clear SRP violation.

Here's an example showing the problem:

```python
# Violates SRP - multiple responsibilities in one class
class User:
    def __init__(self, username, email):
        self.username = username
        self.email = email
    
    def save_to_database(self):
        """Database persistence - first responsibility"""
        # SQL logic to save user
        pass
    
    def send_welcome_email(self):
        """Email notification - second responsibility"""
        # Email sending logic
        pass
    
    def generate_report(self):
        """Report generation - third responsibility"""
        # Report formatting logic
        pass
```

This `User` class has at least three reasons to change: database schema changes, email service changes, or report format changes. Each modification risks breaking unrelated functionality.

The SRP solution separates these concerns:

```python
# Follows SRP - each class has one responsibility
class User:
    """Represents user data - single responsibility: user entity"""
    def __init__(self, username, email):
        self.username = username
        self.email = email

class UserRepository:
    """Handles user persistence - single responsibility: database operations"""
    def save(self, user):
        # Database logic isolated here
        pass

class EmailService:
    """Manages email notifications - single responsibility: email delivery"""
    def send_welcome_email(self, user):
        # Email logic isolated here
        pass

class UserReportGenerator:
    """Generates user reports - single responsibility: report formatting"""
    def generate_report(self, user):
        # Report logic isolated here
        pass
```

Now each class has exactly one reason to change. Database migrations only affect `UserRepository`. Email service changes only touch `EmailService`. Report format updates only modify `UserReportGenerator`. The `User` class remains stable, changing only when the user entity concept itself evolves.

This separation creates several benefits beyond maintainability. Testing becomes straightforward—you can test database operations independently of email sending. Teams can work on different responsibilities simultaneously without conflicts. Reusability improves since `EmailService` can send emails for any entity, not just users.

### Open/Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions) should be open for extension but closed for modification. You should be able to add new functionality without changing existing code.

The Open/Closed Principle addresses a fundamental challenge in software development: how do you add features to existing code without breaking what already works? The answer lies in designing systems that allow new behavior through extension rather than modification. This principle works hand-in-hand with abstraction—by depending on abstract interfaces rather than concrete implementations, you can introduce new functionality without touching existing code.

Consider a payment processing system that starts with credit card support:

```python
# Violates OCP - must modify class to add payment methods
class PaymentProcessor:
    def process_payment(self, amount, payment_type):
        if payment_type == 'credit_card':
            # Credit card logic
            print(f"Processing ${amount} via credit card")
        elif payment_type == 'paypal':
            # PayPal logic - added later, modified existing code
            print(f"Processing ${amount} via PayPal")
        # Adding bitcoin requires modifying this method again
```

Every new payment method requires modifying `process_payment()`, adding another conditional branch. This violates OCP because the class isn't closed for modification. As payment methods proliferate, the conditional logic becomes unwieldy and error-prone. Worse, modifying working code risks introducing bugs into previously stable payment processing.

The OCP solution uses abstraction and polymorphism:

```python
from abc import ABC, abstractmethod

# Abstract base class - defines the contract
class PaymentMethod(ABC):
    @abstractmethod
    def process(self, amount):
        pass

# Concrete implementations - extend behavior without modifying existing code
class CreditCardPayment(PaymentMethod):
    def process(self, amount):
        print(f"Processing ${amount} via credit card")

class PayPalPayment(PaymentMethod):
    def process(self, amount):
        print(f"Processing ${amount} via PayPal")

class BitcoinPayment(PaymentMethod):
    """New payment method - no modifications to existing classes"""
    def process(self, amount):
        print(f"Processing ${amount} via Bitcoin")

# Payment processor - closed for modification, open for extension
class PaymentProcessor:
    def process_payment(self, amount, payment_method: PaymentMethod):
        """Works with any PaymentMethod implementation"""
        payment_method.process(amount)
```

Now adding Bitcoin payment requires creating a new `BitcoinPayment` class without touching `PaymentProcessor` or existing payment implementations. The processor is closed for modification (its code never changes) but open for extension (new payment methods just implement the interface).

This design provides powerful benefits. Existing payment methods continue working exactly as before—no regression risk. Testing new payment methods doesn't require retesting old ones. Different developers can implement new payment methods without coordination. The system grows by addition rather than modification, a hallmark of maintainable architecture.

### Liskov Substitution Principle (LSP)

**Definition:** Objects of a superclass should be replaceable with objects of its subclasses without breaking the application. Derived classes must be substitutable for their base classes.

The Liskov Substitution Principle, named after computer scientist Barbara Liskov, addresses a subtle but critical aspect of inheritance: behavioral compatibility. It's not enough for a subclass to simply inherit from a base class—the subclass must honor the behavioral contract of the base class. When LSP is violated, code that works with the base class breaks when given a subclass, destroying the reliability of polymorphism.

Think of it this way: if you have a function that accepts a `Bird` object and calls its `fly()` method, should it work with every `Bird` subclass? The answer reveals whether your inheritance hierarchy respects LSP.

Here's a classic violation:

```python
# Appears correct but violates LSP
class Bird:
    def fly(self):
        print("Flying through the air")

class Sparrow(Bird):
    def fly(self):
        print("Sparrow flying")  # Behaves as expected

class Penguin(Bird):
    def fly(self):
        raise Exception("Penguins can't fly!")  # Violates LSP

# Function expects any Bird to fly
def make_bird_fly(bird: Bird):
    bird.fly()  # Should work with any Bird subclass

# Works fine with Sparrow
make_bird_fly(Sparrow())

# Breaks with Penguin - LSP violation
make_bird_fly(Penguin())  # Raises exception!
```

The problem: `make_bird_fly()` works correctly with `Sparrow` but fails with `Penguin`, even though both inherit from `Bird`. This violates LSP because `Penguin` can't be substituted for `Bird` without breaking the program. The inheritance hierarchy is conceptually flawed—not all birds can fly.

The LSP-compliant solution recognizes this distinction:

```python
# Respects LSP - inheritance reflects actual capabilities
class Bird:
    def eat(self):
        print("Eating food")

class FlyingBird(Bird):
    """Only birds that can fly inherit this"""
    def fly(self):
        print("Flying through the air")

class Sparrow(FlyingBird):
    def fly(self):
        print("Sparrow flying")

class Penguin(Bird):
    """Penguin is a Bird but not a FlyingBird"""
    def swim(self):
        print("Swimming in water")

# Function now requires FlyingBird specifically
def make_bird_fly(bird: FlyingBird):
    bird.fly()  # Guaranteed to work

# Works with any FlyingBird
make_bird_fly(Sparrow())  # Success

# Won't compile - Penguin isn't a FlyingBird
# make_bird_fly(Penguin())  # Type error caught early
```

Now the type system prevents LSP violations. `Penguin` is a `Bird` but not a `FlyingBird`, accurately reflecting reality. Functions requiring flight capability accept only `FlyingBird` subclasses, ensuring substitutability. This design makes invalid states unrepresentable—the compiler prevents you from passing penguins to flying functions.

LSP violations often signal that inheritance is being used incorrectly. When a subclass must override a method to throw an exception or return null, question whether inheritance is appropriate. Favor composition over inheritance when behavioral compatibility can't be guaranteed. LSP ensures that polymorphism actually works as intended—any subclass can replace its parent without surprises.

### Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use. Many specific interfaces are better than one general-purpose interface.

The Interface Segregation Principle tackles interface bloat—the tendency to create large interfaces that force implementing classes to support functionality they don't need. When interfaces become too broad, implementing classes must provide stub implementations for irrelevant methods, and clients receive access to methods they shouldn't use. ISP advocates for smaller, focused interfaces that match specific client needs.

Consider a document management system with a large interface:

```python
# Violates ISP - interface too broad
class Document(ABC):
    @abstractmethod
    def open(self):
        pass
    
    @abstractmethod
    def save(self):
        pass
    
    @abstractmethod
    def print(self):
        pass
    
    @abstractmethod
    def fax(self):
        pass
    
    @abstractmethod
    def scan(self):
        pass

# Implementation forced to support everything, even if irrelevant
class ReadOnlyDocument(Document):
    def open(self):
        print("Opening document")
    
    def save(self):
        raise Exception("Read-only document can't be saved")
    
    def print(self):
        print("Printing document")
    
    def fax(self):
        raise Exception("Can't fax")  # Doesn't support faxing
    
    def scan(self):
        raise Exception("Can't scan")  # Doesn't support scanning
```

`ReadOnlyDocument` must implement five methods but only meaningfully supports two (`open` and `print`). The other three throw exceptions, violating the behavioral contract and confusing clients. This interface forces implementations to support operations they can't perform.

The ISP solution segregates interfaces by capability:

```python
# Follows ISP - segregated interfaces
class Openable(ABC):
    @abstractmethod
    def open(self):
        pass

class Saveable(ABC):
    @abstractmethod
    def save(self):
        pass

class Printable(ABC):
    @abstractmethod
    def print(self):
        pass

class Faxable(ABC):
    @abstractmethod
    def fax(self):
        pass

# Implementations only implement relevant interfaces
class ReadOnlyDocument(Openable, Printable):
    """Only implements what it actually supports"""
    def open(self):
        print("Opening document")
    
    def print(self):
        print("Printing document")

class EditableDocument(Openable, Saveable, Printable):
    """Supports opening, saving, and printing"""
    def open(self):
        print("Opening editable document")
    
    def save(self):
        print("Saving changes")
    
    def print(self):
        print("Printing document")

class MultiFunctionDocument(Openable, Saveable, Printable, Faxable):
    """Full-featured document with all capabilities"""
    def open(self):
        print("Opening document")
    
    def save(self):
        print("Saving document")
    
    def print(self):
        print("Printing document")
    
    def fax(self):
        print("Faxing document")
```

Now each class implements only the interfaces that match its actual capabilities. `ReadOnlyDocument` never encounters methods it can't support. Clients depending on `Printable` don't receive irrelevant `save()` or `fax()` methods. The type system ensures clients only access operations the implementation actually supports.

ISP creates more granular abstractions that better match real-world requirements. Instead of one `Document` interface with everything, you have focused interfaces for specific capabilities. This segregation improves maintainability—adding a new operation (like `Encrypt`) doesn't affect existing classes unless they choose to implement it. Testing becomes easier since you can test each capability independently.

The principle also applies to function parameters. Instead of passing an object with ten methods when you only need two, accept an interface with just those two methods. This reduces coupling and makes dependencies explicit—the function signature tells you exactly what capabilities it requires.

### Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

The Dependency Inversion Principle fundamentally changes how we think about dependencies in software systems. Traditional architectures have high-level business logic depending directly on low-level implementation details (databases, file systems, external services). DIP inverts this relationship—high-level code depends on abstractions, and low-level implementations satisfy those abstractions. This inversion creates flexible, testable systems where implementations can change without affecting business logic.

Consider an order service that needs to persist orders:

```python
# Violates DIP - high-level code depends on low-level details
class MySQLDatabase:
    """Low-level implementation detail"""
    def save_to_mysql(self, data):
        print(f"Saving to MySQL: {data}")

class OrderService:
    """High-level business logic"""
    def __init__(self):
        # Direct dependency on concrete implementation
        self.database = MySQLDatabase()
    
    def create_order(self, order_data):
        # Business logic coupled to MySQL specifics
        self.database.save_to_mysql(order_data)
        return "Order created"
```

`OrderService` directly instantiates and depends on `MySQLDatabase`. Switching to PostgreSQL requires modifying `OrderService`. Testing `OrderService` requires an actual MySQL database—you can't easily substitute a test double. The high-level business logic (order creation) is tightly coupled to low-level details (MySQL specifics).

DIP inverts these dependencies:

```python
from abc import ABC, abstractmethod

# Abstraction - defines what we need, not how it's implemented
class OrderRepository(ABC):
    """High-level abstraction for order persistence"""
    @abstractmethod
    def save(self, order_data):
        pass

# Low-level implementations depend on the abstraction
class MySQLOrderRepository(OrderRepository):
    """MySQL implementation of the abstraction"""
    def save(self, order_data):
        print(f"Saving to MySQL: {order_data}")

class PostgreSQLOrderRepository(OrderRepository):
    """PostgreSQL implementation - same abstraction"""
    def save(self, order_data):
        print(f"Saving to PostgreSQL: {order_data}")

class InMemoryOrderRepository(OrderRepository):
    """Test implementation - same abstraction"""
    def save(self, order_data):
        print(f"Saving to memory: {order_data}")

# High-level code depends on abstraction, not implementation
class OrderService:
    """Business logic remains unchanged regardless of storage"""
    def __init__(self, repository: OrderRepository):
        # Depends on abstraction through dependency injection
        self.repository = repository
    
    def create_order(self, order_data):
        # Business logic independent of storage details
        self.repository.save(order_data)
        return "Order created"

# Flexibility - swap implementations without changing business logic
production_service = OrderService(MySQLOrderRepository())
test_service = OrderService(InMemoryOrderRepository())
```

Now `OrderService` depends on the `OrderRepository` abstraction, not concrete database implementations. The implementations depend on the abstraction by implementing its interface—the dependency has been inverted. You can switch databases by passing a different repository implementation without touching business logic. Testing uses `InMemoryOrderRepository` without requiring database infrastructure.

This principle creates architectures where business logic sits at the center, depending on abstractions. Infrastructure details (databases, APIs, file systems) implement those abstractions and plug into the business logic. Changes to infrastructure don't propagate to business rules. The system becomes modular, testable, and flexible.

DIP works synergistically with the other SOLID principles. It complements OCP (systems open for extension), enables LSP (implementations substitute for abstractions), and uses ISP (depend on focused abstractions). Together, these principles create systems that withstand change—the hallmark of professional software engineering.

### How SOLID Principles Work Together

The true power of SOLID emerges when these principles work together synergistically. They aren't isolated rules but a comprehensive framework where each principle reinforces the others:

- **SRP creates focused classes** that naturally satisfy LSP—classes with single responsibilities have consistent, predictable behavior that subclasses can maintain.
- **OCP depends on abstraction** taught by DIP—you extend systems by creating new implementations of abstract interfaces without modifying existing code.
- **ISP prevents LSP violations**—segregated interfaces ensure implementations only promise what they can deliver, maintaining behavioral compatibility.
- **DIP enables OCP**—depending on abstractions allows adding new implementations (extension) without changing high-level code (modification).

Consider a complete example showing SOLID principles working together:

```python
from abc import ABC, abstractmethod

# DIP + ISP: Focused abstractions that high-level code depends on
class NotificationSender(ABC):
    @abstractmethod
    def send(self, recipient, message):
        pass

class UserRepository(ABC):
    @abstractmethod
    def find_by_id(self, user_id):
        pass

# OCP + LSP: Implementations extend without modifying existing code
class EmailNotificationSender(NotificationSender):
    def send(self, recipient, message):
        print(f"Email to {recipient}: {message}")

class SMSNotificationSender(NotificationSender):
    def send(self, recipient, message):
        print(f"SMS to {recipient}: {message}")

# SRP: Single responsibility - user notification logic only
class UserNotificationService:
    def __init__(self, notification_sender: NotificationSender, 
                 user_repo: UserRepository):
        self.sender = notification_sender  # DIP: depends on abstraction
        self.user_repo = user_repo  # DIP: depends on abstraction
    
    def notify_user(self, user_id, message):
        user = self.user_repo.find_by_id(user_id)
        self.sender.send(user.email, message)  # Works with any sender

# OCP: Add new notification method without changing existing code
class PushNotificationSender(NotificationSender):
    def send(self, recipient, message):
        print(f"Push notification to {recipient}: {message}")

# System extensible, testable, maintainable
service = UserNotificationService(
    EmailNotificationSender(),
    user_repo_implementation
)
```

This design exemplifies all five principles working in concert. Each class has a single responsibility (SRP). The system extends by adding new `NotificationSender` implementations without modifying `UserNotificationService` (OCP). Any `NotificationSender` can substitute for another (LSP). Interfaces are focused and specific (ISP). High-level business logic depends on abstractions, not concrete implementations (DIP).

### Common SOLID Pitfalls

**Over-abstraction and Analysis Paralysis**

A common mistake when learning SOLID is creating abstractions for everything, even simple cases that don't warrant them. Not every class needs an interface. Not every responsibility needs separation. SOLID principles guide design decisions but shouldn't paralyze development with excessive layering.

For a simple utility function that formats dates, creating an abstract `DateFormatter` interface with multiple implementations is overkill. Apply SOLID principles when complexity justifies them—when you anticipate multiple implementations, when testing requires isolation, or when change is likely. For stable, simple code, straightforward implementation without elaborate abstractions is perfectly appropriate.

**Misunderstanding "Reason to Change"**

SRP's "one reason to change" often confuses developers. Does a class with five methods have five reasons to change? Not necessarily. If those five methods all support a single responsibility—like parsing CSV files—they represent one reason to change (CSV format changes). The key is identifying cohesive responsibilities, not counting methods.

Conversely, a class with two methods might violate SRP if those methods serve unrelated purposes. A `User` class with `save_to_database()` and `send_email()` has two distinct responsibilities despite having just two methods. Focus on conceptual responsibilities, not method count.

**Interface Explosion**

Aggressive application of ISP can create dozens of tiny interfaces, making the codebase harder to navigate. Balance granularity with practicality. An interface with three highly related methods that clients always use together is fine—you don't need to split it into three single-method interfaces. ISP primarily warns against large interfaces where clients only need a small subset of methods.

### Practical Application Guidelines

**When to Apply SOLID Principles**

Apply SOLID principles in production code where maintainability matters—APIs, core business logic, shared libraries, and systems with multiple developers. These contexts benefit from clear separation of concerns, testability, and flexibility.

Skip SOLID overhead in prototypes, scripts, or throwaway code. A one-time data migration script doesn't need abstract interfaces and dependency injection. Quick experiments benefit from simplicity over architectural rigor.

**Refactoring Toward SOLID**

You don't need perfect SOLID compliance from day one. Start with working code, then refactor as complexity grows. When a class accumulates responsibilities, split it (SRP). When adding features requires modifying existing code, introduce abstractions (OCP). When testing becomes difficult due to dependencies, invert them (DIP).

Refactoring incrementally toward SOLID principles is more practical than designing perfect abstractions upfront. Let real requirements guide abstraction decisions rather than hypothetical future needs.

**Testing as a SOLID Indicator**

Difficulty testing code often reveals SOLID violations. If you can't test a class without a database, you're violating DIP. If testing requires complex setup due to multiple responsibilities, you're violating SRP. If tests break when adding new features, you're violating OCP. Let testing pain guide your refactoring efforts.


## Common Pitfalls and Best Practices

**Pitfall 1: Premature Optimization and Over-Engineering**

Many intermediate developers fall into the trap of building overly complex systems "just in case" future requirements demand it. This violates YAGNI and creates maintenance burdens. For example, building a sophisticated caching layer before measuring performance, or creating elaborate plugin architectures when you only need two implementations [5].

**Best Practice:** Start with the simplest solution that meets current requirements. Use profiling tools to identify actual bottlenecks before optimizing. Refactor toward complexity only when requirements justify it. Remember: you can always add abstraction layers later, but removing unnecessary complexity is much harder.

**Pitfall 2: Breaking Encapsulation for Convenience**

Under deadline pressure, developers often bypass encapsulation by directly accessing private attributes or exposing internal state. This creates tight coupling and makes future changes risky. Consider code that directly modifies `order._status` instead of using `order.update_status()`, bypassing validation and history tracking [4].

**Best Practice:** Treat encapsulation boundaries as contracts. If you need to access internal state, ask whether the class should provide a method for that operation. Use properties for read-only access and methods for operations that maintain invariants. The short-term convenience of breaking encapsulation creates long-term maintenance nightmares.

**Pitfall 3: Copy-Paste Programming (DRY Violations)**

When facing similar problems, developers often copy existing code and modify it slightly. This creates multiple versions of the same logic scattered throughout the codebase. When bugs appear or requirements change, you must find and update every copy [5].

**Best Practice:** When you find yourself copying code, stop and extract it into a reusable function or class. Invest time in creating proper abstractions. Use your IDE's refactoring tools to extract methods and identify duplicated code. The rule of three applies: if you write similar code three times, refactor it into a reusable component.

**Pitfall 4: Ignoring the Single Responsibility Principle**

Classes and functions that do too much become difficult to understand, test, and modify. A common example is a "God class" that handles validation, business logic, database access, and presentation formatting all in one place [6].

**Best Practice:** Each class should have one reason to change. If you struggle to name a class without using "and" or "manager," it probably has too many responsibilities. Break large classes into focused components. Ask yourself: "If this requirement changes, how many classes need to be modified?" The answer should be as few as possible.

## Comparison with Alternatives

**Software Engineering vs. Ad-Hoc Programming**

Ad-hoc programming focuses on making code work immediately without considering long-term maintainability. This approach is faster initially but becomes exponentially more expensive as systems grow. Software engineering invests upfront in design, documentation, and testing, paying dividends over the software's lifetime [1][2].

**When to use ad-hoc approaches:** Prototypes, proof-of-concepts, or throwaway scripts where long-term maintenance isn't a concern. One-time data migrations or exploratory analysis where the code won't be reused.

**When to use software engineering:** Production systems, team projects, or any code that will be maintained over time. Systems where reliability, scalability, or security matter. Projects where multiple developers will work on the codebase.

**Formal Methods vs. Practical Engineering**

Formal methods use mathematical proofs to verify software correctness. While theoretically superior, they require significant expertise and time investment. Practical software engineering balances rigor with pragmatism, using proven principles without mathematical formalism [2].

**Trade-offs:** Formal methods excel in safety-critical systems (aerospace, medical devices) where failure costs are catastrophic. Practical engineering works better for business applications where time-to-market and iterative development matter more than mathematical certainty. Most projects benefit from practical engineering principles—modularity, testing, code review—rather than formal verification.

**Agile Engineering vs. Traditional Waterfall**

Both approaches apply engineering principles but differ in how they structure the development process. Waterfall emphasizes upfront design and sequential phases. Agile embraces iterative development and changing requirements. Software engineering principles apply to both—modularity, abstraction, and testing remain essential regardless of process [7].

**Choosing an approach:** Agile works well when requirements evolve and stakeholder feedback is available. Waterfall suits projects with stable requirements and regulatory constraints. Hybrid approaches often work best, applying engineering rigor within agile iterations. The key insight: process methodology doesn't replace engineering fundamentals—you still need well-designed, maintainable code.

## Key Takeaways

- **Software engineering is systematic and disciplined**: It applies proven principles and practices to create reliable, maintainable software. Unlike ad-hoc programming, it considers the entire software lifecycle from requirements through maintenance [1][2].

- **Modularity manages complexity**: Breaking systems into independent, focused components makes them easier to understand, test, and modify. Each module should have a single responsibility and minimal dependencies on other modules [4].

- **Abstraction hides implementation details**: Well-designed abstractions create clear interfaces that allow components to change independently. Depend on abstractions, not concrete implementations, to create flexible systems [4].

- **Encapsulation protects internal state**: Bundling data with the methods that operate on it prevents invalid modifications and maintains consistency. Use properties and methods to control access to internal state [4].

- **Reusability and DRY eliminate duplication**: Creating reusable components saves development time and reduces bugs. Every piece of knowledge should have a single, authoritative representation in your system [5].

- **KISS and YAGNI prevent over-engineering**: Simple solutions are easier to understand and maintain. Build what you need now, not what you might need someday. Add complexity only when requirements justify it [5].
  
- **SOLID principles work synergistically** to create maintainable object-oriented systems. Each principle addresses different aspects of design but they reinforce each other to produce flexible, testable code.
  
- **Single Responsibility Principle ensures each class has one reason to change**, creating focused components that are easier to understand, test, and modify independently.
  
- **Open/Closed Principle enables extending systems without modifying existing code** by depending on abstractions and using polymorphism to add new behavior.
  
- **Liskov Substitution Principle guarantees that subclasses can replace their parent classes** without breaking functionality, making inheritance hierarchies reliable and polymorphism actually work as intended.
  
- **Interface Segregation Principle prevents interface bloat** by creating focused interfaces that match specific client needs, ensuring implementations only depend on methods they actually use.
  
- **Dependency Inversion Principle decouples high-level business logic from low-level implementation details** by having both depend on abstractions, creating flexible systems where infrastructure can change without affecting business rules.
  
- **SOLID complements core engineering concepts** of modularity, abstraction, encapsulation, and reusability, providing specific guidance for object-oriented design decisions.
  
- **Apply SOLID pragmatically**—these principles guide design but shouldn't create unnecessary complexity. Use them when the problem justifies the abstraction, not dogmatically in all code.

- **Engineering principles work together**: Modularity, abstraction, encapsulation, and reusability complement each other. Apply them consistently to create professional-quality software that stands the test of time.

## Practice Quiz

**Question 1:** You're reviewing code where a `UserManager` class handles user authentication, database operations, email notifications, and report generation. Which software engineering principle is being violated, and how would you fix it?

**Answer:** This violates the **Single Responsibility Principle** (part of modularity). The class has multiple reasons to change—authentication logic changes, database schema changes, email service changes, or report format changes all require modifying the same class. 

**Fix:** Separate concerns into focused classes: `AuthenticationService` for authentication, `UserRepository` for database operations, `NotificationService` for emails, and `ReportGenerator` for reports. The `UserManager` could then orchestrate these services but wouldn't contain their implementation details. This makes each component easier to test, understand, and modify independently [4][6].

**Question 2:** A developer creates this code:

```python
class PaymentProcessor:
    def process_payment(self, amount, payment_type):
        if payment_type == 'credit_card':
            # Credit card processing logic
        elif payment_type == 'paypal':
            # PayPal processing logic
        elif payment_type == 'bitcoin':
            # Bitcoin processing logic
```

What principle is violated, and what's a better approach?

**Answer:** This violates the **Open/Closed Principle** and lacks proper **abstraction**. Adding new payment types requires modifying the existing class, and the conditional logic becomes unwieldy as payment methods grow.

**Better approach:** Create a `PaymentProcessor` abstract base class with a `process_payment()` method. Implement concrete classes (`CreditCardProcessor`, `PayPalProcessor`, `BitcoinProcessor`) for each payment type. Use polymorphism to handle different payment methods without conditional logic. This allows adding new payment types without modifying existing code, as shown in the abstraction examples in the Core Content section [4].

**Question 3:** You find this pattern repeated in five different files:

```python
if not email or '@' not in email or '.' not in email.split('@')[1]:
    raise ValueError("Invalid email")
```

What's the problem, and how should you address it?

**Answer:** This violates the **DRY (Don't Repeat Yourself) principle**. The email validation logic is duplicated across multiple files. If validation requirements change (e.g., adding domain whitelist), you must update all five locations, risking inconsistency and bugs.

**Solution:** Extract validation into a reusable component:

```python
class Validator:
    @staticmethod
    def validate_email(email):
        if not email or '@' not in email or '.' not in email.split('@')[1]:
            raise ValueError("Invalid email")
        return email
```

Now all code uses `Validator.validate_email(email)`, creating a single source of truth. Changes happen in one place and automatically apply everywhere [5].

**Question 4:** A `BankAccount` class exposes `balance` as a public attribute that can be directly modified: `account.balance = 5000`. Why is this problematic from a software engineering perspective?

**Answer:** This violates **encapsulation** and fails to maintain invariants. Direct attribute access bypasses validation and business rules. External code could set negative balances, skip transaction logging, or violate account freeze status. The object can't guarantee its internal consistency.

**Correct approach:** Make `balance` private (`_balance`) and provide controlled access through methods:

```python
@property
def balance(self):
    return self._balance

def deposit(self, amount):
    if amount <= 0:
        raise ValueError("Amount must be positive")
    self._balance += amount
    self._record_transaction('deposit', amount)
```

This ensures all balance modifications follow business rules and maintain transaction history, as demonstrated in the encapsulation examples [4].

**Question 5:** Your team is building a simple CRUD API for a blog. A senior developer suggests implementing a plugin architecture, event sourcing, and a sophisticated caching layer "to make it scalable." What software engineering principles should guide your response?

**Answer:** Apply **KISS (Keep It Simple, Stupid)** and **YAGNI (You Aren't Gonna Need It)**. The suggested architecture adds significant complexity without proven need. For a simple CRUD API, start with straightforward implementation: basic REST endpoints, standard database operations, and simple caching if performance testing shows it's needed.

**Reasoning:** Complex architectures have costs—longer development time, more bugs, harder maintenance, and steeper learning curve for new team members. Build what you need now based on actual requirements. If the blog grows and needs sophisticated features, refactor then with real data about bottlenecks and usage patterns. Premature optimization and over-engineering are common pitfalls that violate fundamental engineering principles [5][7].

## References

[1] Sommerville, I. (2015). Software Engineering (10th Edition). Pearson. URL: https://www.pearson.com/en-us/subject-catalog/p/software-engineering/P200000003290, Quote: "Software engineering is an engineering discipline that is concerned with all aspects of software production from the early stages of system specification through to maintaining the system after it has gone into use."

[2] IEEE Computer Society. (2014). Guide to the Software Engineering Body of Knowledge (SWEBOK). URL: https://www.computer.org/education/bodies-of-knowledge/software-engineering, Quote: "Software engineering is the application of a systematic, disciplined, quantifiable approach to the development, operation, and maintenance of software; that is, the application of engineering to software."

[3] The Standish Group. (2020). CHAOS Report 2020: Beyond Infinity. URL: https://www.standishgroup.com/sample_research_files/CHAOSReport2020.pdf, Quote: "The primary reasons for project failure include incomplete requirements, lack of user involvement, and inadequate resources, with approximately 70% of projects failing to meet their original goals."

[4] Martin, R. C. (2017). Clean Architecture: A Craftsman's Guide to Software Structure and Design. Prentice Hall. URL: https://www.pearson.com/en-us/subject-catalog/p/clean-architecture-a-craftsmans-guide-to-software-structure-and-design/P200000009529, Quote: "The principles of software design—modularity, abstraction, encapsulation, and hierarchy—are the foundation of creating maintainable systems. Good architecture allows systems to be easily understood, developed, tested, and deployed."

[5] Hunt, A., & Thomas, D. (2019). The Pragmatic Programmer: Your Journey to Mastery (20th Anniversary Edition). Addison-Wesley. URL: https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/, Quote: "DRY—Don't Repeat Yourself. Every piece of knowledge must have a single, unambiguous, authoritative representation within a system. When the DRY principle is applied successfully, a modification of any single element of a system does not require a change in other logically unrelated elements."

[6] Martin, R. C. (2008). Clean Code: A Handbook of Agile Software Craftsmanship. Prentice Hall. URL: https://www.pearson.com/en-us/subject-catalog/p/clean-code-a-handbook-of-agile-software-craftsmanship/P200000009044, Quote: "The Single Responsibility Principle states that a class should have one, and only one, reason to change. This principle helps us create more cohesive classes and reduces coupling between different parts of the system."

[7] Beck, K., et al. (2001). Manifesto for Agile Software Development. URL: https://agilemanifesto.org/, Quote: "We are uncovering better ways of developing software by doing it and helping others do it. Through this work we have come to value: Individuals and interactions over processes and tools, Working software over comprehensive documentation, Customer collaboration over contract negotiation, Responding to change over following a plan."
