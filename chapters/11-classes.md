# Chapter 11 — Classes


## TL;DR

- You are standing in front of a vending machine on a Tuesday afternoon.
- You will practice Define a class with attributes and methods, and explain the difference between a class and an instance; Create instances of a class and modify their attributes; Write the _init_() method to initialize a new instance.
- The chapter moves through Chapter opening, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Chapter opening

You are standing in front of a vending machine on a Tuesday afternoon. The machine is a single physical object, but inside it are dozens of different products — soda cans, snack bags, energy bars. Each product has its own price, quantity, and position in the grid. The machine's job is simple: you choose a slot, insert money, and the machine dispenses the product. The same machine, same design, different inventory.

Now imagine you are the owner of a vending-machine company. You have a thousand machines in a thousand locations. Each machine has different products, different prices, different stock levels. But they all follow the same blueprint. They all have the same buttons, the same payment system, the same internal logic. They are all instances of the same design.

That is what a class is.

In this chapter, you will learn to write a blueprint — a class — that describes what an object is, what data it carries, and what it can do. You will learn to create instances of that class, each with its own state but following the same design. You will learn that this is not just organization; it is the way to scale a program from one machine to a thousand, from one bank account to a million, from one vending machine to a global network of them.

### Learning objectives

By the end of this chapter you will be able to:

- **Define** a class with attributes and methods, and explain the difference between a class and an instance.
- **Create** instances of a class and modify their attributes.
- **Write** the `__init__()` method to initialize a new instance.
- **Understand** the `self` parameter and why it matters.
- **Design** methods that access and modify instance attributes.
- **Recognize** class attributes vs. instance attributes and use each correctly.
- **Implement** magic methods like `__str__()` to customize how objects behave and appear.
- **Identify** and fix errors: forgetting `self`, `AttributeError` from missing attributes, mutable default arguments.

### Prerequisites

- Chapter 3 (Objects): understanding that values are objects with type and identity.
- Chapter 6 (Functions): defining functions, parameters, return values.
- Chapter 9 (Lists) and Chapter 10 (Dictionaries): experience with mutable data structures.
- Comfort creating and calling functions.

### Why this chapter matters

Classes are the language's answer to a profound problem: how do you organize code and data so that one design can scale to a million instances? Every program beyond toy size uses classes — to model users, transactions, products, accounts, game objects, anything that has state and behavior. Learning to think in classes means learning to think at scale. It also means learning that code organization is not a luxury; it is a load-bearing part of how software works.

---

## Concept 1 — What a class is, and why you need one

You are writing a program that manages bank accounts. You start simple:

```python
account1 = {"owner": "Alice", "balance": 1000}
account2 = {"owner": "Bob", "balance": 2500}

def deposit(account, amount):
    account["balance"] += amount

def withdraw(account, amount):
    account["balance"] -= amount

deposit(account1, 100)
print(account1["balance"])  # 1100
```

This works. You have dictionaries representing accounts, and functions that operate on them. But now your program grows. You have a thousand accounts. You add more functions: `validate_withdrawal()`, `apply_interest()`, `freeze_account()`. Each function takes an account dictionary as the first argument. The functions and the data are separate. The data does not carry its own behavior; you have to remember which functions apply to which data structure.

This is where organization breaks down. The dictionary does not remember: "I am a bank account, and if you want to know my balance, the key is `balance`, not `amt` or `amount`." The function does not remember: "This function only works on accounts, not on arbitrary dictionaries." You, the programmer, have to hold all of that in your head.

A *class* is a blueprint that solves this problem. It bundles data and behavior together. A class defines:

- **Attributes** (also called fields or properties) — the data that each object carries. For a bank account, the attributes are the owner's name and the balance.
- **Methods** (also called functions within a class) — the procedures that operate on that data. For a bank account, the methods are `deposit()`, `withdraw()`, and `apply_interest()`.

When you define a class, you are saying: "Here is a template. Every object created from this template will have these attributes and can perform these methods."

An *instance* is a single object created from that blueprint. `account1` and `account2` are two instances of the same class. They follow the same design but carry different data.

Here is the same program, rewritten with a class:

```python
class BankAccount:
    def __init__(self, owner, initial_balance):
        self.owner = owner
        self.balance = initial_balance
    
    def deposit(self, amount):
        self.balance += amount
    
    def withdraw(self, amount):
        self.balance -= amount

account1 = BankAccount("Alice", 1000)
account2 = BankAccount("Bob", 2500)

account1.deposit(100)
print(account1.balance)  # 1100
```

The class definition comes first. Then you create instances. Each instance knows its own data. When you call `account1.deposit(100)`, the method knows which account you are depositing into — the one you called the method on. You don't have to pass the account as a separate argument.

### Object-oriented programming as a style

The style of bundling data and behavior together is called *object-oriented programming* (OOP, from the Greek *orient*, meaning to place or arrange). It is a design philosophy, not a Python-specific feature. Many languages use it. The core idea is simple: organize code around the things you are modeling. A bank account is a thing. It has state (the balance). It has behavior (deposit, withdraw). Make the class match the thing.

This is not the only way to write programs. Functional programming keeps data and functions separate and passes data through a chain of transformations. Procedural programming writes a sequence of steps. Python supports all three styles. Classes are one tool. You use them when bundling data and behavior together makes the program clearer and more maintainable.

### Encapsulation and abstraction — why classes pay off

Two concepts justify the structure of classes.

