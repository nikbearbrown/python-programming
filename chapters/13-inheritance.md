# Chapter 13 — Inheritance


## TL;DR

- You walk into a hardware store and find two saws hanging side by side on the wall.
- You will practice Distinguish between is-a relationships (inheritance) and has-a relationships (composition); Write a subclass that inherits attributes and methods from a superclass; Override inherited methods in a subclass to customize behavior.
- The chapter moves through Cold open, Learning objectives, Prerequisites, Why this chapter matters, and related ideas.

## Cold open

You walk into a hardware store and find two saws hanging side by side on the wall. One is a hand saw — a wooden handle, a long blade, teeth along the edge. The other is a circular saw — a motor, a spinning blade, wheels for rolling. They are different machines. But if you've used a hand saw, you know something important already: how to position the material, how to apply pressure, which way to push. You don't start from zero with the circular saw. You inherit knowledge from the simpler tool.

Classes work the same way. You have written an `Animal` class — attributes like `name` and `species`, methods like `speak()` and `move()`. Now you need a `Dog` class. A dog is a type of animal. It has all the attributes and methods an animal has. But a dog has something extra: a `breed` attribute, and a `bark()` method that sounds different from a generic animal sound. Rather than rewrite the entire `Animal` class inside `Dog`, you build `Dog` on top of `Animal`. You inherit the structure, specialize where needed.

This is inheritance: the ability to build a new class from an existing one, borrowing its machinery and adding your own. It reduces redundancy, clarifies structure, and makes large programs possible.

### Learning objectives

By the end of this chapter you will be able to:

- **Distinguish** between is-a relationships (inheritance) and has-a relationships (composition).
- **Write** a subclass that inherits attributes and methods from a superclass.
- **Override** inherited methods in a subclass to customize behavior.
- **Use** `super()` to call a superclass method from inside a subclass.
- **Apply** polymorphism — using the same method name across different classes.
- **Build** hierarchical inheritance trees where one subclass becomes the parent of another.

### Prerequisites

Comfortable with class definitions, `__init__()`, and method syntax from Chapter 11. Understanding of when one concept is a type of another (is-a) versus when one concept contains another (has-a).

### Why this chapter matters

Inheritance is how real programs organize complexity. A graphics engine has a `Shape` base class; `Circle`, `Rectangle`, and `Polygon` all inherit from it. A web framework has a `User` class; `AdminUser` and `RegularUser` inherit specialized behavior. A banking system has an `Account` class; `CheckingAccount`, `SavingsAccount`, and `MoneyMarketAccount` inherit and diverge. By Chapter 15 you'll be working with classes from libraries like pandas that were designed with inheritance in mind. Understanding how it works — and when to use it versus other approaches — is how you read and extend professional code.

---

## Concept 1 — Is-a relationships and simple inheritance

A relationship between two concepts can be one of two types, and choosing between them shapes your entire program.

A **has-a relationship** is composition. A `Car` has an `Engine`. A `Person` has a `BankAccount`. A `House` has a `Door`. The engine is not a type of car; it is a part of a car. You model this by giving the `Car` class an attribute whose type is `Engine`. The attribute holds an instance of another class.

An **is-a relationship** is inheritance. A `Labrador` is a type of `Dog`. A `Dog` is a type of `Animal`. A `CheckingAccount` is a type of `Account`. The subclass — the one that *is* a type of the other — inherits the structure of the superclass (also called the *base class* or *parent class*) and can add its own attributes and methods.

Inheritance, from Latin *hereditas* meaning what is inherited — a child class inherits structure from its parent without needing to redefine everything.

The distinction matters because it changes what you write. Here's a `Person` using composition (has-a):

```python
class BankAccount:
    def __init__(self, account_number, balance):
        self.account_number = account_number
        self.balance = balance

class Person:
    def __init__(self, name, account):
        self.name = name
        self.account = account  # account is an instance of BankAccount
    
    def deposit(self, amount):
        self.account.balance += amount  # Delegate to the account
```

Notice that `Person` doesn't inherit from `BankAccount`. Instead, it *contains* one. You call methods on the account through the person object. This is composition.

Here's a `CheckingAccount` using inheritance (is-a):

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def deposit(self, amount):
        self.balance += amount

class CheckingAccount(BankAccount):
    def __init__(self, balance, monthly_fee):
        super().__init__(balance)
        self.monthly_fee = monthly_fee
```

`CheckingAccount` inherits from `BankAccount`. It automatically has the `deposit()` method and the `balance` attribute. It adds its own `monthly_fee`. This is inheritance.

Which should you use? The heuristic: use is-a (inheritance) when the subclass truly is a specialized type of the parent. Use has-a (composition) when one concept contains another as a part. A car has an engine; a car is not an engine. A checking account is an account; a checking account is not a person.

Here is a simple superclass, `Animal`:

```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species
    
    def speak(self):
        print(f"{self.name} makes a sound")
    
    def move(self):
        print(f"{self.name} is moving")
