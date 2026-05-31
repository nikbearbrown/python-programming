# Chapter 11 — Classes
*One blueprint. A thousand machines. The same logic.*

You are standing in front of a vending machine on a Tuesday afternoon. Inside it are dozens of different products — soda cans, snack bags, energy bars — each with its own price, quantity, and position in the grid. The machine's job is simple: you choose a slot, insert money, it dispenses. Now imagine you own a thousand machines in a thousand locations. Each machine has different products, different prices, different stock levels. But they all follow the same blueprint. Same buttons, same payment system, same internal logic. Different inventory, same design.

That is what a class is.

Before classes, you might model a bank account as a dictionary:

```python
account1 = {"owner": "Alice", "balance": 1000}
account2 = {"owner": "Bob", "balance": 2500}

def deposit(account, amount):
    account["balance"] += amount

def withdraw(account, amount):
    account["balance"] -= amount
```

This works — until it doesn't. The program grows. You add `validate_withdrawal()`, `apply_interest()`, `freeze_account()`. Each function takes an account dictionary as its first argument. The data and the behavior are separate. The dictionary doesn't know it's a bank account. The functions don't know that they only make sense for account dictionaries. You, the programmer, hold all of that in your head.

A class bundles data and behavior together. The dictionary knows what it is. The method knows what it operates on. The same design scales from two accounts to two million without changing the code.

---

## The blueprint and the instance

A *class* defines a template: what data each object carries (its **attributes**), and what each object can do (its **methods**). An *instance* is one concrete object built from that template — a single vending machine, a single bank account, a single student.

Here is the bank account rewritten as a class:

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
print(account1.balance)    # 1100
print(account2.balance)    # 2500 — unaffected
```

The class definition comes first. Then you create instances. Each instance has its own copy of the attributes. When you call `account1.deposit(100)`, the method operates on `account1`'s data, not `account2`'s. The instance is the target; the method is the action.

---

## `__init__` and `self`

When you write `BankAccount("Alice", 1000)`, Python creates a new object and immediately calls a special method: `__init__`. The double underscores — *dunder*, short for double-underscore — signal that Python calls this method automatically at the moment of creation. It is the initializer: its job is to set the instance's starting state.

```python
class Cat:
    def __init__(self, name, age):
        self.name = name
        self.age = age

mittens = Cat("Mittens", 3)
shadow = Cat("Shadow", 5)

print(mittens.name)    # Mittens
print(shadow.name)     # Shadow
```

The `self` parameter is the piece that surprises people. When you write `mittens = Cat("Mittens", 3)`, Python creates the instance and passes it as `self` to `__init__`. The line `self.name = name` says: "On this specific instance, create an attribute called `name` and give it this value." Each instance gets its own `name`, its own `age`. When you later write `mittens.name`, Python finds the attribute stored on that instance.

`self` is not magic. It is just the first parameter of every instance method, and it is always the instance the method is called on. The name `self` is a convention — technically you could name it anything, but everyone uses `self`, and you should too.

<!-- → [INFOGRAPHIC: Diagram showing two Cat instances in memory. On the left: the class blueprint with slots for `name` and `age`. On the right: two separate boxes labeled `mittens` and `shadow`, each with their own filled `name` and `age` values. Arrows from class to instances show that the blueprint generates independent objects.] -->

---

## Instance attributes and class attributes

The attributes set in `__init__` — `self.name`, `self.age` — are *instance attributes*. Each instance has its own copy. Change `mittens.name` and `shadow.name` is unaffected.

There is also a second kind: *class attributes*, defined at the class level, outside any method. They are shared across all instances:

```python
class Dog:
    species = "Canis familiaris"    # Class attribute — shared
    
    def __init__(self, name, breed):
        self.name = name            # Instance attribute — unique
        self.breed = breed

buddy = Dog("Buddy", "Golden Retriever")
max_dog = Dog("Max", "Labrador")