*Encapsulation* (from the Latin *capsula*, meaning a small container) means packaging data and behavior together, and controlling access to the data. A bank does not let arbitrary code modify the balance. Only the `deposit()` and `withdraw()` methods do. This prevents bugs. If a bug modifies the balance incorrectly, you know it came from one of those two methods, not from anywhere else in the program.

*Abstraction* (from the Latin *abstrahere*, meaning to draw away) means hiding the complexity and showing only what matters. When you use a bank account, you do not need to know how the computer stores the balance. You don't need to know whether it's stored as an integer, a float, or a Decimal. You just call `deposit()` and trust that the balance is updated correctly. The class handles the details. You handle the intention.

### Trade-off: classes vs. dictionaries with functions

When should you use a class instead of a dictionary with separate functions?

**Use a class when:**
- The data and the operations are tightly coupled (they belong together).
- You will create many instances of the same template.
- You want to enforce rules about what data is valid (validation).
- The object has identity that matters (`account1` is not the same as `account2` even if they have the same balance).

**Use a dictionary and functions when:**
- The data is loosely structured and temporary (a single record you will process once).
- You don't need to create many instances.
- The behavior is a one-off transformation, not part of the thing's identity.

For a bank, you use a class. For parsing a configuration file once, a dictionary is fine.

### Worked example — designing a simple class

You are building a pet-adoption app. Each dog has a name, a breed, and an age. You need to be able to create dogs and ask their age in human years (multiply by 7).

```python
class Dog:
    def __init__(self, name, breed, age):
        self.name = name
        self.breed = breed
        self.age = age
    
    def human_years(self):
        return self.age * 7

buddy = Dog("Buddy", "Golden Retriever", 3)
print(f"{buddy.name} is {buddy.human_years()} in human years")  # Buddy is 21 in human years
```

> **Prompt for Claude.** "Write a Python class called `Dog` with attributes `name`, `breed`, and `age`. Include an `__init__()` method that takes these three parameters and stores them as instance attributes. Add a method `human_years()` that returns the age multiplied by 7. Create an instance `buddy = Dog('Buddy', 'Golden Retriever', 3)` and print the human years."

> **Tests to validate.**
> - `buddy.name == "Buddy"` — the name is stored correctly
> - `buddy.human_years() == 21` — the calculation is correct
> - Creating a second instance: `max = Dog("Max", "Labrador", 5); max.human_years() == 35` — multiple instances work independently

### Common misconceptions

**A class is an instance.** No. A class is a blueprint. An instance is an object created from that blueprint. The class `Dog` is not a dog; a `Dog()` created with specific values is a dog.

**Methods are just functions with an extra parameter.** Methods *are* functions that live inside a class, but the first parameter (`self`) is special. It refers to the instance the method is being called on. Without it, the method would not know which instance's data to access.

**Every object needs a class.** Early chapters use simple values — integers, strings — that are built-in objects with built-in classes. You define new classes for new types you create. You don't wrap everything in a class, only the things that benefit from grouping data and behavior.

---

## Concept 2 — The `__init__()` method and instance attributes

When you create an instance of a class, something needs to happen first. The instance's attributes need to be initialized. That is the job of a special method: `__init__()`.

The name `__init__()` is short for *initialize*. The double underscores (read as "dunder," short for double-underscore) signal that this is a **magic method** — a special method that Python calls automatically at key moments. When you write `BankAccount("Alice", 1000)`, Python does not just create an empty object. It calls `__init__()` inside that object, passing the arguments you provided. The `__init__()` method sets up the initial state.

Here is the simplest class:

```python
class Cat:
    def __init__(self):
        self.name = "Kitty"
        self.age = 1

mittens = Cat()
print(mittens.name)  # Kitty
print(mittens.age)   # 1
```

The `__init__()` method has one required parameter: `self`. The name is not magic; it is a convention. `self` refers to the instance being created. When you write `mittens = Cat()`, Python creates the instance and passes it as `self` inside `__init__()`. The lines `self.name = "Kitty"` are creating attributes on that instance. The instance `mittens` now has attributes `name` and `age`.

But this class is not very useful. Every cat is named "Kitty" with age 1. You need `__init__()` to accept parameters so that each instance can have different initial values:

```python
class Cat:
    def __init__(self, name, age):
        self.name = name
        self.age = age

mittens = Cat("Mittens", 3)
shadow = Cat("Shadow", 5)

print(mittens.name)  # Mittens
print(shadow.name)   # Shadow
```

Now `__init__()` takes `name` and `age` as parameters. When you create an instance, you pass the values: `Cat("Mittens", 3)`. Python passes them to `__init__()`, which stores them as attributes using `self.name = name` and `self.age = age`. Each instance has its own values.

### Understanding `self`

The `self` parameter is the source of confusion for beginners. Here is what is actually happening.

When you write:

```python
mittens = Cat("Mittens", 3)
```

Python does three things:
1. Creates a new object in memory (the instance).
2. Calls `__init__(self, "Mittens", 3)`, passing the new object as `self`.
3. Returns the instance, which you assign to the variable `mittens`.

Inside `__init__()`, the line `self.name = name` says: "On the instance that was passed to me as `self`, create an attribute called `name` and set it to the value of the parameter `name`."

Later, when you write:

```python
mittens.name
```

Python looks up the instance `mittens` and finds the attribute `name` inside it. The value is `"Mittens"`.

You do not write `self.mittens.name`. You do not write `Cat.mittens.name`. You write `mittens.name` because `mittens` is the instance, and the attribute lives inside it.