```

A subclass inherits from a superclass by naming it in parentheses:

```python
class Dog(Animal):
    pass
```

That's it. `Dog` now has every attribute and method that `Animal` has. You can create a dog and call animal methods:

```python
my_dog = Dog("Buddy", "Canis familiaris")
my_dog.speak()  # Prints: Buddy makes a sound
my_dog.move()   # Prints: Buddy is moving
```

The `pass` keyword means "do nothing." The subclass doesn't have to define anything new — it just inherits everything from `Animal`. But that's rarely what you want. Usually the subclass extends the superclass with specialized behavior.

### Worked example — a simple inheritance hierarchy with an error

First, here's what not to do:

```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species
    
    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

my_dog = Dog("Luna", "Golden Retriever")
print(f"{my_dog.name} is a {my_dog.breed}")
print(f"Species: {my_dog.species}")  # ERROR! No such attribute.
```

This fails because when you define `Dog.__init__()`, you are overriding the animal initializer. The dog has `name` and `breed`, but not `species` — the attribute from the parent class is gone. The child's `__init__()` does not automatically call the parent's. You have to call it explicitly, using a special tool called `super()`.

Here's the correct version:

```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species
    
    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Canis familiaris")  # Initialize parent
        self.breed = breed

my_dog = Dog("Luna", "Golden Retriever")
print(f"{my_dog.name} is a {my_dog.breed}")
print(f"Species: {my_dog.species}")  # Now it works!
```

The `super().__init__()` call on the first line ensures the parent's `__init__()` runs, setting `name` and `species`. Then `breed` is set in the subclass. Result: the dog has all three attributes.

### Common misconceptions

**Subclass attributes don't automatically inherit default values from the parent.** If `Animal.__init__()` sets `self.species = "Unknown"` and you override `__init__()` in the subclass without calling the parent's `__init__()`, the subclass instances won't have a `species` attribute unless you set it yourself. Inheritance gives you access to methods; `__init__()` is a method, not a magical transfer of attributes.

**A subclass is not a weaker version of the parent.** It is a specialized version — more specific, not less capable. A `Dog` is not a stripped-down animal; it is an animal plus dog-specific behavior. If the program asks for an `Animal`, a `Dog` will work (because a dog is an animal), but the reverse isn't true.

**Order matters: call `super().__init__()` early.** Some programmers call `super().__init__()` at the end of the subclass initializer, after setting all their own attributes. This works, but calling it at the start (after docstring, before your own attributes) is clearer — the parent is set up first, then you build on top.

**Don't confuse the inheritance arrow direction.** When you write `class Dog(Animal):`, you read it as "Dog inherits from Animal," not the other way around. `Animal` is the parent; `Dog` is the child. A child inherits from a parent, never the reverse.

---

## Concept 2 — Overriding methods and `super()`

When you want a subclass to have different behavior than its parent, you *override* a method: define a method with the same name in the subclass. When called on a subclass instance, Python uses the subclass version, not the parent version.

Here's an `Animal` class with a `speak()` method:

```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        print(f"{self.name} makes a generic animal sound")
```

A `Dog` subclass overrides `speak()`:

```python
class Dog(Animal):
    def speak(self):
        print(f"{self.name} barks: Woof!")

my_dog = Dog("Buddy")
my_dog.speak()  # Prints: Buddy barks: Woof!
```

Often you want the subclass to do what the parent does, plus something extra. This is where `super()` comes in.

`super()` — from Latin *super* meaning above — is a special function that reaches *above* to the parent class's method. In `__init__()`, you use it to call the parent's initializer so you don't have to rewrite initialization logic:

```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Canis familiaris")  # Call parent's __init__()
        self.breed = breed

my_dog = Dog("Luna", "Labrador")
print(my_dog.name)      # Luna
print(my_dog.species)   # Canis familiaris
print(my_dog.breed)     # Labrador
```

The flow: `Dog.__init__()` calls `super().__init__(name, "Canis familiaris")`. That line executes `Animal.__init__()` with those arguments, setting `self.name` and `self.species`. Then control returns to `Dog.__init__()`, which sets `self.breed`. By the time the initializer finishes, the dog has all three attributes.

You can override methods other than `__init__()` and use `super()` to call the parent version:

```python
class Animal:
    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def speak(self):
        super().speak()  # Call the parent's speak()
        print(f"{self.name} also barks: Woof!")

