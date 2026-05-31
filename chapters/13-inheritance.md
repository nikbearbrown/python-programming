# Chapter 13 — Inheritance
*One design. Many specializations. The same machinery underneath.*

You walk into a hardware store and find two saws hanging side by side. One is a hand saw — wooden handle, long blade, teeth along the edge. The other is a circular saw — a motor, a spinning blade, a guard. They are different machines. But if you've used a hand saw, you already know something about the circular saw: how to position the material, how to apply pressure, which way to push. You don't start from zero. You inherit knowledge from the simpler tool and adapt it.

Classes work the same way. You already have a `BankAccount` class with an owner, a balance, and methods for deposit and withdrawal. Now you need a `SavingsAccount` that earns interest, and a `CheckingAccount` that allows overdrafts. Both are bank accounts — they share the same foundation — but each has something specialized. You could copy-paste the `BankAccount` code into both new classes. Or you could build on it, inheriting the machinery you already wrote and adding only what's different.

That's inheritance: building a new class from an existing one, borrowing its structure and adding your own.

---

## Is-a versus has-a

Before writing any code, there's a prior question: should this relationship be inheritance at all?

Two relationships structure most class design. A **has-a relationship** is composition — one object contains another as a component. A car has an engine. A person has a bank account. The engine is not a type of car; it is a part of a car. You model this with an attribute that holds another object.

An **is-a relationship** is inheritance — one concept is a specialized type of another. A savings account is a type of account. A labrador is a type of dog. The subclass inherits the structure of the parent class and adds its own features.

The heuristic: use inheritance when the subclass truly is a more specific version of the parent, when swapping one for the other would still make sense in the program. Use composition when one concept contains another as a part. The distinction matters because it shapes the program's structure in ways that compound as the program grows.

Here's composition (a person *has* an account):

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

class Person:
    def __init__(self, name, account):
        self.name = name
        self.account = account    # account is a component, not a parent

    def deposit(self, amount):
        self.account.balance += amount
```

Here's inheritance (a checking account *is* an account):

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

class CheckingAccount(BankAccount):
    def __init__(self, balance, overdraft_limit=100):
        super().__init__(balance)
        self.overdraft_limit = overdraft_limit
```

`CheckingAccount` inherits from `BankAccount` — it automatically has the `deposit()` method and the `balance` attribute. It adds `overdraft_limit` on top. The subclass is named in parentheses after `class`, pointing at its parent.

---

## Inheriting without changing

The simplest subclass adds nothing at all:

```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species

    def speak(self):
        print(f"{self.name} makes a sound")

    def move(self):
        print(f"{self.name} is moving")

class Dog(Animal):
    pass
```

`pass` means "do nothing — just inherit." A `Dog` now has every attribute and method `Animal` has:

```python
buddy = Dog("Buddy", "Canis familiaris")
buddy.speak()    # Buddy makes a sound
buddy.move()     # Buddy is moving
```

This is rarely what you want, but it shows the mechanism cleanly: the subclass receives everything from the parent without any additional work.

<!-- → [INFOGRAPHIC: Inheritance tree diagram showing Animal at the top with two arrows pointing down to Dog and Cat. Dog box shows inherited attributes (name, species) in one color and a new attribute (breed) in a different color. Makes the "inherited vs. added" distinction visual.] -->

---

## `super()` and the initializer chain

When you define a new `__init__` in a subclass, you override the parent's initializer. The parent's attributes don't get set automatically — Python doesn't call two `__init__` methods by default. You have to call the parent's initializer yourself, using `super()`.

First, the mistake:

```python
class Dog(Animal):
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed        # species is never set

buddy = Dog("Buddy", "Golden Retriever")
print(buddy.species)              # AttributeError: 'Dog' object has no attribute 'species'
```

The dog has `name` and `breed`, but never `species` — because `Animal.__init__` never ran. Now the fix:

```python
class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Canis familiaris")    # Call the parent's __init__
        self.breed = breed

buddy = Dog("Buddy", "Golden Retriever")
print(buddy.name)      # Buddy
print(buddy.species)   # Canis familiaris
print(buddy.breed)     # Golden Retriever
```

`super()` — from Latin *super*, meaning above — reaches up to the parent class. `super().__init__(name, "Canis familiaris")` calls `Animal.__init__` with those arguments, setting `self.name` and `self.species`. Then execution returns to `Dog.__init__`, which sets `self.breed`. By the time the initializer finishes, all three attributes exist on the instance.

The rule: if your subclass defines `__init__`, call `super().__init__()` first (or at least early), before setting your own attributes. The parent should be initialized before you build on top of it.

---

## Overriding methods

To change a method's behavior in a subclass, define a method with the same name. Python searches for a method starting in the object's actual class — the subclass — before looking at the parent. The subclass version shadows the parent version.