### Instance attributes vs. class attributes

There are two kinds of attributes. An *instance attribute* is unique to each instance. A *class attribute* belongs to the class itself and is shared by all instances.

```python
class Dog:
    species = "Canis familiaris"  # Class attribute
    
    def __init__(self, name, breed):
        self.name = name  # Instance attribute
        self.breed = breed  # Instance attribute

buddy = Dog("Buddy", "Golden Retriever")
max = Dog("Max", "Labrador")

print(buddy.name)     # Buddy (instance attribute)
print(Dog.species)    # Canis familiaris (class attribute)
print(buddy.species)  # Canis familiaris (can access via instance too)
```

Instance attributes are created in `__init__()` and stored on each instance separately. If you change `buddy.name`, `max.name` is unaffected. Class attributes are defined at the class level (not inside a method) and are shared. All instances see the same value. This is useful for information that does not change: a constant price, a database URL, a version number.

### The mutable default argument trap

Here is a dangerous mistake. Suppose you want to track the orders for a coffee shop:

```python
class CoffeeOrder:
    def __init__(self, size=16, items=[]):
        self.size = size
        self.items = items

order1 = CoffeeOrder()
order1.items.append("espresso")

order2 = CoffeeOrder()
print(order2.items)  # ['espresso'] — WRONG!
```

The list in the default argument is created *once*, when the class is defined, not each time `__init__()` is called. Both `order1` and `order2` share the same list object. When you append to `order1.items`, `order2.items` is affected.

The fix is to use `None` as the default and create a new list inside the method:

```python
class CoffeeOrder:
    def __init__(self, size=16, items=None):
        self.size = size
        self.items = items if items is not None else []

order1 = CoffeeOrder()
order1.items.append("espresso")

order2 = CoffeeOrder()
print(order2.items)  # [] — correct
```

This is a subtle bug and easy to miss. Every time you use a mutable default argument (a list, dict, or set) in a method, you are creating shared state. Avoid it.

### Worked example — building a Rectangle class

You are writing a geometry program. You need a class to represent rectangles. Each rectangle has a width and height. You need to calculate its area and perimeter.

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height
    
    def perimeter(self):
        return 2 * (self.width + self.height)

room = Rectangle(10, 15)
print(f"Area: {room.area()}")  # Area: 150
print(f"Perimeter: {room.perimeter()}")  # Perimeter: 50
```

> **Prompt for Claude.** "Write a Python class `Rectangle` with an `__init__()` method that takes `width` and `height` as parameters. Add methods `area()` and `perimeter()` that calculate and return the area and perimeter. Create an instance `room = Rectangle(10, 15)` and test both methods."

> **Tests to validate.**
> - `room.area() == 150` — area calculation is correct
> - `room.perimeter() == 50` — perimeter calculation is correct
> - `small = Rectangle(3, 4); small.area() == 12` — different instances have different results

---

## Concept 3 — Methods and behavior

A method is a function attached to a class. When you call a method, you are asking an instance to perform an action on itself.

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
    
    def deposit(self, amount):
        self.balance += amount
        print(f"Deposited {amount}. New balance: {self.balance}")
    
    def withdraw(self, amount):
        if self.balance >= amount:
            self.balance -= amount
            print(f"Withdrew {amount}. New balance: {self.balance}")
        else:
            print("Insufficient funds")

account = BankAccount("Alice", 1000)
account.deposit(200)     # Deposited 200. New balance: 1200
account.withdraw(300)    # Withdrew 300. New balance: 900
account.withdraw(1000)   # Insufficient funds
```

Each method has `self` as the first parameter. When you call `account.deposit(200)`, Python passes the instance (`account`) as `self` and `200` as `amount`. The method accesses and modifies the instance's attributes: `self.balance += amount`.

Methods can return values, just like functions:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
    
    def get_balance(self):
        return self.balance
    
    def deposit(self, amount):
        self.balance += amount
        return self.balance

account = BankAccount("Alice", 1000)
new_balance = account.deposit(200)
print(new_balance)  # 1200
```

### Instance methods vs. class methods (preview)

You will occasionally see a method decorated with `@classmethod`. This is a class method — a method that operates on the class, not on an instance. You don't need to understand this yet (it belongs in a more advanced chapter), but it is useful to know the name. Instance methods (which you are writing now) are far more common.

### The `__str__()` method — controlling how objects appear

When you print an object, Python looks for a special method called `__str__()`. This method tells Python how to convert the object to a string for printing.

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

buddy = Dog("Buddy", "Golden Retriever")
print(buddy)  # <__main__.Dog object at 0x7f8b8c0f0e50>
```

Without `__str__()`, you get a cryptic object representation showing the memory address. Add a `__str__()` method, and you control what appears:

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed
    
    def __str__(self):
        return f"{self.name} is a {self.breed}"

buddy = Dog("Buddy", "Golden Retriever")
print(buddy)  # Buddy is a Golden Retriever
```

Now when you print the object, the method is called automatically. This is magic method behavior: Python calls `__str__()` behind the scenes so you don't have to write `print(buddy.__str__())`.

### Trade-off: behavior in methods vs. validation

A method can do two things: access and modify the instance's attributes, or validate that the change is safe. Should you do both?

Consider a bank account's `withdraw()` method. You could write:

```python
def withdraw(self, amount):
    self.balance -= amount  # Simple