my_dog = Dog("Buddy")
my_dog.name = "Buddy"  # Set name manually for this example
my_dog.speak()
# Output:
# Buddy makes a sound
# Buddy also barks: Woof!
```

The trade-off: you can ignore the parent's method entirely and write a completely new one (pure override), or you can call `super()` and build on what the parent did (extension). Choose based on what the subclass needs to do.

### Worked example — building a bank account hierarchy

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance
    
    def deposit(self, amount):
        self.balance += amount
        print(f"Deposited ${amount}. New balance: ${self.balance}")
    
    def display_info(self):
        print(f"Account owner: {self.owner}, Balance: ${self.balance}")

class CheckingAccount(BankAccount):
    def __init__(self, owner, balance=0, overdraft_limit=100):
        super().__init__(owner, balance)  # Initialize parent attributes
        self.overdraft_limit = overdraft_limit
    
    def withdraw(self, amount):
        if self.balance - amount >= -self.overdraft_limit:
            self.balance -= amount
            print(f"Withdrew ${amount}. New balance: ${self.balance}")
        else:
            print(f"Overdraft limit exceeded!")
    
    def display_info(self):
        super().display_info()  # Call parent's display_info()
        print(f"Overdraft limit: ${self.overdraft_limit}")

checking = CheckingAccount("Alice", balance=500, overdraft_limit=100)
checking.deposit(200)          # Prints: Deposited $200. New balance: $700
checking.withdraw(750)         # Prints: Withdrew $750. New balance: -$50
checking.display_info()        # Shows owner, balance, and overdraft limit
```

> **Prompt for Claude.** "Write a Python class hierarchy with a `Vehicle` superclass and two subclasses: `Car` and `Motorcycle`. The `Vehicle` class should have `__init__(self, brand, year)` and a method `describe()`. The `Car` class adds `num_doors` and overrides `describe()` to include the door count. The `Motorcycle` class adds `has_sidecar` (boolean) and overrides `describe()` to mention the sidecar status. Use `super()` in both subclasses to call the parent's `__init__()` method."

> **Tests to validate.**
> - A car with brand "Toyota" and 4 doors can be created and `describe()` prints "Toyota car with 4 doors".
> - A motorcycle with brand "Harley" and a sidecar `describe()` prints "Harley motorcycle with sidecar".
> - A motorcycle with no sidecar `describe()` prints "Harley motorcycle without sidecar".

### Common misconceptions

**`super()` doesn't mean "call the parent's version and stop."** After `super().__init__()` returns, the current method continues. You can do things before and after the `super()` call.

**Overriding is not the same as overloading.** In Python, you cannot have two methods with the same name and different signatures (different parameter counts). If you define `Dog.bark(self)` and then `Dog.bark(self, volume)`, the second definition replaces the first. To handle different parameter counts, use default arguments: `def bark(self, volume=1)`.

---

## Concept 3 — Polymorphism and method resolution

Polymorphism, from Greek *poly* (many) and *morphe* (form) — one name, many forms. In inheritance, polymorphism means you can call the same method name on different subclasses, and each class executes its own version.

```python
class Shape:
    def area(self):
        return 0

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14159 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height

# Same method name, different implementations
circle = Circle(5)
rect = Rectangle(4, 6)

print(circle.area())  # Prints: 78.5375 (approximation of pi * 25)
print(rect.area())    # Prints: 24
```

The magic: you can write code that works with any `Shape` without knowing whether it's a circle or rectangle:

```python
shapes = [Circle(3), Rectangle(2, 5), Circle(4)]
total = sum(shape.area() for shape in shapes)
print(f"Total area: {total}")
```

The code calls `shape.area()` on each element. Python looks at the actual type of each object — is it a `Circle` or a `Rectangle`? — and calls the appropriate method. This is method resolution: Python searches for the method starting in the object's class, then in its parent class, then in the parent's parent, all the way up the inheritance chain. The first match wins.

Polymorphism also works across unrelated classes — classes that don't inherit from each other. Python doesn't check type; it just looks for the method:

```python
class Dog:
    def speak(self):
        print("Woof!")

class Duck:
    def speak(self):
        print("Quack!")

animals = [Dog(), Duck()]
for animal in animals:
    animal.speak()  # Each calls its own speak()
```

This is sometimes called "duck typing": if it walks like a duck and quacks like a duck, Python treats it as a duck. It doesn't check inheritance; it checks whether the method exists.

### Worked example — a shape area calculator

```python
import math

class Shape:
    """Base class for shapes."""
    def area(self):
        raise NotImplementedError("Subclass must implement area()")

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return math.pi * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height

class Triangle(Shape):
    def __init__(self, base, height):
        self.base = base
        self.height = height
    
    def area(self):
        return 0.5 * self.base * self.height

# Polymorphism in action
shapes = [
    Circle(5),
    Rectangle(4, 6),
    Triangle(8, 3)
]

for shape in shapes:
    print(f"Area: {shape.area():.2f}")

total_area = sum(shape.area() for shape in shapes)
print(f"Total area: {total_area:.2f}")
```