print(buddy.species)               # Canis familiaris
print(max_dog.species)             # Canis familiaris
print(Dog.species)                 # Canis familiaris — same value
```

Class attributes are useful for constants that belong to the type, not to any one object: a tax rate, a version number, a physical constant. Instance attributes are for the data that varies from object to object.

---

## Methods: what the object can do

Methods are functions defined inside a class. They always take `self` as their first parameter — that's how they know which instance they're operating on.

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
    
    def deposit(self, amount):
        self.balance += amount
        return self.balance
    
    def withdraw(self, amount):
        if self.balance >= amount:
            self.balance -= amount
            return self.balance
        else:
            print("Insufficient funds")
            return self.balance

account = BankAccount("Alice", 1000)
account.deposit(200)       # balance is now 1200
account.withdraw(300)      # balance is now 900
account.withdraw(1000)     # prints "Insufficient funds", balance unchanged
```

The `withdraw` method checks the balance before subtracting. This is *encapsulation* — from the Latin *capsula*, a small container — the class enforcing its own rules. Code outside the class cannot accidentally subtract from the balance by writing `account.balance -= 5000`. The only way to change the balance is through the methods, which validate the operation first.

This is not just organization. It is a load-bearing design decision. When a bug corrupts the balance, you know exactly where to look: `deposit()` or `withdraw()`. The class constrains the problem space.

---

## `__str__`: controlling how objects appear

By default, printing an object gives you something unhelpful:

```python
print(account)    # <__main__.BankAccount object at 0x7f8b...>
```

Add a `__str__` method and Python uses it whenever the object is converted to a string:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
    
    def __str__(self):
        return f"{self.owner}: ${self.balance:.2f}"

account = BankAccount("Alice", 1000)
print(account)    # Alice: $1000.00
```

`__str__` is another magic method — Python calls it automatically when you print the object or pass it to `str()`. You define what it returns; Python decides when to call it.

<!-- → [TABLE: Magic methods summary — three rows: `__init__` / called when? / instance is created / typical use: set initial attribute values; `__str__` / called when? / object is printed or passed to str() / typical use: return human-readable description; `__len__` (preview) / called when? / len() is called on the object / typical use: return count of items.] -->

---

## The mutable default argument trap

One mistake appears consistently and silently. Suppose you initialize a list attribute as a default argument:

```python
class TodoList:
    def __init__(self, tasks=[]):    # WRONG
        self.tasks = tasks

list1 = TodoList()
list1.tasks.append("Buy milk")

list2 = TodoList()
print(list2.tasks)    # ['Buy milk'] — should be empty!
```

The default `[]` is created once, when the class definition is loaded. Every instance that uses the default shares the same list object. When you modify `list1.tasks`, you modify the shared list that `list2.tasks` also points to.

The fix: use `None` as the default, then create a fresh list inside `__init__`:

```python
class TodoList:
    def __init__(self, tasks=None):    # CORRECT
        self.tasks = tasks if tasks is not None else []
```

Now each instance gets its own empty list. The rule: never use a mutable object — a list, dict, or set — as a default argument in `__init__`. Use `None` and create the mutable object inside the method.

---

## A complete example

Here is a class that uses everything so far: attributes, methods, validation, `__str__`:

```python
class Budget:
    """A budget for a spending category."""
    
    def __init__(self, category, limit):
        self.category = category
        self.limit = limit
        self.spent = 0.0
    
    def record_expense(self, amount):
        if amount <= 0:
            print("Expense must be positive.")
            return False
        self.spent += amount
        remaining = self.limit - self.spent
        if remaining < 0:
            print(f"OVER BUDGET by ${abs(remaining):.2f}")
        else:
            print(f"${remaining:.2f} remaining.")
        return True
    
    def remaining(self):
        return self.limit - self.spent
    
    def __str__(self):
        status = "OK" if self.remaining() >= 0 else "OVER"
        return f"{self.category}: ${self.spent:.2f} of ${self.limit:.2f} [{status}]"