```

Or:

```python
def withdraw(self, amount):
    if amount <= 0:
        raise ValueError("Withdrawal must be positive")
    if self.balance < amount:
        raise ValueError("Insufficient funds")
    self.balance -= amount  # Safe
```

The second is safer and stronger. It prevents invalid withdrawals. The class enforces its own rules. This is part of encapsulation: the class guarantees that the account is always in a valid state. Code outside the class cannot bypass the rules by directly modifying `self.balance = balance - amount`. The method is the gatekeeper.

### Worked example — a student record class

You are building a grade-tracking system. A `Student` object needs to track the name, major, and GPA. You need a method to update the GPA when new grades come in.

```python
class Student:
    def __init__(self, name, major):
        self.name = name
        self.major = major
        self.gpa = 0.0
    
    def update_gpa(self, new_gpa):
        if 0.0 <= new_gpa <= 4.0:
            self.gpa = new_gpa
        else:
            print("GPA must be between 0.0 and 4.0")
    
    def __str__(self):
        return f"{self.name} ({self.major}): GPA {self.gpa:.2f}"

alice = Student("Alice", "Computer Science")
alice.update_gpa(3.8)
print(alice)  # Alice (Computer Science): GPA 3.80

alice.update_gpa(5.0)  # GPA must be between 0.0 and 4.0
```

> **Prompt for Claude.** "Write a Python class `Student` with attributes `name`, `major`, and `gpa` (initialized to 0.0). Include an `update_gpa()` method that validates the GPA is between 0.0 and 4.0 before updating. Add a `__str__()` method that returns a nicely formatted string showing the student's name, major, and GPA with two decimal places. Create an instance and test the methods."

> **Tests to validate.**
> - `alice = Student("Alice", "CS"); alice.gpa == 0.0` — initial GPA is correct
> - `alice.update_gpa(3.8); alice.gpa == 3.8` — valid update works
> - `str(alice) == "Alice (CS): GPA 3.80"` — string representation is formatted correctly
> - `alice.update_gpa(5.0)` does not change the GPA — invalid update is rejected

---

## Concept 4 — Bringing it together: a complete class

You are building a simple command-line budgeting app. The user can create a budget for a category (groceries, rent, entertainment) and track spending. Here is a complete `Budget` class:

```python
class Budget:
    """A budget for a spending category."""
    
    def __init__(self, category, limit):
        self.category = category
        self.limit = limit
        self.spent = 0.0
    
    def record_expense(self, amount):
        """Record an expense and warn if over budget."""
        if amount <= 0:
            print("Expense must be positive")
            return False
        
        self.spent += amount
        remaining = self.limit - self.spent
        
        if remaining < 0:
            print(f"OVER BUDGET by ${abs(remaining):.2f}")
        else:
            print(f"Expense recorded. ${remaining:.2f} remaining")
        
        return True
    
    def remaining(self):
        """Return the amount remaining in the budget."""
        return self.limit - self.spent
    
    def __str__(self):
        status = "OK" if self.remaining() >= 0 else "OVER"
        return f"{self.category}: ${self.spent:.2f} of ${self.limit:.2f} [{status}]"