> **Prompt for Claude.** "Write three classes: `BankAccount`, `SavingsAccount`, and `CheckingAccount`. Each has a method `monthly_fee()` that returns the fee amount. `BankAccount.monthly_fee()` returns 0. `SavingsAccount.monthly_fee()` returns 2. `CheckingAccount.monthly_fee()` returns 5. Write a function `total_fees(accounts)` that takes a list of accounts (mixed types) and returns the total fees. Demonstrate polymorphism by calling it with a mixed list."

> **Tests to validate.**
> - A list with one savings account and one checking account returns 7.
> - A list with three savings accounts returns 6.
> - The function doesn't need to check the type of each account; polymorphism handles it.

### Common misconceptions

**Method resolution doesn't stop at the superclass.** Python searches the entire inheritance chain — grandparent, great-grandparent, all the way to `object`, Python's base class that every class inherits from.

**Duck typing doesn't mean "no errors."** If a class doesn't have the method you're calling, Python raises an `AttributeError`. Duck typing is about flexibility, not about avoiding errors.

**Not all method names work polymorphically.** Polymorphism relies on consistent naming. If one class calls `calculate_total()` and another calls `sum_items()`, they're not polymorphic — they're just differently named. The power of polymorphism comes from all classes in a group using the same method names for the same purposes.

### Errors in inheritance

Here are errors you'll see when working with inheritance:

**`AttributeError: 'ClassName' object has no attribute 'method_name'`.** You called a method or accessed an attribute that doesn't exist. Check the inheritance chain — did you forget to use `super()` to initialize a parent attribute? Did you misspell the method name?

Example:
```python
class Dog(Animal):
    def __init__(self, name):
        self.name = name  # Forgot super().__init__()

my_dog = Dog("Buddy")
print(my_dog.species)  # AttributeError — never initialized
```

Fix: Call `super().__init__()` in the subclass.

**`TypeError: __init__() takes X positional arguments but Y were given`.** You're passing the wrong number of arguments to a subclass initializer. This often happens when the subclass initializer expects different arguments than the parent.

Example:
```python
class Animal:
    def __init__(self, name, species):  # Expects 2 arguments
        ...

class Dog(Animal):
    def __init__(self, name, breed):  # Also expects 2, but different
        super().__init__(name, "Canis familiaris")
```

When you call `Dog("Luna", "Retriever")`, the arguments match. But if you accidentally pass three arguments, Python complains.

---

## Concept 4 — Hierarchical inheritance and class trees

Real programs build trees of classes. An `Employee` is a superclass. `Developer`, `Manager`, and `Designer` inherit from it. Then `SeniorDeveloper` inherits from `Developer`. The structure is hierarchical — pyramid-shaped, with the most general class at the top and specialized ones at the bottom.

Multilevel inheritance is when a subclass becomes the superclass for another class:

```python
class Animal:
    def speak(self):
        print("Generic animal sound")

class Mammal(Animal):
    def nurse(self):
        print("Nursing offspring")

class Dog(Mammal):
    def bark(self):
        print("Woof!")

my_dog = Dog()
my_dog.speak()  # Inherited from Animal (two levels up)
my_dog.nurse()  # Inherited from Mammal (one level up)
my_dog.bark()   # Defined in Dog
```

Hierarchical inheritance is when multiple classes inherit from the same parent:

```python
class Animal:
    def speak(self):
        print("Generic animal sound")

class Dog(Animal):
    def speak(self):
        print("Woof!")

class Cat(Animal):
    def speak(self):
        print("Meow!")

class Bird(Animal):
    def speak(self):
        print("Tweet!")

animals = [Dog(), Cat(), Bird()]
for animal in animals:
    animal.speak()  # Each class's own speak()
```

You can combine both — multiple classes inheriting from the same parent, some of those classes becoming parents themselves — building a tree shape.

### Worked example — a game character hierarchy

In a game, different character types behave differently. A warrior has armor that reduces damage. A mage has mana for casting spells. Both are characters — they have health and can take damage — but their rules are different.