```python
class Animal:
    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def speak(self):
        print(f"{self.name} barks: Woof!")

class Cat(Animal):
    def speak(self):
        print(f"{self.name} says: Meow.")

buddy = Dog("Buddy", "Canis familiaris")
whiskers = Cat("Whiskers", "Felis catus")

buddy.speak()      # Buddy barks: Woof!
whiskers.speak()   # Whiskers says: Meow.
```

When you want the subclass to do what the parent does *plus something more*, call `super()` inside the override:

```python
class CheckingAccount(BankAccount):
    def deposit(self, amount):
        super().deposit(amount)                  # Run the parent's deposit
        print(f"CheckingAccount updated.")       # Then do the extra step
```

This is *extension* — you call the parent's behavior and add to it. *Pure override* replaces the parent's behavior entirely. Choose whichever matches what the subclass actually needs.

---

## Polymorphism

**Polymorphism** — from Greek *poly* (many) and *morphe* (form) — is the ability to call the same method name on different objects and get different behavior, because each object's class provides its own version.

```python
import math

class Shape:
    def area(self):
        return 0

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
```

Now you can write code that works with any shape — without knowing which specific shape it is:

```python
shapes = [Circle(5), Rectangle(4, 6), Triangle(8, 3)]

for shape in shapes:
    print(f"Area: {shape.area():.2f}")

total = sum(shape.area() for shape in shapes)
print(f"Total: {total:.2f}")
```

Python looks at the actual type of each object and calls the right `area()`. The loop doesn't need to check types; it just calls the method and trusts each class to do the right thing. This is what makes polymorphism powerful — you can add new shapes later (a `Pentagon`, a `Star`) without modifying the code that processes them. The loop works automatically on any object that has an `area()` method.

<!-- → [TABLE: Method resolution order — three rows showing: where Python looks first (object's own class), then (parent class), then (parent's parent, up to object). Example column showing what each would find for `buddy.speak()` given the Animal/Dog hierarchy.] -->

Python doesn't even require a shared parent class for polymorphism to work. This is called *duck typing* — if an object has the method you're calling, Python calls it, regardless of whether the object is part of a formal hierarchy:

```python
class Dog:
    def speak(self):
        print("Woof!")

class Robot:
    def speak(self):
        print("Beep boop.")

for thing in [Dog(), Robot()]:
    thing.speak()
```

`Dog` and `Robot` share no parent class. They both have `speak()`. Python calls whichever version belongs to the actual object. If the method exists, it runs.

---

## A working hierarchy

Here is a game-character hierarchy that uses everything so far: inheritance, `super()`, method overriding, and polymorphism.

```python
class Character:
    def __init__(self, name, hp):
        self.name = name
        self.hp = hp

    def take_damage(self, damage):
        self.hp -= damage
        print(f"{self.name} takes {damage} damage. HP: {self.hp}")

    def is_alive(self):
        return self.hp > 0


class Warrior(Character):
    def __init__(self, name, hp, armor):
        super().__init__(name, hp)
        self.armor = armor

    def take_damage(self, damage):
        reduced = max(damage - self.armor, 0)
        super().take_damage(reduced)       # Parent handles the rest


class Mage(Character):
    def __init__(self, name, hp, mana):
        super().__init__(name, hp)
        self.mana = mana

    def cast_spell(self, cost):
        if self.mana >= cost:
            self.mana -= cost
            print(f"{self.name} casts a spell! Mana: {self.mana}")
        else:
            print(f"{self.name} is out of mana.")


warrior = Warrior("Thor", hp=100, armor=10)
mage = Mage("Gandalf", hp=50, mana=100)

warrior.take_damage(15)    # Takes 5 damage (15 - 10 armor)
mage.take_damage(10)       # Takes full 10 (no armor)
mage.cast_spell(20)
mage.cast_spell(100)       # Not enough mana

print(f"Warrior alive: {warrior.is_alive()}")
print(f"Mage alive: {mage.is_alive()}")
```

Both `Warrior` and `Mage` inherit `is_alive()` without change — it works for both because both have `hp`. `Warrior` overrides `take_damage()` to apply armor, then calls `super().take_damage(reduced)` to let the parent handle updating health and printing. `Mage` doesn't override `take_damage()` at all, so it uses the parent's version. Adding behavior without rewriting what already works.

---

## The Liskov Substitution Principle

There's a rule for inheritance that professional programmers follow even when they can't name it: a subclass should work anywhere the parent class is expected, without breaking anything.

Here's a violation:

```python
class Bird:
    def fly(self):
        print("Flying...")

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins cannot fly!")

def launch_bird(bird):
    bird.fly()

launch_bird(Penguin())    # Crashes — violates the substitution principle
```