groceries = Budget("Groceries", 400)
groceries.record_expense(120)  # Expense recorded. $280.00 remaining
groceries.record_expense(150)  # Expense recorded. $130.00 remaining
groceries.record_expense(200)  # OVER BUDGET by $20.00
print(groceries)  # Groceries: $470.00 of $400.00 [OVER]
```

This class demonstrates several concepts:
- `__init__()` sets up initial state (category, limit, spent at zero).
- Methods modify state (`record_expense`) and query it (`remaining`).
- Methods validate input (rejecting non-positive amounts).
- `__str__()` provides a readable representation.
- The class enforces the budget's rules; code outside cannot bypass validation.

> **Prompt for Claude.** "Write a Python class `Budget` with attributes `category`, `limit`, and `spent` (starting at 0.0). Include a method `record_expense(amount)` that adds to spent and prints the remaining budget. Include a method `remaining()` that returns the remaining amount. Add a `__str__()` method that shows category, spent, limit, and whether the budget is over. Create a budget for 'Groceries' with a limit of 400, record several expenses, and print the budget."

> **Tests to validate.**
> - `groceries = Budget("Groceries", 400); groceries.spent == 0.0` — initial state is correct
> - `groceries.record_expense(120); groceries.spent == 120.0` — expense is recorded
> - `groceries.remaining() == 280.0` — remaining calculation is correct after one expense
> - `"Groceries" in str(groceries)` — string representation includes the category name
> - `Budget("Rent", 1000); rent.record_expense(-50)` returns `False` — negative expenses are rejected

---

## Integration and synthesis

You now understand the structure of classes: a blueprint that bundles data (attributes) and behavior (methods) together. You know how to create instances, initialize them with `__init__()`, and call methods on them. You know that `self` refers to the instance a method is called on. You know that instance attributes are unique to each instance, while class attributes are shared.

Here are the key patterns to carry forward:

**Every class needs `__init__()`.** This is where you set up the initial state. The method is called automatically when you create an instance.

**`self` is always the first parameter in a method.** It is how the method knows which instance to operate on.

**Methods can access and modify attributes.** They can also return values, print output, or trigger other behaviors.

**Encapsulation is the point.** By grouping behavior with data, you can enforce rules (validation) and prevent bugs. A bank account's balance is not just a number; it is protected by the rules the class enforces.

**Magic methods customize behavior.** `__str__()` controls printing. In later chapters, you will see `__eq__()` (equality comparison), `__add__()` (addition operator), and others. They let you make your objects behave like built-in types.

---

## Graduated exercises

### Warm-up

1. **Define a simple class.** Write a class `Rectangle` with an `__init__()` method that takes width and height. Do not add any other methods yet.

2. **Create instances.** Create two instances of the `Rectangle` class with different dimensions. Print their width and height.

3. **Add a method.** Add an `area()` method to `Rectangle` that returns width × height. Call it on each instance.

4. **Trace through `self`.** Write a class `Counter` with `__init__()` that initializes `count = 0`. Add a method `increment()` that does `self.count += 1`. Create an instance, call `increment()` three times, and print the count. Draw a memory diagram showing the instance and the attribute.

### Application

5. **Build a bank account class.** Write a class `BankAccount` with attributes `owner` and `balance`. Include methods `deposit(amount)` and `withdraw(amount)`. The `withdraw()` method should prevent negative balances (print an error instead). Create two accounts and test both methods.

6. **Add `__str__()`.** Add a `__str__()` method to `BankAccount` that returns a string like `"Alice: $1200.50"`. Print the instances.

7. **Class vs. instance attributes.** Write a class `Car` with a class attribute `wheels = 4` (all cars have four wheels). Include instance attributes `make` and `model`. Create two cars and verify they both have four wheels.

8. **Validate on initialization.** Write a class `Person` with attributes `name` and `age`. In `__init__()`, check that age is non-negative. If not, set age to 0 and print a warning.

### Synthesis

9. **Build a complete `Student` class.** Write a class to represent a student. Attributes: `name`, `student_id`, and `grades` (a list of test scores). Methods:
   - `add_grade(score)` — add a score to the list
   - `average()` — return the average score
   - `passed()` — return `True` if average >= 60, `False` otherwise
   - `__str__()` — return `"[name] (ID [id]): [average]%"`
   Create a student, add several grades, and print the result.

10. **Design a `ShoppingCart` class.** Write a class with attributes `items` (a list of item names) and `total` (the price sum). Methods:
    - `add_item(name, price)` — add an item and update the total
    - `remove_item(name)` — remove an item (if it exists) and update the total
    - `checkout()` — return the total and reset (items = [], total = 0)
    - `__str__()` — return `"[count] items, total: $[total]"`
    Simulate a shopping session: add items, remove one, and check out.

---

## Claude Code

Here you will build a working `BankAccount` class in Claude Code, then write a test suite to verify it behaves correctly. This exercise reinforces instance attributes, methods, encapsulation, validation, and `__str__()`.

### Orientation

When you write a class, you are defining a contract: "Every instance of this class will have these attributes, and if you call these methods, they will do exactly this." A test suite is how you verify that contract. You write tests first to specify what the class should do, then write the class to pass those tests. This is called test-driven development.

In this exercise, you will:
1. Write a `BankAccount` class with attributes and methods.
2. Implement validation in `deposit()` and `withdraw()` so the class protects its own state.
3. Implement `__str__()` so the object prints meaningfully.
4. Write a test file with at least 8 test cases covering happy paths and error cases.
5. Run the tests using pytest to confirm the class works as specified.

### What to expect

You will create two files:
- `bank.py` — the `BankAccount` class
- `test_bank.py` — the test suite

The class definition is about 30 lines. The test suite is about 50 lines. You are not writing a big program; you are writing a small, correct one.

### Prompt for Claude Code

Create a new project folder. Inside, ask Claude Code:

> "I need a `BankAccount` class with the following spec:
>
> **Constructor:** `__init__(self, name, balance=0)`
> - Store `name` and `balance` as instance attributes.
> - Validate: if `balance` < 0, raise `ValueError("Balance cannot be negative")`
>
> **Methods:**
> - `deposit(self, amount)` — Add `amount` to balance. Validate: `amount` > 0, or raise `ValueError("Deposit must be positive")`. Return the new balance.
> - `withdraw(self, amount)` — Subtract `amount` from balance. Validate: `amount` > 0, or raise `ValueError("Withdrawal must be positive")`. Validate: `amount` <= balance, or raise `ValueError("Insufficient funds")`. Return the new balance.
> - `__str__(self)` — Return a string like `'Alice: $1234.56'` (name and balance formatted to 2 decimals).
>
> Write the class in `bank.py`.
>
> Then write `test_bank.py` with pytest. At least 8 test cases:
> 1. Create a new account; verify initial balance is correct.
> 2. Deposit positive amount; verify balance increases and return value is correct.
> 3. Withdraw positive amount; verify balance decreases and return value is correct.
> 4. Try to deposit zero; verify it raises `ValueError`.
> 5. Try to deposit negative amount; verify it raises `ValueError`.
> 6. Try to withdraw zero; verify it raises `ValueError`.
> 7. Try to withdraw more than balance; verify it raises `ValueError` and balance is unchanged.
> 8. Verify two accounts have independent balances (isolation test).
> 9. Bonus: Test `__str__()` output format.
>
> Run pytest and show me the results."

### What the tests should demonstrate

- **Encapsulation:** The class enforces its own rules. Invalid operations raise errors, not silent failures.
- **Instance isolation:** Two `BankAccount` instances do not interfere with each other.
- **Method return values:** Each method returns something the caller can use.
- **String representation:** `__str__()` makes debugging and display simple.

### Stretch

Once the tests pass:
1. Add a `get_balance()` method that returns the balance without modifying it. Add a test for it.
2. Add a class attribute `bank_name = "First Python Bank"` and access it from an instance. Add a test: `account.bank_name == "First Python Bank"`.
3. Create a second account, make several transactions, and print both accounts. Show that they are independent.
4. Write a docstring for each method in the class. Re-run pytest to confirm the tests still pass.

---

## Chapter summary

A class is a blueprint for creating objects. It bundles attributes (data) and methods (behavior) together. An instance is a single object created from that blueprint. `__init__()` is a magic method that runs automatically when an instance is created; it initializes the instance's attributes. The `self` parameter in a method refers to the instance the method is called on. Instance attributes are unique to each instance; class attributes are shared. Methods access and modify attributes, and can return values. Magic methods like `__str__()` customize how objects behave. Encapsulation — controlling access to data through methods — prevents bugs and enforces rules. Classes scale: the same design can manage one account or a million.

---

## Connections forward

**Chapter 12 (Recursion)** uses classes as containers for recursive problems. A tree data structure, for instance, is often represented as a class with a left child and a right child — both instances of the same class.

**Chapter 13 (Inheritance)** shows how classes can extend other classes. A `SavingsAccount` can inherit from `BankAccount`, reusing the `deposit()` and `withdraw()` methods and adding new ones like `add_interest()`.

**Chapter 14 (Files)** shows how to save and load instances to disk. A program can create a `Student` instance, save it to a file, close the program, and later load it back.

**Chapter 15 (Data Science)** introduces libraries like pandas, which use classes heavily. A DataFrame is a class representing a table of data; the methods operate on that table.

Magic methods like `__add__()`, `__eq__()`, and others allow your classes to work with Python's operators — turning a custom class into something that feels as natural as a built-in type. That is the power of classes: they are not a separate feature bolted onto Python. They are the foundation. Every value in Python is an object of some class. Learning to define your own classes is learning to think like Python itself.

---

## Tags

classes, object-oriented-programming, `__init__`, `self`, instance-attributes, methods, encapsulation, magic-methods, `__str__`, BankAccount

---

## Extended worked example 1: The BankAccount class — deposit and withdraw

You are writing the core of a banking system. You need a `BankAccount` class that handles deposits and withdrawals safely. The class must:
- Track the owner's name and balance.
- Prevent withdrawals that exceed the balance.
- Return the new balance after each transaction.

Here is the complete implementation:

```python
class BankAccount:
    """A simple bank account with deposit and withdraw methods."""
    
    def __init__(self, owner, initial_balance):
        """Initialize the account with an owner and starting balance."""
        if initial_balance < 0:
            print("Warning: initial balance cannot be negative. Setting to 0.")
            self.initial_balance = 0
        else:
            self.initial_balance = initial_balance
        
        self.owner = owner
        self.balance = self.initial_balance
    
    def deposit(self, amount):
        """Add money to the account. Return the new balance."""
        if amount <= 0:
            print("Error: deposit amount must be positive")
            return self.balance
        
        self.balance += amount
        print(f"Deposited ${amount:.2f}. New balance: ${self.balance:.2f}")
        return self.balance
    
    def withdraw(self, amount):
        """Remove money from the account if funds are available."""
        if amount <= 0:
            print("Error: withdrawal amount must be positive")
            return self.balance
        
        if amount > self.balance:
            print(f"Error: insufficient funds. Balance is ${self.balance:.2f}")
            return self.balance
        
        self.balance -= amount
        print(f"Withdrew ${amount:.2f}. New balance: ${self.balance:.2f}")
        return self.balance
    
    def __str__(self):
        """Return a formatted account summary."""
        return f"{self.owner}: ${self.balance:.2f}"