```python
class Character:
    """Base class for all game characters."""
    def __init__(self, name, hp):
        self.name = name
        self.hp = hp
    
    def take_damage(self, damage):
        """Apply damage and report it."""
        self.hp -= damage
        print(f"{self.name} takes {damage} damage. HP: {self.hp}")
    
    def is_alive(self):
        """Return whether the character is still alive."""
        return self.hp > 0

class Warrior(Character):
    """A character with armor that reduces damage."""
    def __init__(self, name, hp, armor):
        super().__init__(name, hp)
        self.armor = armor
    
    def take_damage(self, damage):
        """Override: armor reduces damage before applying it."""
        reduced_damage = damage - self.armor
        super().take_damage(max(reduced_damage, 0))  # Minimum 0 damage
    
    def swing_sword(self):
        """Warrior's special ability."""
        print(f"{self.name} swings a sword!")

class Mage(Character):
    """A character with mana for casting spells."""
    def __init__(self, name, hp, mana):
        super().__init__(name, hp)
        self.mana = mana
    
    def cast_spell(self, mana_cost):
        """Cast a spell if enough mana is available."""
        if self.mana >= mana_cost:
            self.mana -= mana_cost
            print(f"{self.name} casts a spell! Mana remaining: {self.mana}")
        else:
            print(f"{self.name} doesn't have enough mana!")
    
    def regen_mana(self, amount):
        """Mages can recover mana."""
        self.mana += amount
        print(f"{self.name} regenerates {amount} mana. Total: {self.mana}")

# Using the hierarchy
warrior = Warrior("Thor", hp=100, armor=10)
mage = Mage("Gandalf", hp=50, mana=100)

warrior.take_damage(15)  # Takes 5 damage (15 - 10 armor), HP becomes 95
mage.take_damage(10)     # Takes full 10 damage, HP becomes 40 (no armor)
mage.cast_spell(20)      # Succeeds, mana becomes 80
mage.cast_spell(100)     # Not enough mana (only 80 available)
mage.regen_mana(50)      # Mana becomes 130

print(f"Warrior alive: {warrior.is_alive()}")  # True
print(f"Mage alive: {mage.is_alive()}")        # True
```

Notice that both classes inherit `is_alive()` without changing it. The method works for both because both have `hp`. The warrior overrides `take_damage()` to apply armor, but still calls `super().take_damage()` to update health and print the message. The mage gets `take_damage()` unchanged but adds its own methods like `cast_spell()` and `regen_mana()`.

This is a clean hierarchy: common behavior lives in the parent; specialized behavior lives in subclasses.

> **Prompt for Claude.** "Design a class hierarchy for a library system. Create a `LibraryItem` superclass with `title`, `author`, and `checkout_date` attributes. Create two subclasses: `Book` (with `pages`) and `DVD` (with `duration` in minutes). Each subclass should override a method `display_info()`. Write code that creates a mixed list of books and DVDs and calls `display_info()` on each, using polymorphism."

> **Tests to validate.**
> - A book displays its title, author, and page count.
> - A DVD displays its title, author, and duration.
> - The list can be iterated without checking the type of each item.

### Common misconceptions

**Deep hierarchies can become unmanageable.** A chain of inheritance three or four levels deep is common and manageable. Beyond that, the code becomes hard to reason about — you have to trace through multiple parents to understand what a method does. If you find yourself writing very deep hierarchies, consider whether composition (has-a) would be clearer than inheritance (is-a).

**Multiple inheritance should be rare.** Python allows a class to inherit from multiple superclasses. This is powerful but dangerous. It can create the "diamond problem" — if two parent classes share a common ancestor, which version of a method does the child use? For now, stick with single inheritance (one parent per class). Multiple inheritance is an advanced tool that requires careful design.

---

## Concept 5 — The Liskov Substitution Principle

There is a rule for inheritance that shows up everywhere in professional code, even if it's not always named. The Liskov Substitution Principle (named after computer scientist Barbara Liskov) says this: a subclass should be usable anywhere the parent class is used, without breaking the program.

Here's what that means in practice. If you have a function that works with `Shape` objects, it should work correctly with `Circle`, `Rectangle`, and any other `Shape` subclass:

```python
def calculate_total_area(shapes):
    """Takes a list of shapes and returns the total area."""
    total = 0
    for shape in shapes:
        total += shape.area()
    return total
```

This function works with any shape because it only calls methods that `Shape` promises all subclasses will have: `area()`. As long as every subclass properly overrides `area()`, the function works. That's the substitution principle — you can substitute any subclass for the parent, and the code still works.

When you violate this principle, things break. Here's a violation:

```python
class Bird:
    def fly(self):
        print("Flying...")

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins cannot fly!")

# Using the principle
def make_bird_fly(bird):
    bird.fly()  # Works for birds... except penguins!

penguin = Penguin()
make_bird_fly(penguin)  # Crashes!
```

A penguin is a bird, but it doesn't behave like a bird in this context — it can't fly. So `Penguin` shouldn't inherit from `Bird`. Instead, you'd refactor:

```python
class Bird:
    def move(self):
        pass

class FlyingBird(Bird):
    def fly(self):
        print("Flying...")

class SwimmingBird(Bird):
    def swim(self):
        print("Swimming...")

class Penguin(SwimmingBird):
    def swim(self):
        print("Penguin swimming efficiently...")
```

Now `Penguin` is a `SwimmingBird`, not a `FlyingBird`. Any code that expects a `SwimmingBird` can use a `Penguin` safely.

The principle shapes good inheritance design: subclasses should fulfill the contract of their parent. If you find yourself raising `NotImplementedError` or writing methods that don't make sense for the subclass, you've violated the principle. Refactor the hierarchy.

---

## Integration — inheritance in design

Return to the cold open. You learn to use a hand saw. The circular saw looks different, acts differently, but you recognize the principle — how to position the material, how to apply force. You inherit knowledge from the first tool and apply it to the second.

