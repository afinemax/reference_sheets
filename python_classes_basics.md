## Python Classes

### Defining a Class
```python
class Animal:
    def __init__(self, name, species):
        self.name = name  # Instance variable
        self.species = species
    
    def make_sound(self, sound):
        return f"{self.name} the {self.species} says {sound}!"
```

### Creating Objects
```python
dog = Animal("Buddy", "Dog")
cat = Animal("Whiskers", "Cat")

print(dog.make_sound("Woof"))  # Buddy the Dog says Woof!
print(cat.make_sound("Meow"))  # Whiskers the Cat says Meow!
```

### Inheritance
```python
class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Dog")  # Call the parent constructor
        self.breed = breed  # Additional attribute
    
    def fetch(self):
        return f"{self.name} is fetching the ball!"
```

### Using the Subclass
```python
buddy = Dog("Buddy", "Golden Retriever")
print(buddy.make_sound("Woof"))  # Buddy the Dog says Woof!
print(buddy.fetch())  # Buddy is fetching the ball!
```

### Class vs Instance Variables
```python
class Example:
    class_variable = "I am shared"
    
    def __init__(self, value):
        self.instance_variable = value
```

```python
obj1 = Example("Instance 1")
obj2 = Example("Instance 2")
print(obj1.class_variable)  # I am shared
print(obj2.class_variable)  # I am shared
print(obj1.instance_variable)  # Instance 1
print(obj2.instance_variable)  # Instance 2
```

### Private and Protected Variables
```python
class PrivateExample:
    def __init__(self):
        self._protected = "I am protected"
        self.__private = "I am private"
```

```python
obj = PrivateExample()
print(obj._protected)  # Accessible but should be treated as private
# print(obj.__private)  # Will raise an AttributeError