# Create an account and test
alice_account = BankAccount("Alice", 1000)
print(alice_account)  # Alice: $1000.00

alice_account.deposit(200)     # Deposited $200.00. New balance: $1200.00
alice_account.withdraw(150)    # Withdrew $150.00. New balance: $1050.00
alice_account.withdraw(2000)   # Error: insufficient funds. Balance is $1050.00

print(alice_account)  # Alice: $1050.00
```

> **Prompt for Claude.** "Write a Python class `BankAccount` with attributes `owner` and `balance`. The `__init__()` method should validate that the initial balance is not negative; if it is, set it to 0 and print a warning. Include a `deposit(amount)` method that validates the amount is positive, adds it to balance, prints a confirmation, and returns the new balance. Include a `withdraw(amount)` method that checks for positive amount, sufficient funds, then subtracts from balance and prints a confirmation. Add a `__str__()` method that returns `'[owner]: $[balance]'` with balance formatted to two decimal places. Create an account, make several deposits and withdrawals (including one that fails), and print the account."

> **Tests to validate.**
> - `alice = BankAccount("Alice", 1000); alice.balance == 1000` — initial balance is correct
> - `alice.deposit(200) == 1200` — deposit returns the correct new balance
> - `alice.withdraw(150) == 1050` — withdraw returns the correct new balance
> - `alice.withdraw(2000) == 1050` — insufficient-funds check prevents withdrawal and returns unchanged balance
> - `str(alice) == "Alice: $1050.00"` — string representation formats correctly
> - `BankAccount("Bob", -50); bob.balance == 0` — negative initial balance is converted to 0

---

## Extended worked example 2: The VendingMachine class

You are designing a vending machine. The machine needs to track:
- How many units of product it currently holds.
- The maximum capacity.
- Whether it can dispense (has stock).

Here is the class:

```python
class VendingMachine:
    """A vending machine that tracks stock and can dispense items."""
    
    def __init__(self, product_name, max_capacity):
        """Initialize a vending machine with a product and capacity."""
        self.product_name = product_name
        self.max_capacity = max_capacity
        self.current_stock = 0
    
    def refill(self):
        """Fill the machine to maximum capacity."""
        self.current_stock = self.max_capacity
        print(f"Refilled {self.product_name}. Stock: {self.current_stock}")
    
    def dispense(self, quantity):
        """Dispense items if stock is available."""
        if quantity <= 0:
            print("Error: quantity must be positive")
            return False
        
        if quantity > self.current_stock:
            print(f"Error: not enough stock. Only {self.current_stock} available.")
            return False
        
        self.current_stock -= quantity
        print(f"Dispensed {quantity} {self.product_name}. Remaining: {self.current_stock}")
        return True
    
    def status(self):
        """Return the current stock level."""
        return self.current_stock
    
    def __str__(self):
        """Return a status string."""
        percent = (self.current_stock / self.max_capacity) * 100 if self.max_capacity > 0 else 0
        return f"{self.product_name}: {self.current_stock}/{self.max_capacity} ({percent:.0f}%)"