A well-designed inheritance hierarchy does this for code. A programmer reads the `Shape` base class, understands the interface (the methods and attributes all shapes are expected to have), then reads `Circle` or `Rectangle`. They don't have to re-learn the foundation; they only read what's new.

This is why inheritance matters in large programs. A graphics library defines `Shape` as the base. Any new shape you want to add — hexagon, star, spiral — inherits from `Shape` and only needs to implement `area()` and `perimeter()`. A thousand lines of existing code that calls `calculate_total_area(shapes)` works automatically with the new shape. You didn't have to rewrite anything; you just added the new class.

But inheritance is a tool, not a default. Sometimes a "has-a" relationship (composition) is clearer than an "is-a" relationship (inheritance). A `Car` has an `Engine` — composition is better than having `Car` inherit from `Engine`. A `Programmer` has a `Computer` — composition, not inheritance. Composition is often simpler because it avoids deep hierarchies and the complexity of method resolution.

The heuristic: use inheritance when you have true is-a relationships and when subclasses share enough structure that reusing code matters. Use composition when one object is a part of another, not a type of another. Follow the Liskov Substitution Principle — any subclass should work everywhere the parent works. As programs grow, you'll find yourself using inheritance sparingly, saving it for the moments when the relationship is genuinely hierarchical.

Real programs tend toward shallow hierarchies. A base class, maybe one or two levels of subclasses, then you're done. Deep inheritance (four or five levels) starts smelling like overdesign. When you reach that depth, usually composition is clearer.

By the end of this chapter, you understand the vocabulary and the mechanics. Chapters 14 and 15 will show you inheritance in the wild — in libraries like pandas, where data classes and analysis classes form rich hierarchies, and in frameworks like Flask, where you inherit from base classes to build web applications. When you see those patterns, you'll recognize them because you've built inheritance hierarchies yourself.

---

## Exercises

### Warm-up

**Exercise 13.1** *(LO: recognize is-a relationships.)* For each pair, decide whether the relationship is is-a (inheritance) or has-a (composition):
1. A `Student` and a `University`.
2. A `Square` and a `Shape`.
3. A `Bicycle` and a `Wheel`.
4. A `Penguin` and a `Bird`.

Write a sentence for each explaining your choice.

**Exercise 13.2** *(LO: write a simple subclass.)* Create an `Animal` class with `name` and `species` attributes and a `speak()` method. Create a `Cat` subclass that inherits from `Animal` and has no additional attributes or methods. Create an instance and call both the inherited attributes and the `speak()` method.

**Exercise 13.3** *(LO: override a method.)* Modify the `Cat` class from Exercise 13.2 to override `speak()` so it prints `"[name] meows"` instead of the generic animal sound.

### Application

**Exercise 13.4** *(LO: use `super()` in `__init__()`.)* Create a `Vehicle` superclass with `brand` and `year` attributes. Create a `Motorcycle` subclass with an additional `engine_size` attribute. Use `super().__init__()` to initialize the parent's attributes. Create an instance and verify all three attributes are set.

**Exercise 13.5** *(LO: polymorphism.)* Create three unrelated classes: `Dog`, `Cat`, and `Robot`. Each has a `make_sound()` method that prints a different sound. Create a list with one instance of each and iterate through it, calling `make_sound()` on each. Show that polymorphism works even though the classes don't share an inheritance relationship.

**Exercise 13.6** *(LO: hierarchical inheritance.)* Create a `Shape` superclass with an `area()` method that returns 0. Create two subclasses: `Circle` and `Square`. Each should override `area()` to calculate correctly. Create a mixed list and sum the total area using polymorphism.

### Synthesis

**Exercise 13.7** *(LO: design a multi-level hierarchy.)* Design a class hierarchy for bank accounts. Create an `Account` base class with `owner`, `balance`, and a `deposit()` method. Create `SavingsAccount` (adds `interest_rate` and calculates interest monthly) and `CheckingAccount` (adds `monthly_fee` and allows overdrafts). Create a mixed list of accounts and apply `deposit()` to each using polymorphism.

**Exercise 13.8** *(LO: use `super()` in methods other than `__init__()`.)* Create an `Employee` class with a `give_raise(percent)` method that increases salary by the percentage. Create a `Manager` class that overrides `give_raise()` to also give a bonus. Use `super()` to call the parent's method before adding the bonus logic. Demonstrate both classes working correctly.

**Exercise 13.9** *(LO: composition vs. inheritance.)* Redesign the pet system as a composition example. Create a `Toy` class and a `Pet` class that contains a toy (has-a), rather than inheriting. Show how this differs structurally from the inheritance approach. Which feels clearer to you?

**Exercise 13.10** *(LO: debug inheritance errors.)* Given the code below, identify the bugs and fix them:

```python
class Vehicle:
    def __init__(self, brand, year):
        self.brand = brand
        self.year = year

class Bicycle(Vehicle):
    def __init__(self, brand):
        self.brand = brand
        self.type = "bicycle"

bike = Bicycle("Trek")
print(f"{bike.brand}, {bike.year}")  # Error expected
```

What's missing? How would you fix it?

---

## Claude Code — Building an account hierarchy with inheritance

In Chapter 11, you built a `BankAccount` class. Now you'll extend it using inheritance — the mechanism that keeps you from rewriting the same logic twice when you add specialized account types.

The task: ask Claude Code to build `accounts.py` extending the `BankAccount` class with two subclasses: `CheckingAccount` (allows overdrafts up to a limit) and `SavingsAccount` (earns interest). Then write tests that use polymorphism — a single function that processes any account type.

**Prompt to Claude Code:**

```
Write a Python module called accounts.py that extends the BankAccount class from Chapter 11.

1. Create a CheckingAccount(BankAccount) subclass with:
   - An overdraft_limit attribute (default 500)
   - An override of withdraw() that allows the balance to go negative
     up to the overdraft limit
   - If a withdrawal would exceed the limit, raise ValueError

2. Create a SavingsAccount(BankAccount) subclass with:
   - An interest_rate attribute (in decimal, e.g., 0.02 for 2%)
   - A method apply_interest() that adds interest to the balance
   - Both subclasses should call super().__init__() to initialize parent attributes

3. Use the BankAccount class from Chapter 11 (or define it inline if needed).

Then write test_accounts.py with pytest:
   - Test that a BankAccount cannot overdraft
   - Test that a CheckingAccount can overdraft up to the limit
   - Test that a CheckingAccount raises ValueError if overdraft is exceeded
   - Test that a SavingsAccount accrues interest correctly
   - Test polymorphism: write a function process_monthly(accounts) that takes
     a list of accounts (mixed types), applies interest to savings accounts,
     charges fees if needed, and returns the total balance across all accounts.
     The function should not check the type of each account—use polymorphism.

Run pytest and show all tests passing.
```

**Expected output:**

Claude Code will generate `accounts.py` with the two subclasses, each using `super().__init__()` to initialize the parent's attributes. The CheckingAccount overrides `withdraw()` to implement custom behavior (allowing overdraft). The SavingsAccount adds a new method `apply_interest()` specific to savings accounts.

The test file demonstrates polymorphism: a single `process_monthly()` function handles a mixed list of account types. Python doesn't check whether each account is a CheckingAccount or SavingsAccount; it just calls `withdraw()` or `apply_interest()` on each. If the method exists, it works.

Run the tests. All should pass. Notice that:
- Subclassing eliminated duplication. The `CheckingAccount` and `SavingsAccount` didn't redefine `deposit()` or other parent methods — they inherited them.
- `super().__init__()` ensured each subclass initialized its parent's attributes before adding its own.
- Method overriding allowed `CheckingAccount.withdraw()` to have different behavior than `BankAccount.withdraw()`.
- Polymorphism meant the test function didn't need to know which account type it was processing — it just called the methods and trusted Python to call the right one.

---

## Chapter summary

You can now do six things you could not do an hour ago.

You can distinguish is-a relationships (inheritance) from has-a relationships (composition), and choose the right tool for each. A `Dog` is a type of `Animal` — inheritance. An `Animal` has a `Name` — composition. This distinction shapes how you structure programs.

You can write a subclass that inherits all the attributes and methods from a superclass without redefining them. `class Dog(Animal):` gives `Dog` access to everything `Animal` has. You can extend it with new attributes and methods specific to `Dog`.

You can override methods in a subclass to change behavior. The subclass version shadows the superclass version. When called on a subclass instance, Python uses the subclass's method, not the parent's.

You can use `super()` to call the parent's method from inside the subclass, either in `__init__()` to initialize parent attributes, or in overridden methods to build on the parent's logic.

You can write code that works with any class that has a certain method, without needing to check the type. This is polymorphism — one method name, many implementations. Python looks at the actual object's class and calls the right method.

You understand hierarchical inheritance: multiple classes inheriting from the same parent, or a chain where a subclass becomes the parent of another. This is how large programs organize themselves.

The thing to watch for, going forward: inheritance is powerful, but deep hierarchies and complex multiple-inheritance structures become hard to understand. Simpler is better. Composition often makes code clearer than inheritance. As you build larger programs, favor shallow hierarchies and lean toward composition when you're unsure.

What you should now be able to teach a friend: how to inherit from a class, why you'd override a method, and what `super()` does.

---

## Connections forward

Chapter 14 — Files — introduces reading and writing data to disk. You'll build file-handling functions that can be imported into multiple programs, a pattern that relies on modules (Chapter 7) and functions (Chapter 6). The data structures you read and write — lists, dictionaries — are the same ones you've been working with since Chapters 9 and 10. Inheritance doesn't appear as heavily in file I/O, but the organizational thinking it teaches carries forward.