groceries = Budget("Groceries", 400)
groceries.record_expense(120)    # $280.00 remaining.
groceries.record_expense(150)    # $130.00 remaining.
groceries.record_expense(200)    # OVER BUDGET by $20.00
print(groceries)                 # Groceries: $470.00 of $400.00 [OVER]
```

Walk through it: `__init__` sets category, limit, and initial spent at zero. `record_expense` validates the amount, adds to spent, computes the remaining, and reports. `remaining` is a query — it returns a value without modifying state. `__str__` assembles a readable summary. The class enforces the rules; anything outside the class that wants to interact with the budget goes through these methods.

---

## Scaling from one to a thousand

The payoff of a class becomes visible when you need many instances:

```python
machines = []
for location in ["Downtown", "Airport", "Mall", "Hospital"]:
    m = VendingMachine("Soda", 50)
    m.refill()
    machines.append(m)

total_stock = sum(m.status() for m in machines)
print(f"Total stock across {len(machines)} locations: {total_stock}")
```

The class definition doesn't change. The logic you wrote once runs for every machine in the fleet. This is what *code the blueprint, not the fleet* means: you write the design once, and instantiation handles the rest.

---

## Three bugs beginners always hit

**Forgetting `self` in the method signature:**

```python
class Counter:
    def __init__(self, count):
        self.count = count
    
    def increment():              # WRONG — missing self
        self.count += 1

c = Counter(0)
c.increment()    # TypeError: increment() takes 0 positional arguments but 1 was given
```

Python passes the instance as the first argument automatically. If the method doesn't have a parameter to receive it, Python complains. Fix: always write `self` first.

**Forgetting `self.` when setting an attribute:**

```python
class Dog:
    def __init__(self, name):
        name = name    # WRONG — sets a local variable, not an attribute

buddy = Dog("Buddy")
print(buddy.name)    # AttributeError: 'Dog' object has no attribute 'name'
```

Without `self.name = name`, nothing is stored on the instance. Fix: `self.name = name`.

**Bare attribute access without `self` inside a method:**

```python
class Dog:
    def __init__(self, name):
        self.name = name
    
    def greet(self):
        print(f"Hi, I'm {name}")    # WRONG — name is not defined in this scope

buddy = Dog("Buddy")
buddy.greet()    # NameError: name 'name' is not defined
```

Inside a method, instance attributes are only reachable through `self`. Fix: `self.name`.

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

## AI Wayback Machine

**Kristen Nygaard** co-designed Simula in the 1960s — the first object-oriented language and the ancestor of every modern class system.

**Run this:**

```
Who is Kristen Nygaard, and how does their work connect to classes we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Kristen Nygaard"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Kristen Nygaard's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Kristen Nygaard's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. Write a class `Rectangle` with an `__init__` method that takes `width` and `height`. Create two instances with different dimensions and print their attributes.

2. Add an `area()` method to `Rectangle` that returns width × height. Call it on both instances and verify they return different values.

3. Write a class `Counter` with `__init__` that sets `count = 0`, and an `increment()` method that adds 1. Create an instance, call `increment()` three times, and print the count.

**Application**

4. Write a class `BankAccount` with attributes `owner` and `balance`. Add `deposit(amount)` and `withdraw(amount)` methods. The `withdraw` method should refuse to overdraw (print an error and leave the balance unchanged). Test both methods.

5. Add a `__str__` method to `BankAccount` that returns a string like `"Alice: $1200.50"`. Print two different accounts and verify the output.

6. Write a class `Car` with a class attribute `wheels = 4` and instance attributes `make` and `model`. Create two cars and confirm they both have four wheels via `car.wheels`. What happens if you change `Car.wheels`?

**Synthesis**

7. Write a class `Student` with attributes `name`, `student_id`, and a list of `grades`. Add an `add_grade(score)` method, an `average()` method that returns the mean, a `passed()` method that returns `True` if the average is 60 or above, and a `__str__` method. Create a student, add several grades, and print the result.

8. Write a class `ShoppingCart` with a list of items and a running total. Add `add_item(name, price)` and `remove_item(name, price)`. Add a `checkout()` method that returns the total and resets the cart. Simulate a shopping session: add three items, remove one, then check out.

**Challenge**

9. The following class has three bugs. Find and fix each one without running the code first — trace through it manually:

```python
class Book:
    def __init__(title, pages):
        self.title = title
        self.pages = pages
    
    def summary(self):
        return f"{title} has {pages} pages"

b = Book("Python 101", 350)
print(b.summary())
```

Then verify your fixes by running the corrected version.