# Create machines and test
soda_machine = VendingMachine("Soda", 50)
soda_machine.refill()              # Refilled Soda. Stock: 50
soda_machine.dispense(10)          # Dispensed 10 Soda. Remaining: 40
soda_machine.dispense(5)           # Dispensed 5 Soda. Remaining: 35
print(soda_machine)                # Soda: 35/50 (70%)

soda_machine.dispense(100)         # Error: not enough stock. Only 35 available.

snack_machine = VendingMachine("Chips", 30)
print(snack_machine)               # Chips: 0/30 (0%)
```

> **Prompt for Claude.** "Write a Python class `VendingMachine` with attributes `product_name`, `max_capacity`, and `current_stock` (starting at 0). Include a `refill()` method that sets stock to max and prints a message. Include a `dispense(quantity)` method that checks if quantity is positive and stock is sufficient, then decreases stock and prints confirmation or error. Add a `status()` method that returns current stock. Add a `__str__()` method that returns '[product]: [stock]/[max] ([percent]%)' with percent calculated and rounded. Create two machines, refill one, dispense items from it, and print both."

> **Tests to validate.**
> - `soda = VendingMachine("Soda", 50); soda.current_stock == 0` — initial stock is 0
> - `soda.refill(); soda.current_stock == 50` — refill sets stock to max
> - `soda.dispense(10)` returns `True` and `soda.current_stock == 40` — dispense works
> - `soda.dispense(100)` returns `False` and stock unchanged — insufficient stock is handled
> - `soda.dispense(-5)` returns `False` — negative quantities are rejected
> - `str(soda) == "Soda: 40/50 (80%)"` — string representation formats with percentage

---

## Extended worked example 3: Debugging common class errors

Here are three common mistakes beginners make with classes, and how to fix them.

### Error 1: Forgetting `self` in a method

**Mistake:**

```python
class Dog:
    def __init__(self, name):
        name = name  # WRONG — sets a local variable, not an attribute

buddy = Dog("Buddy")
print(buddy.name)  # AttributeError: 'Dog' object has no attribute 'name'
```

The method sets a local variable `name` instead of storing it on the instance. When you try to access `buddy.name`, Python looks for an attribute and does not find one.

**Fix:**

```python
class Dog:
    def __init__(self, name):
        self.name = name  # CORRECT — stores on the instance

buddy = Dog("Buddy")
print(buddy.name)  # Buddy
```

Always use `self.attribute_name` to store data on an instance.

### Error 2: Forgetting `self` as the first parameter

**Mistake:**

```python
class Counter:
    def __init__(self, count):
        self.count = count
    
    def increment():  # WRONG — missing 'self'
        self.count += 1

counter = Counter(0)
counter.increment()  # TypeError: increment() takes 0 positional arguments but 1 was given
```

Python is telling you: "I tried to call `increment()`, but it does not accept the instance as an argument." The method signature is wrong.

**Fix:**

```python
class Counter:
    def __init__(self, count):
        self.count = count
    
    def increment(self):  # CORRECT — 'self' is the first parameter
        self.count += 1

counter = Counter(0)
counter.increment()
print(counter.count)  # 1
```

Every instance method must have `self` as the first parameter.

### Error 3: Mutable default arguments create shared state

**Mistake:**

```python
class TodoList:
    def __init__(self, tasks=[]):  # WRONG — default list is shared
        self.tasks = tasks

list1 = TodoList()
list1.tasks.append("Buy milk")

list2 = TodoList()
print(list2.tasks)  # ['Buy milk'] — WRONG!
```

The default `[]` is created once, when the class is defined. Both instances share it.

**Fix:**

```python
class TodoList:
    def __init__(self, tasks=None):  # CORRECT — use None
        self.tasks = tasks if tasks is not None else []

list1 = TodoList()
list1.tasks.append("Buy milk")

list2 = TodoList()
print(list2.tasks)  # [] — correct
```

Always use `None` as the default for mutable arguments, then create a new mutable object inside the method.

---

## Practical debugging exercise

Here is a class with four intentional bugs. Find and fix each one:

```python
class Book:
    """A book in a library."""
    
    def __init__(title, pages):  # BUG 1: Missing 'self'
        self.title = title
        self.pages = pages
        self.borrowed_by = []  # BUG 2: Mutable default (used elsewhere)
    
    def borrow(self, person):
        self.borrowed_by.append(person)  # Should be okay if bug 2 is fixed
    
    def __str__(self):
        return f"{title}: {pages} pages"  # BUG 3: Should be 'self.title', 'self.pages'