A penguin is a bird biologically, but it doesn't fulfill the contract of `Bird` as defined here. Code that expects a `Bird` can't safely use a `Penguin`. The inheritance is wrong. Better:

```python
class Bird:
    def move(self):
        pass

class FlyingBird(Bird):
    def fly(self):
        print("Flying...")

class Penguin(Bird):
    def swim(self):
        print("Swimming...")
```

Now `Penguin` inherits from `Bird` but not from `FlyingBird`. Any code that expects a `FlyingBird` won't be given a `Penguin` by accident. The hierarchy matches the actual behavior.

The test: can you substitute a subclass for its parent in every situation the parent is used? If yes, the hierarchy is sound. If no, reconsider whether inheritance is the right relationship.

---

## When not to use inheritance

Inheritance is a tool, not a default. Deep hierarchies — four or five levels — become hard to reason about. When a method is called, you have to trace up the chain to find where it's actually defined. Composition is often clearer.

When you're unsure, ask: is this a genuine is-a relationship, or am I using inheritance because it's convenient? A common mistake is inheriting just to reuse a method, when the two classes don't actually share a type relationship. Composition handles that more cleanly — give the second class an attribute that is an instance of the first, and call its methods through that attribute.

In practice, most inheritance hierarchies are shallow — a base class and one or two levels of subclasses. When you find yourself wanting a third or fourth level, pause and ask whether the bottom class really is a type of the top class in any meaningful sense.

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

## AI Wayback Machine

**Ole-Johan Dahl** co-designed Simula with Kristen Nygaard — and developed the inheritance mechanism that all modern OO languages descend from.

**Run this:**

```
Who is Ole-Johan Dahl, and how does their work connect to inheritance we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about their career or ideas.
```

→ Search **"Ole-Johan Dahl"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to apply Ole-Johan Dahl's ideas to a small Python program you'd write.
- Add a constraint: "Answer including criticisms or limits of Ole-Johan Dahl's framework."

What changes? What gets better? What gets worse?

---

## Exercises

**Warm-up**

1. For each pair, decide whether the relationship is is-a (inheritance) or has-a (composition). Write a sentence explaining each:
   - A `Student` and a `University`
   - A `Square` and a `Shape`
   - A `Bicycle` and a `Wheel`
   - A `Penguin` and a `Bird`

2. Create an `Animal` class with `name` and `species` attributes and a `speak()` method that prints a generic sound. Create a `Cat` subclass that inherits without adding anything. Create an instance and verify the inherited attributes and method work.

3. Override `speak()` in your `Cat` class so it prints `"[name] says: Meow."` Create a cat and a generic animal and call `speak()` on both. Verify the outputs differ.

**Application**

4. Create a `Vehicle` superclass with `brand` and `year` attributes. Create a `Motorcycle` subclass with an additional `engine_size` attribute. Use `super().__init__()` to initialize the parent's attributes. Verify all three attributes are accessible on a motorcycle instance.

5. Create three classes — `Dog`, `Cat`, and `Robot` — that share no inheritance relationship but each have a `make_sound()` method. Build a list with one instance of each and iterate over it, calling `make_sound()`. This is duck typing in action.

6. Create a `Shape` base class with `area()` returning 0. Create `Circle` and `Square` subclasses that override `area()` correctly. Build a mixed list and compute the total area using a single loop without type checking.

**Synthesis**

7. Design a bank account hierarchy: an `Account` base class with `owner`, `balance`, and `deposit()`. A `SavingsAccount` subclass that adds `interest_rate` and an `apply_interest()` method. A `CheckingAccount` subclass that adds `overdraft_limit` and overrides `withdraw()` to allow overdrafts up to the limit. Build a mixed list and call `deposit()` on each using polymorphism.

8. Create an `Employee` base class with `name`, `salary`, and a `give_raise(percent)` method. Create a `Manager` subclass that overrides `give_raise()` to also add a flat bonus after the raise. Use `super()` in the override. Test both classes and verify the manager's total increase is raise plus bonus.

**Challenge**

9. The code below has two inheritance bugs. Find and fix them without running the code first:

```python
class Vehicle:
    def __init__(self, brand, year):
        self.brand = brand
        self.year = year

class Bicycle(Vehicle):
    def __init__(self, brand):
        self.brand = brand
        self.type = "bicycle"

    def describe(self):
        return f"{brand} bicycle from {year}"

bike = Bicycle("Trek")
print(bike.describe())
```

After fixing, verify that `bike.year` raises an `AttributeError` (because you never passed it), and update the code to either pass a default year or require it.

10. Design a `Bird` hierarchy that correctly handles penguins without violating the Liskov Substitution Principle. Start by trying to make `Penguin` a subclass of a generic `Bird` class with a `fly()` method. Show why that breaks. Then redesign the hierarchy so that `Penguin` fits naturally.