Chapter 15 — Data science — is where inheritance becomes visible again. Libraries like pandas and NumPy define classes with rich inheritance hierarchies. A DataFrame is a subclass of a more general table concept. Series objects inherit from sequences. Understanding inheritance will make it easier to read documentation and understand what methods are available on the objects you create.

Throughout the rest of your programming life, you'll encounter frameworks and libraries built on inheritance. Flask web applications inherit from a base application class. Django models inherit from Model. The more you understand inheritance mechanics, the easier it becomes to work with professional code.

---

## Research notes
---

## LLM Exercise — Chapter 13: Inheritance (Text Adventure Project)

**Project:** Text Adventure.
**What you're building this chapter:** the inheritance hierarchies — Item → Weapon/Armor/Potion; Enemy → Goblin/Dragon/Boss. Polymorphism.
**Tool:** **Claude Code**.

---

**The Prompt:**

```
Chapter 13 of my Text Adventure project. Chapter 13 covered:
inheritance; method overriding; polymorphism; super(); the
inheritance hierarchy.

Build the type hierarchies.

1. **Item hierarchy.**
   ```python
   class Item:
       def __init__(self, name, description, value=0):
           ...
       def use(self, player):
           print(f"You can't use the {self.name} that way.")

   class Weapon(Item):
       def __init__(self, name, description, damage, value=0):
           super().__init__(name, description, value)
           self.damage = damage

       def use(self, player):
           # Equip as the player's weapon.
           player.equipped_weapon = self
           print(f"You wield the {self.name}.")

   class Armor(Item):
       ...
       def use(self, player):
           # Equip as armor; updates player.defense.
           ...

   class Potion(Item):
       def __init__(self, name, description, heal_amount, value=0):
           super().__init__(name, description, value)
           self.heal_amount = heal_amount

       def use(self, player):
           player.heal(self.heal_amount)
           # Consumable: remove from inventory.
           player.remove_from_inventory(self.name)
   ```

2. **Enemy hierarchy.**
   ```python
   class Enemy:
       def __init__(self, name, health, attack_power, defense, xp_reward, gold_reward):
           ...
       def attack(self, target):
           # Default: deterministic attack.
           ...
       def special_ability(self, target):
           # Default: no special ability.
           pass

   class Goblin(Enemy):
       def __init__(self):
           super().__init__("Goblin", 20, 5, 2, xp_reward=15, gold_reward=10)
       def attack(self, target):
           # Goblins are quick — 30% chance of double attack.
           ...

   class Dragon(Enemy):
       def __init__(self):
           super().__init__("Dragon", 200, 30, 15, xp_reward=500, gold_reward=200)
       def attack(self, target):
           # Standard physical attack.
           ...
       def special_ability(self, target):
           # Fire breath — extra damage.
           ...

   class Boss(Dragon):
       def __init__(self, name="The Demon King"):
           super().__init__()
           self.name = name
           self.health = 400
           self.attack_power = 50
       def special_ability(self, target):
           # Multiple specials; demon-specific.
           ...
   ```

3. **Polymorphism in combat.** The combat function now treats
   any Enemy the same way — but each enemy's `attack()` and
   `special_ability()` are overridden to behave differently. This
   is polymorphism: same interface, different behavior.

4. **Add 5-10 specific items** using Weapon, Armor, Potion
   classes. Add 5-10 specific enemies using Goblin, Dragon, Boss
   classes (or more subclasses).

In addition to the code, produce the prompt + tests. Tests:
- `w = Weapon("Sword of Truth", "...", damage=20); p.use_item(w);
  assert p.equipped_weapon == w`.
- `g = Goblin(); d = Dragon(); assert g.attack_power < d.attack_power`.
- Polymorphism test: combat loop with a Goblin vs. combat with
  a Dragon — different behaviors verified.

End with: name one place where composition (an Enemy HAS a
Weapon) would be cleaner than inheritance (Dragon IS-A Enemy).
This is the "favor composition over inheritance" lesson.
```

---

**What this produces:** A proper inheritance hierarchy + polymorphic combat. This is the OOP-textbook climax.

**Connection to previous chapters:** Ch 11's base classes get extended; the game now has rich type variety.

**Preview of next chapter:** Chapter 14 covers files. You'll add save/load — write the player's state and world state to a JSON file, restore them on load. The game becomes persistent across sessions.


---

##  AI Wayback Machine
**Ole-Johan Dahl** was co-designed Simula with Kristen Nygaard — and developed the inheritance mechanism that all modern OO languages descend from.

**Run this:**

```
Who is Ole-Johan Dahl, and how does their work connect to inheritance we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Ole-Johan Dahl"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Ole-Johan Dahl's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Ole-Johan Dahl's framework."

What changes? What gets better? What gets worse?