book1 = Book("Python 101", 350)  # This line fails at BUG 1
```

**Bugs:**
1. Line 3: `def __init__(title, pages):` should be `def __init__(self, title, pages):`
2. The `__init__()` method should not initialize mutable defaults in attributes if they are supposed to be instance-specific. Here, `borrowed_by = []` in the signature would be a bug, but it's created inside, so it's actually okay. (This might be a red herring, depending on context.)
3. Line 10: `return f"{title}: {pages} pages"` should be `return f"{self.title}: {self.pages} pages"`

**Corrected code:**

```python
class Book:
    """A book in a library."""
    
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages
        self.borrowed_by = []
    
    def borrow(self, person):
        self.borrowed_by.append(person)
    
    def __str__(self):
        return f"{self.title}: {self.pages} pages"

book1 = Book("Python 101", 350)
print(book1)  # Python 101: 350 pages
book1.borrow("Alice")
print(book1.borrowed_by)  # ['Alice']
```

---

## Scaling example: One machine to a thousand

To see why classes matter, consider this: the `VendingMachine` class you wrote can represent a single machine, or a thousand machines, or a million. Here is how you might scale:

```python
# Create one machine
soda_machine = VendingMachine("Soda", 50)

# Scale to a fleet
vending_fleet = []
for location in ["Downtown", "Airport", "Mall"]:
    machine = VendingMachine("Soda", 50)
    vending_fleet.append(machine)

# Operate on the entire fleet
for i, machine in enumerate(vending_fleet):
    machine.refill()
    machine.dispense(10)
    print(f"Location {i}: {machine}")

# Query the fleet
total_stock = sum(m.status() for m in vending_fleet)
print(f"Total stock across all machines: {total_stock}")
```

The class design does not change whether you manage one machine or a million. You write the logic once, then instantiate and manage as many as you need. This is the power of classes: *you code the blueprint, not the fleet*.

---

## LLM Exercise — Chapter 11: Classes (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** Player, Enemy, and Item classes — the OOP refactor.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 11 of my Text Adventure project. Chapter 11 covered:
class; __init__; attributes; methods; instances; encapsulation;
self.

Refactor the game into classes. The Ch 6 globals issue gets
properly resolved here.

1. **Player class.**
   ```python
   class Player:
       def __init__(self, name, max_health=100, gold=50):
           self.name = name
           self.health = max_health
           self.max_health = max_health
           self.gold = gold
           self.experience = 0
           self.level = 1
           self.attack_power = 10
           self.defense = 5
           self.inventory = {}
           self.current_room = "tavern"

       def take_damage(self, amount):
           ...

       def heal(self, amount):
           ...

       def add_to_inventory(self, item):
           ...

       def remove_from_inventory(self, item):
           ...

       def has_item(self, item):
           ...

       def print_stats(self):
           ...

       def __str__(self):
           return f"{self.name} (HP {self.health}/{self.max_health},
               Gold {self.gold})"
   ```

2. **Enemy class.**
   ```python
   class Enemy:
       def __init__(self, name, health, attack_power, defense, loot=None):
           ...

       def take_damage(self, amount):
           ...

       def attack(self, target):
           # target is a Player instance
           damage = self.attack_power - target.defense
           damage = max(damage, 1)  # always at least 1
           target.take_damage(damage)
           return damage
   ```

3. **Item class.**
   ```python
   class Item:
       def __init__(self, name, description, value=0):
           ...

       def use(self, player):
           # Override in subclasses (Ch 13 inheritance).
           print(f"You can't use the {self.name} that way.")
   ```

4. **The refactor.** Replace globals throughout:
   - `player_health -= damage` becomes `player.take_damage(damage)`.
   - `player_inventory.append(item)` becomes
     `player.add_to_inventory(item)`.
   - Combat receives Player and Enemy instances.

5. **Encapsulation discipline.** The Player class doesn't expose
   raw health for direct modification — go through methods. Hide
   internal state.

In addition to the code, produce the prompt + tests. Tests now
benefit hugely from OOP:
- `p = Player("Alice", max_health=100); p.take_damage(30);
  assert p.health == 70`.
- `e = Enemy("goblin", 50, 8, 2); damage = e.attack(p); assert
  damage == e.attack_power - p.defense or 1`.
- Combat: simulate a fight; verify game-ending conditions.

End with: name one Player method you suspect should be private
(prefix with `_`). Why?
```

---

**What this produces:** A proper OOP refactor with Player, Enemy, Item classes. This is the project's biggest single architectural improvement.

**Connection to previous chapters:** Ch 6's function design + Ch 10's dict structures get encapsulated into proper classes.

**Preview of next chapter:** Chapter 12 covers recursion. You'll use it for tree-structured features — maybe a recursive description of nested rooms ("you see a chest; inside the chest is another chest..."), recursive search through inventory containers, or a dialogue tree for NPCs.


---

##  AI Wayback Machine
**Kristen Nygaard** was co-designed Simula in the 1960s — the first object-oriented language and the ancestor of every modern class system.

**Run this:**

```
Who is Kristen Nygaard, and how does their work connect to classes we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Kristen Nygaard"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Kristen Nygaard's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Kristen Nygaard's framework."

What changes? What gets better? What gets worse?
