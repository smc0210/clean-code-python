# clean-code-python

## Table of Contents
  1. [도입](#도입)
  2. [변수](#변수)
  3. [함수](#함수)
  4. [객체와 자료구조](#객체와-자료구조)
  5. [클래스](#클래스)
     1. [S: Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
     2. [O: Open/Closed Principle (OCP)](#openclosed-principle-ocp)
     3. [L: Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
     4. [I: Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
     5. [D: Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  6. [Don't repeat yourself (DRY)](#dont-repeat-yourself-dry)

## 도입

소프트웨어 엔지니어링의 원칙으로 불리는 Robert C. Martin의 저서 
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
를 파이썬으로 각색한 버전입니다. 이것은 스타일 가이드가 아닙니다.
파이썬으로 읽기 쉽고, 재사용 가능하며 리팩토링이 가능한 소프트웨워를 제작하는 가이드 입니다.

본 문서의 모든 원칙을 엄격하게 준수 할 필요는 없으며, 심지어 더 적은 사람들이 보편적으로 동의할 것입니다.
이것들은 지침일 뿐이지 그 이상은 아니지만, 수년 간 *Clean Code*의 저자들에 의해 축적된 경험들을 통해 체계화된 것들입니다.

[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)에서 영감을 얻었습니다.

`Python3.7+` 를 대상으로 합니다.

## **변수**
### 의미 있고 발음하기 쉬운 변수 이름을 사용하세요.

**안 좋은 예:**
```python
ymdstr = datetime.date.today().strftime("%y-%m-%d")
```

**좋은 예:**
```python
current_date: str = datetime.date.today().strftime("%y-%m-%d")
```
**[⬆ back to top](#table-of-contents)**

### 동일한 유형의 변수에 동일한 어휘를 사용하세요.

**안 좋은 예:**
여기서는 동일한 기본 엔티티(Entity)에 대해 세 가지 다른 이름을 사용합니다.:
```python
get_user_info()
get_client_data()
get_customer_record()
```

**좋은 예:**
만약 엔티티(Entity)가 동일하다면 함수에서 엔티티(Entity)를 참조하는 데 일관성이 있어야 합니다.:
```python
get_user_info()
get_user_data()
get_user_record()
```

**더 나은 예**
파이썬은 (또한) 객체 지향 프로그래밍 언어입니다. 의미가 있다면 코드에서 엔티티(Entity)의 구체적인 구현과 함께
인스턴스 속성(attributes), 프로퍼티 메소드(property methods) 또는 메소드(methods)로 함수를 패키지화 하십시오:

```python
class User:
    info : str

    @property
    def data(self) -> dict:
        # ...

    def get_record(self) -> Union[Record, None]:
        # ...
```

**[⬆ back to top](#table-of-contents)**

### 검색 가능한 이름을 사용하세요.
We will read more code than we will ever write. It's important that the code we do write is 
readable and searchable. By *not* naming variables that end up being meaningful for 
understanding our program, we hurt our readers.
Make your names searchable.

우리는 우리가 작성하는 것보다 더 많은 코드를 읽을 것입니다. 우리가 작성하는 코드를 읽고 검색할 수 있어야 합니다. 
프로그램을 이해하기 위해서 의미가 있는 변수이름을 짓지 *않는다*면 코드를 읽는 사람들에게 상처를 줄 수 있습니다.
검색 가능한 이름을 사용하세요.

**안 좋은 예:**
```python
# 도대체 86400이 뭐야?
time.sleep(86400);
```

**좋은 예:**
```python
# 모듈의 전역 네임스페이스에 선언하십시오.
SECONDS_IN_A_DAY = 60 * 60 * 24

time.sleep(SECONDS_IN_A_DAY)
```
**[⬆ back to top](#table-of-contents)**

### 설명적인 변수를 사용하세요.
**안 좋은 예:**
```python
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = r'^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$'
matches = re.match(city_zip_code_regex, address)

save_city_zip_code(matches[1], matches[2])
```

**나쁘지 않은 예**:

더 낫지만 여전히 정규표현식에 크게 의존합니다.

```python
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = r'^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$'
matches = re.match(city_zip_code_regex, address)

city, zip_code = matches.groups()
save_city_zip_code(city, zip_code)
```

**좋은 예:**

하위 패턴의 이름을 지정하여 정규식에 대한 의존성을 줄입니다.
```python
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = r'^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$'
matches = re.match(city_zip_code_regex, address)

save_city_zip_code(matches['city'], matches['zip_code'])
```
**[⬆ back to top](#table-of-contents)**

### Avoid Mental Mapping
코드를 읽는 사람이 변수의 의미를 번역하도록 강요하지 마세요.
암시적인 것보다 명시적인 것이 좋습니다.

**안 좋은 예:**
```python
seq = ('Austin', 'New York', 'San Francisco')

for item in seq:
    do_stuff()
    do_some_other_stuff()
    # ...
    # 잠깐만요, `item`이 뭐에요?
    dispatch(item)
```

**좋은 예:**
```python
locations = ('Austin', 'New York', 'San Francisco')

for location in locations:
    do_stuff()
    do_some_other_stuff()
    # ...
    dispatch(location)
```
**[⬆ back to top](#table-of-contents)**


### 불필요한 문맥을 추가하지 마세요.

클래스/객체의 이름이 뭔가를 말한다면 변수 이름에서 반복하지 마세요.

**안 좋은 예:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**좋은 예:**

```python
class Car:
    make: str
    model: str
    color: str
```

**[⬆ back to top](#table-of-contents)**

### Use default arguments instead of short circuiting or conditionals
### 단락 또는 조건문 대신 기본 인수(arguments)를 사용하세요.

**Tricky**

Why write:

```python
def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

... when you can specify a default argument instead? This also makes ist clear that
you are expecting a string as the argument.

**좋은 예:**

```python
def create_micro_brewery(name: str = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ back to top](#table-of-contents)**
## **함수**
### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it makes 
testing your function easier. Having more than three leads to a combinatorial explosion 
where you have to test tons of different cases with each separate argument.

Zero arguments is the ideal case. One or two arguments is ok, and three should be avoided. 
Anything more than that should be consolidated. Usually, if you have more than two 
arguments then your function is trying to do too much. In cases where it's not, most 
of the time a higher-level object will suffice as an argument.

**안 좋은 예:**
```python
def create_menu(title, body, button_text, cancellable):
    # ...
```

**좋은 예:**
```python
class Menu:
    def __init__(self, config: dict):
        title = config["title"]
        body = config["body"]
        # ...

menu = Menu(
    {
        "title": "My Menu",
        "body": "Something about my menu",
        "button_text": "OK",
        "cancellable": False
    }
)
```

**Also good**
```python
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title = config.title
    body = config.body
    # ...


config = MenuConfig
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**Fancy**
```python
from typing import NamedTuple


class MenuConfig(NamedTuple):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = config
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier**
```python
from dataclasses import astuple, dataclass


@dataclass
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False

def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = astuple(config)
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**[⬆ back to top](#table-of-contents)**

### Functions should do one thing
This is by far the most important rule in software engineering. When functions do more 
than one thing, they are harder to compose, test, and reason about. When you can isolate 
a function to just one action, they can be refactored easily and your code will read much 
cleaner. If you take nothing else away from this guide other than this, you'll be ahead 
of many developers.

**안 좋은 예:**
```python

def email_clients(clients: List[Client]):
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**좋은 예:**
```python
def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client, ...]) -> None:
    """Send an email to a given list of clients.
    """
    for client in clients:
        email(client)
```

Do you see an opportunity for using generators now?

**Even better**
```python
def active_clients(clients: List[Client]) -> Generator[Client]:
    """Only active clients.
    """
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in clients:
        email(client)
```


**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**안 좋은 예:**

```python
class Email:
    def handle(self) -> None:
        # Do something...

message = Email()
# What is this supposed to do again?
message.handle()
```

**Good:**

```python
class Email:
    def send(self) -> None:
        """Send this message.
        """

message = Email()
message.send()
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction

When you have more than one level of abstraction, your function is usually doing too 
much. Splitting up functions leads to reusability and easier testing.

**안 좋은 예:**

```python
def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = regexes.split()
    tokens = []
    for regex in regexes:
        for statement in statements:
            # ...

    ast = []
    for token in tokens:
        # Lex.

    for node in ast:
        # Parse.
```

**Good:**

```python

REGEXES = (
   # ...
)


def parse_better_js_alternative(code: str) -> None:
    tokens = tokenize(code)
    syntax_tree = parse(tokens)

    for node in syntax_tree:
        # Parse.


def tokenize(code: str) -> list:
    statements = code.split()
    tokens = []
    for regex in REGEXES:
        for statement in statements:
           # Append the statement to tokens.

    return tokens


def parse(tokens: list) -> list:
    syntax_tree = []
    for token in tokens:
        # Append the parsed token to the syntax tree.

    return syntax_tree
```

**[⬆ back to top](#table-of-contents)**

### Don't use flags as function parameters

Flags tell your user that this function does more than one thing. Functions 
should do one thing. Split your functions if they are following different code 
paths based on a boolean.

**안 좋은 예:**

```python
from pathlib import Path

def create_file(name: str, temp: bool) -> None:
    if temp:
        Path('./temp/' + name).touch()
    else:
        Path(name).touch()
```

**Good:**

```python
from pathlib import Path

def create_file(name: str) -> None:
    Path(name).touch()

def create_temp_file(name: str) -> None:
    Path('./temp/' + name).touch()
```

**[⬆ back to top](#table-of-contents)**

### Avoid side effects

A function produces a side effect if it does anything other than take a value in 
and return another value or values. For example, a side effect could be writing 
to a file, modifying some global variable, or accidentally wiring all your money
to a stranger.

Now, you do need to have side effects in a program on occasion - for example, like
in the previous example, you might need to write to a file. In these cases, you
should centralize and indicate where you are incorporating side effects. Don't have
several functions and classes that write to a particular file - rather, have one
(and only one) service that does it.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
or using an instance of a class, and not centralizing where your side effects occur.
If you can do this, you will be happier than the vast majority of other programmers.

**안 좋은 예:**

```python
# This is a module-level name.
# It's good practice to define these as immutable values, such as a string.
# However...
name = 'Ryan McDermott'

def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global name
    name = name.split()

split_into_first_and_last_name()

print(name)  # ['Ryan', 'McDermott']

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**Good:**
```python
def split_into_first_and_last_name(name: str) -> list:
    return name.split()

name = 'Ryan McDermott'
new_name = split_into_first_and_last_name(name)

print(name)  # 'Ryan McDermott'
print(new_name)  # ['Ryan', 'McDermott']
```

**Also good**
```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split() 

# The reason why we create instances of classes is to manage state!
person = Person('Ryan McDermott')
print(person.name)  # 'Ryan McDermott'
print(person.name_as_first_and_last)  # ['Ryan', 'McDermott']
```

**[⬆ back to top](#table-of-contents)**

## **객체와 자료구조**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **클래스**

### **Single Responsibility Principle (SRP)**
### **Open/Closed Principle (OCP)**
### **Liskov Substitution Principle (LSP)**
### **Interface Segregation Principle (ISP)**
### **Dependency Inversion Principle (DIP)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **Don't repeat yourself (DRY)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

