# کد تمیز در پایتون

## فهرست مطالب
  1. [مقدمه](#مقدمه)
  2. [متغیر ها](#متغیر-ها)
  3. [توابع](#توابع)
  5. [کلاس ها](#کلاس-ها)
     * [S: Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
     * [O: Open/Closed Principle (OCP)](#openclosed-principle-ocp)
     * [L: Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
     * [I: Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
     * [D: Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  6. [Don't repeat yourself (DRY)](#dont-repeat-yourself-dry)

## مقدمه

اصول مهندسی نرم افزار، از کتاب [*کد تمیز*](https://www.digikala.com/product/dkp-4964829/%DA%A9%D8%AA%D8%A7%D8%A8-clean-code-a-handbook-of-agile-software-craftsmanship-%D8%A7%D8%AB%D8%B1-robert-c-martin-%D8%A7%D9%86%D8%AA%D8%B4%D8%A7%D8%B1%D8%A7%D8%AA-pearson/) نوشته ی Robert C. Martin، برای پایتون. این یک راهنمای تولید نیست، این یک راهنما برای تولید نرم افزار های خوانا، قابل استفاده مجدد و قابل از نو بازسازی است.
نیازی بر سرسختگیری بر هر اصل گفته شده در اینجا نیست، و فقط تعداد کمی از آنها به صورت عمومی مورد تایید هستند. این ها چیزی جز اصول نیستند، اما اصول کدنویسی شده ای توسط سالها تجربه از نویسنده های *کد تمیز* هستند.
## **متغیر ها**
### از اسم های متغیر با معنا و قابل تلفظ استفاده کنید

**بد:**

```python
import datetime


ymdstr = datetime.date.today().strftime("%y-%m-%d")
```

به علاوه، نیازی به استفاده کردن تایپ `str` برای اسم نیست.

**خوب**:

```python
import datetime


current_date: str = datetime.date.today().strftime("%y-%m-%d")
```
**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از واژگان یکسان برای همان نوع متغیر استفاده کنید
**بد:**
ما در اینجا از چند اسم برای یک موجود استفاده میکنیم:

```python
def get_user_info(): pass
def get_client_data(): pass
def get_customer_record(): pass
```

**خوب**:
اگر موجودیت یکسان است، باید در ارجاع به آن در توابع خود ثابت قدم باشید:

```python
def get_user_info(): pass
def get_user_data(): pass
def get_user_record(): pass
```

**حتی بهتر**

پایتون (همچنین) یک زبان برنامه نویسی شی گرا است. اگر منطقی است، توابع را همراه با پیاده‌سازی مشخص موجودیت در کد خود، به‌عنوان ویژگی‌های نمونه، متدهای ویژگی یا متدها بسته‌بندی کنید

```python
from typing import Union, Dict


class Record:
    pass


class User:
    info : str

    @property
    def data(self) -> Dict[str, str]:
        return {}

    def get_record(self) -> Union[Record, None]:
        return Record()
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از اسامی قابل جست و جو استفاده کنید
ما بیشتر کد میخوانیم تا بنویسیم. این خیلی مهم است که کدی که مینویسیم خوانا و قابل جستجو باشد. با نام گذاری *نکردن* متغیر هایی که برای برنامه قابل فهم باشد، به خواننده هایمان آسیب میرسانیم. اسامی متغیرهایتان را قابل جست و جو کنید.

**بد:**

```python
import time


# What is the number 86400 for again?
time.sleep(86400)
```

**خوب**:

```python
import time


# Declare them in the global namespace for the module.
SECONDS_IN_A_DAY = 60 * 60 * 24
time.sleep(SECONDS_IN_A_DAY)
```
**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از متغیر های توضیحی استفاده کنید
**بد:**

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches[1]}: {matches[2]}")
```

**بد نیست**:

بهتر است، اما هنوز به شدت به رجکس وابسته ایم.
```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"
matches = re.match(city_zip_code_regex, address)

if matches:
    city, zip_code = matches.groups()
    print(f"{city}: {zip_code}")
```

**خوب**:


وابستگی به رجکس را با نام گذاری الگوهای فرعی کمتر کنید.

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches['city']}, {matches['zip_code']}")
```
**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از نقشه برداری ذهنی خودداری کنید

خواننده ی کدتان را مجبور نکنید که اسامی متغیرهای شمارا ترجمه کنند.
صریح بهتر از ضمنی است.

**بد:**

```python
seq = ("Austin", "New York", "San Francisco")

for item in seq:
    #do_stuff()
    #do_some_other_stuff()

    # Wait, what's `item` again?
    print(item)
```

**خوب**:

```python
locations = ("Austin", "New York", "San Francisco")

for location in locations:
    #do_stuff()
    #do_some_other_stuff()
    # ...
    print(location)
```
**[⬆ برگشت به بالا](#فهرست-مطالب)**


### توضیحات اضافی ننویسید

اگر اسم کلاس/شیء شما چیزی را میگوید، نیازی نیست که آنرا در اسامی متغیر هایتان تکرار کنید.

**بد:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**خوب**:

```python
class Car:
    make: str
    model: str
    color: str
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از آرگومان های پیشفرض به جای شروط یک خطی استفاده کنید.

**بد**

چرا بنویسیم :

```python
import hashlib


def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

...زمانی که میتوانید از یک آرگومان پیشفرض استفاده کنید ؟ این همچنین خواننده را کاملا روشن میکند که شما برای آرگومانتان یک رشته میخواهید.

**خوب**:

```python
import hashlib


def create_micro_brewery(name: str = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### آرگومان های توابع (ترجیحاً دو یا کمتر)

کم کردن مقادیر پارامتر های توابع بسیار مهم است چون تست کردن آنهارا راحت تر میکند. بیشتر از سه تا منجر به انفجاری ترکیبی میشود چون باید حالت های مختلف را با آرگومان های متفاوت تست کنید.

آرگومان نداشتن حالت آیده آل است. یکی یا دوتا خوب است، و باید از سه تا اجتناب کرد. 
هرچیزی بیشتر از آن باید یک رقم شود. معمولا اگر بیشتر از دو آرگومان دارید، کد شما دارد چند کار را باهم انجام میدهد. در شرایطی که اینگونه نیست، اغلب اوقات یک شی سطح بالاتر به عنوان یک آرگومان کافی است.

**بد:**

```python
def create_menu(title, body, button_text, cancellable):
    pass
```

**به سبک جاوا**:

```python
class Menu:
    def __init__(self, config: dict):
        self.title = config["title"]
        self.body = config["body"]
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

**این هم خوب است**

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


def create_menu(config: MenuConfig) -> None:
    title = config.title
    body = config.body
    # ...


config = MenuConfig()
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**تفننی**

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

**تفننی تر**

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

**باز هم تفننی تر، فقط پایتون 3.8+**

```python
from typing import TypedDict


class MenuConfig(TypedDict):
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
    cancellable: bool


def create_menu(config: MenuConfig):
    title = config["title"]
    # ...


create_menu(
    # You need to supply all the parameters
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!",
        cancellable=True
    )
)
```
**[⬆ برگشت به بالا](#فهرست-مطالب)**

## **توابع**
### توابع فقط باید یک کار انجام دهند.
این مهم ترین قانون در طراحی نرم افزار است. وقتی توابع بیشتر از یک کار انجام میدهند، سخت تر میتوان آنهارا نوشت و تست و استدلال کرد. وقتی که یک تابع را فقط به یک رفتار ایزوله میکنید، به راحتی میتوان آنهارا از نو نوشت و کدتان بسیار تمیزتر خواهد بود. اگر فقط همین مورد را از این راهنما یاد بگیرید، از بسیاری از برنامه نویس ها جلوتر خواهید بود.

**بد:**

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def email_clients(clients: List[Client]) -> None:
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**خوب**:

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in get_active_clients(clients):
        email(client)
```

آیا اکنون فرصتی برای استفاده از چنریتور ها می بینید؟

**حتی بهتر**

```python
from typing import Generator, Iterator


class Client:
    active: bool


def email(client: Client):
    pass


def active_clients(clients: Iterator[Client]) -> Generator[Client, None, None]:
    """Only active clients"""
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in active_clients(clients):
        email(client)
```


### اسامی توابع باید کاری که انجام میدهند را بگویند.

**بد:**

```python
class Email:
    def handle(self) -> None:
        pass

message = Email()
# What is this supposed to do again?
message.handle()
```

**خوب:**

```python
class Email:
    def send(self) -> None:
        """Send this message"""

message = Email()
message.send()
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### توابع باید فقط یک مرحله از انتزاع داشته باشند.

وقتی که کدتان بیشتر از یک مرحله انتزاع دارد، کدتان دارد زیاد کار انجام میدهد. تکه تکه کردن توابع باعث خوانایی کد و تست نویسی آسان تر میشود.

**بد:**

```python
# type: ignore

def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = code.split('\n')
    tokens = []
    for regex in regexes:
        for statement in statements:
            pass

    ast = []
    for token in tokens:
        pass

    for node in ast:
        pass
```

**خوب:**

```python
from typing import Tuple, List, Dict


REGEXES: Tuple = (
   # ...
)


def parse_better_js_alternative(code: str) -> None:
    tokens: List = tokenize(code)
    syntax_tree: List = parse(tokens)

    for node in syntax_tree:
        pass


def tokenize(code: str) -> List:
    statements = code.split()
    tokens: List[Dict] = []
    for regex in REGEXES:
        for statement in statements:
            pass

    return tokens


def parse(tokens: List) -> List:
    syntax_tree: List[Dict] = []
    for token in tokens:
        pass

    return syntax_tree
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از فلگ ها برای پارامتر های تابعتان استفاده نکنید.

فلگ ها به کاربر ها میگویند که این تابع بیشتر از یک کار انجام میدهد. توابع باید یک کار انجام دهند. توابعتان را تکه تکه کنید اگر کدهای متفاوتی بر حسب یک بولین هستند.

**بد:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str, temp: bool) -> None:
    if temp:
        (Path(gettempdir()) / name).touch()
    else:
        Path(name).touch()
```

**خوب:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str) -> None:
    Path(name).touch()


def create_temp_file(name: str) -> None:
    (Path(gettempdir()) / name).touch()
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

### از تاثیرات جانبی خودداری کنید

یک تابع هنگامی اثر جانبی دارد که کاری بجز گرفتن یک مقدار و برگرداندن مقدار یا مقدار هایی دیگر انجام دهد. برای مثال، یک اثر جانبی میتواند نوشتن به یک فایل، تغییر دادن متغیر های گلوبال و یا انتقال تمامی اموالتان به یک غریبه باشد.
حالا، بعضی اوقات نیاز است که برنامه شما اثرات جانبی داشته باشد - برای مثال، همانند مثال قبلی، نیاز داشته باشید که به یک فایل بنویسید. در این شرایط، شما باید متمرکز باشید و مشخص کنید که در کجا اثرات جانبی ایجاد میکنید. چندین تابع و کلاس نداشته باشید که به یک فایل مینویسند، بلکه یک و فقط یک سرویس داشته باشید که تمامی این کارها را انجام میدهد. 
نکته اصیل اجتناب از دام های رایج مثل تبادل وضعیت بین چندین شیء بدون هیچ ساختمان، استفاده از دیتاتایپ های قابل تغییر که هرچیزی بتواند به آن بنویسد یا استفاده کردن نمونه ای از یک کلاس به جای متمرکز کردن مکان هایی که اثرات جانبی خواهید داشت. 
اگر بتوانید این کار را انجام دهید، از بیشتر برنامه نویس ها خوشحال تر خواهید بود.


**بد:**

```python
# type: ignore

# This is a module-level name.
# It's good practice to define these as immutable values, such as a string.
# However...
fullname = "Ryan McDermott"

def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global fullname
    fullname = fullname.split()

split_into_first_and_last_name()

# MyPy will spot the problem, complaining about 'Incompatible types in
# assignment: (expression has type "List[str]", variable has type "str")'
print(fullname)  # ["Ryan", "McDermott"]

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**خوب:**

```python
from typing import List, AnyStr


def split_into_first_and_last_name(name: AnyStr) -> List[AnyStr]:
    return name.split()

fullname = "Ryan McDermott"
name, surname = split_into_first_and_last_name(fullname)

print(name, surname)  # => Ryan McDermott
```
**همچنین خوب :**

```python
from dataclasses import dataclass


@dataclass
class Person:
    name: str

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split()


# The reason why we create instances of classes is to manage state!
person = Person("Ryan McDermott")
print(person.name)  # => "Ryan McDermott"
print(person.name_as_first_and_last)  # => ["Ryan", "McDermott"]
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**

## **کلاس ها**

### **Single Responsibility Principle (SRP)**

رابرت سی. مارتین مینویسد :

> یک کلاس باید فقط یک دلیل برای تغییر داشته باشد.

"  دلایل تغییر " ذاتاً مسئولیت هایی است که توسط یک کلاس یا تابع مدیریت میشود.

در مثال های زیر، ما یک عنصر HTML درست میکنیم که را میسازیم که یک کامنت و ورژن را نشان میدهد.

**بد :**
```python
from importlib import metadata


class VersionCommentElement:
   """An element that renders an HTML comment with the program's version number
   """

   def get_version(self) -> str:
       """Get the package version"""
       return metadata.version("pip")
   
   def render(self) -> None:
       print(f'<!-- Version: {self.get_version()} -->')


VersionCommentElement().render()
```
این کلاس دو وظیفه دارد:
* گرفتن نسخه ی پکیج پایتون
* رندر کردن آن به یک عنصر HTML

هر تغییری در یکی از آن ها، ریسک تاثیر گذاشتن روی آن یکی را ایجاد میکند.
ما میتوانیم کلاس را از نو بنویسیم و مسئولیت ها را جدا کنیم.

**خوب :**
```python
from importlib import metadata


def get_version(pkg_name:str) -> str:
    """Retrieve the version of a given package"""
    return metadata.version(pkg_name)


class VersionCommentElement:
   """An element that renders an HTML comment with the program's version number
   """

   def __init__(self, version: str):
       self.version = version
   
   def render(self) -> None:
       print(f'<!-- Version: {self.version} -->')


VersionCommentElement(get_version("pip")).render()
```
نتیجه این خواهد بود که کلاس فقط باید رندر    کردن را به عهده بگیرد. ورژن را هنگام نمونه گیری دریافت میکند و متن توسط تابعی جدا به نام `get_version()` گرفته میشود. تغییر دادن کلاس هیچ تاثیری روی آن یکی ندارد، و بالعکس، تا زمانی که قرارداد های بینشان تغییر نکند، یعنی فانکشن یک متن برگرداند و متود `__init__` کلاس یک رشته را دریافت کند.

و همینطور، تابع `get_version()` قابل استفاده در همه جا است.

### **Open/Closed Principle (OCP)**

> “ قابلیت های جدید را با گسترش دادن ایجاد کنید، نه با تغییر دادن(آن). “ Uncle Bob.

اشیاء باید برای گسترش دادن دردسترس باشند، اما بسته برای تغییرات. باید امکان تقویت عملکرد ارائه شده توسط یک شیء (برای مثال، یک کلاس) بدون تغییر دادن قرارداد های داخلی آن باشد. یک شیء میتواند این قابلیت را هنگامی ایجاد کند که طوری نوشته شده باشد که اجازه اینکار را به راحتی بدهد.

در مثال بعدی، تلاش میکنیم یک فریم ورک وب ساده پیاده سازی کنیم که درخواست های HTTP را هندل و پاسخی را برمیگرداند. کلاس `View` یک متد `.get()` دارد که زمانی صدا زده خواهد شد که سرور HTTP یک درخواست GET دریافت کند.

کلاس `View` به طور قصد ساده است و پاسخ های `text/plain` برمیگرداند. ما همچنین پاسخ هایی بر پایه فایل های تمپلیت میخواهیم پس با کلاس `TemplateView` از آن ارث بری میکنیم.

**بد:**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type='text/plain',
            body="Welcome to my web site"
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    def get(self, request) -> Response:
        """Handle a GET request and return an HTML document in the response"""
        with open("index.html") as fd:
            return Response(
                status=200,
                content_type='text/html',
                body=fd.read()
            )

```

کلاس `TemplateView` رفتار های داخلی والدش را تغییر داده تا بتواند عملکرد های پیشرفته تر اضافه کند. با اینکار، کلاس `TemplateView` به والدش تکیه میکند که عملکرد `.get()` تغییر نکند، که حالا باید در زمان منجمد شود. برای مثال، نمی‌توانیم برخی بررسی‌های اضافی را در تمام کلاس‌های مشتق از `View` معرفی کنیم، زیرا این رفتار حداقل در یک نوع فرعی لغو شده است و ما باید آن را به‌روزرسانی کنیم.  

بیایید کلاس های خود را از نو طراحی کنیم تا این مشکل حل شود و اجازه دهیم تا کلاس `View` به تمیزی گسترش (نه تغییر) داده شود.

**خوب :**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"
    template_file = "index.html"

    def render_body(self) -> str:
        """Render the message body as HTML"""
        with open(self.template_file) as fd:
            return fd.read()


```
توجه داشته باشید که نیاز داشتیم `render_body()` را از نو نویسی کنیم تا منبع بدنه را عوض کنیم، اما این کلاس، تنها یک مسئولیت به درستی تعیین شده دارد که **کلاس های فرعی را دعوت میکند که آن را از نو نویسی کنند**. جوری ساخته شده است که اجازه ی گسترش داده شدن توسط کلاس های فرعی را بدهد.
یک راه خوب دیگر برای استفاده از قدرت های ارث بری و ترکیب اشیاء استفاده از [Mixins](https://docs.djangoproject.com/en/4.1/topics/class-based-views/mixins/) ها است.

میکسین ها کلاس های ریشه و ساده ای هستند که به طور انحصاری با سایر کلاس های مرتبط استفاده می شوند. آنها با کلاس هدف با استفاده از وراثت چندگانه "مخلوط" می شوند تا رفتار هدف را تغییر دهند.

چند قانون :
 - میکسین ها باید از `object` ارث بری کنند.
 - میکسین ها همیشه قبل از کلاس هدف میایند. برای مثال : 

`class Foo(MixinA, MixinB, TargetClass): ...`

**همچنین خوب :**
```python
from dataclasses import dataclass, field
from typing import Protocol


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str
    headers: dict = field(default_factory=dict)


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateRenderMixin:
    """A mixin class for views that render HTML documents using a template file

    Not to be used by itself!
    """
    template_file: str = ""

    def render_body(self) -> str:
        """Render the message body as HTML"""
        if not self.template_file:
            raise ValueError("The path to a template file must be given.")

        with open(self.template_file) as fd:
            return fd.read()


class ContentLengthMixin:
    """A mixin class for views that injects a Content-Length header in the
    response

    Not to be used by itself!
    """

    def get(self, request) -> Response:
        """Introspect and amend the response to inject the new header"""
        response = super().get(request) # type: ignore
        response.headers['Content-Length'] = len(response.body)
        return response


class TemplateView(TemplateRenderMixin, ContentLengthMixin, View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"
    template_file = "index.html"

```

همانطور که میبینید، میکسین ها ترکیب اشیاء را، با بسته بندی کردن عملکرد های مشابه و تبدیل آنها به یک کلاس قابل استفاده دوباره با یک مسئولیت و اجازه جدا کردن آنها، آسان میکنند.
فریم ورک مشهور جنگو استفاده زیادی از میکسین ها میکند تا ویو های مبتنی بر کلاس خود را بسازد.

### **Liskov Substitution Principle (LSP)**

> “ توابعی که از اشاره گر ها یا ارجاع ها به کلاس های پایه استفاده میکنند باید بتوانند اشیاء کلاس ارث بری شده را بدون دانستن استفاد کنند. “ Uncle Bob.

این اصل به افتخار Barbara Liskov نامگذاری شده، که با دانشمند کامپیوتر دیگری به نام Jeannette Wing در مقاله ی "A behavioral notion of subtyping (1994)" همکاری کرده است. 
یک اصل اصلی مقاله این است که " یک نوع فرعی باید رفتار متود های نوع اصلی خود را حفظ و همچنین تمام ویژگی‌های تغییرناپذیر و تاریخی نوع اصلی خود را نگه دارد.

این به این معناست که، یک تابع که یک نوع اصلی را قبول میکند باید همچنین نوع های فرعی آن را بدون هیچ تغیری قبول کند.

میتوانید مشکل کد زیر را پیدا کنید ؟

**بد :**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"

    def get(self, request, template_file: str) -> Response: # type: ignore
        """Render the message body as HTML"""
        with open(template_file) as fd:
            return Response(
                status=200,
                content_type=self.content_type,
                body=fd.read()
            )


def render(view: View, request) -> Response:
    """Render a View"""
    return view.get(request)

```

انتظار میرود که فانکشن `render()` به خوبی بتواند با `View` و زیرکلاسش `TemplateView`  کار کند، اما زیرکلاسش این سازگاری را با تغییر متود `.get()` از بین برده است. 
تابع خطای `TypeError` را هنگامی که با `TemplateView` استفاده شود برمیگرداند.

اگر بخواهیم که تابع `render()` با هر زیرنوعی از `View` کار کند، باید توجه کنیم که پروتوکل عمومی آن را نشکنیم. اما از کجا بدانیم که چه چیزی یک کلاس را تشکیل میدهد؟ Type Hinter هایی مثل *mypy*  اگر اشتباهاتی مثل این ببینند، اخطار هایی به این شکل برمیگردانند :
```
error: Signature of "get" incompatible with supertype "View"
<string>:36: note:      Superclass:
<string>:36: note:          def get(self, request: Any) -> Response
<string>:36: note:      Subclass:
<string>:36: note:          def get(self, request: Any, template_file: str) -> Response
```

### **Interface Segregation Principle (ISP)**

> اینترفیس ها را کوچک نگه دارید تا کاربر ها نیاز  وابسته به چیزهایی که از آنها بی نیازند نشوند. Uncle Bob.

چندین زبان برنامه نویسی مشهور شیء گرا مثل Go و Java مفهمومی به نام اینترفیس ها دارند. اینترفیس ها متود های عمومی و ویژگی های یک شیء را بدون پیاده سازی آن تعریف میکند. آنها زمانی مفید هستند که نمی خواهیم امضای یک تابع را با یک شیء مشخص مرتبط کنیم.
پایتون اینترفیس ندارد. اما به جایش Abstract Base Clase دارد که کمی متقاوت هستند، اما میتوانند همانکار را انجام دهند.

**خوب**
```python

from abc import ABCMeta, abstractmethod


# Define the Abstract Class for a generic Greeter object
class Greeter(metaclass=ABCMeta):
    """An object that can perform a greeting action."""

    @staticmethod
    @abstractmethod
    def greet(name: str) -> None:
        """Display a greeting for the user with the given name"""


class FriendlyActor(Greeter):
    """An actor that greets the user with a friendly salutation"""

    @staticmethod
    def greet(name: str) -> None:
        """Greet a person by name"""
        print(f"Hello {name}!")


def welcome_user(user_name: str, actor: Greeter):
    """Welcome a user with a given name using the provided actor"""
    actor.greet(user_name)


welcome_user("Barbara", FriendlyActor())
```
حالا این سناریو را در نظر بگیرید : میخواهیم تعداد مشخصی سند PDF داشته باشیم که ما نوشتیم و میخواهیم به بازدید کننده های وبسایتمان داده شوند. ما از یک وب فریمورک پایتون استفاده میکنیم و شاید وسوسه شویم که کلاسی را طراحی کنیم که این اسناد را مدیریت کند، پس یک کلاس پایه انتزاعی جامع برای سند خود طراحی می کنیم.

**خطا**
```python
import abc


class Persistable(metaclass=abc.ABCMeta):
    """Serialize a file to data and back"""

    @property
    @abc.abstractmethod
    def data(self) -> bytes:
        """The raw data of the file"""

    @classmethod
    @abc.abstractmethod
    def load(cls, name: str):
        """Load the file from disk"""

    @abc.abstractmethod
    def save(self) -> None:
        """Save the file to disk"""


# We just want to serve the documents, so our concrete PDF document
# implementation just needs to implement the `.load()` method and have
# a public attribute named `data`.

class PDFDocument(Persistable):
    """A PDF document"""

    @property
    def data(self) -> bytes:
        """The raw bytes of the PDF document"""
        ... # Code goes here - omitted for brevity

    @classmethod
    def load(cls, name: str):
        """Load the file from the local filesystem"""
        ... # Code goes here - omitted for brevity


def view(request):
    """A web view that handles a GET request for a document"""
    requested_name = request.qs['name'] # We want to validate this!
    return PDFDocument.load(requested_name).data

```
اما نمیتونیم! اگر متود `.save()` را پیاده سازی نکنیم یک خطا به ما نشان داده میشود :

```
Can't instantiate abstract class PDFDocument with abstract method save.
```
این اعصاب خورد کن است. ما واقعا نیازی به پیاده سازی `.save()` در اینجا نداریم. میتوانیم یک متود بنویسیم که هیچ کاری نکند یا خطای `NotImplementedError` را برگرداند اما این فقط کد اضافی ای است که باید مراقبش باشیم.

در عین حال، اگر `.save()` را از کلاس انتزاعی حذف کنیم نیاز خواهیم داشت بعدا برای اینکه کاربران بتوانند اسنادشان را ارسال کنند از نو پیاده سازی کنیم که دوباره مارا به خانه ی اول برمیگرداند.

مشکل اینجاست که ما یک *اینترفیس* نوشتیم که قابلیت هایی دارد که ما در حال حاظر به آنها یا نیاز نداریم یا استفاده نمیکنیم.

راه حل این است که اینترفیست را به قطعات کوچک تر که کارهای جدا میکنند تکه تکه کنیم.

**خوب**
```python
import abc


class DataCarrier(metaclass=abc.ABCMeta):
    """Carries a data payload"""
    @property
    def data(self):
        ...

class Loadable(DataCarrier):
    """Can load data from storage by name"""
    @classmethod
    @abc.abstractmethod
    def load(cls, name: str):
        ...

class Saveable(DataCarrier):
    """Can save data to storage"""
    @abc.abstractmethod
    def save(self) -> None:
        ...


class PDFDocument(Loadable):
    """A PDF document"""

    @property
    def data(self) -> bytes:
        """The raw bytes of the PDF document"""
        ... # Code goes here - omitted for brevity

    @classmethod
    def load(cls, name: str):
        """Load the file from the local filesystem"""
        ... # Code goes here - omitted for brevity


def view(request):
    """A web view that handles a GET request for a document"""
    requested_name = request.qs['name'] # We want to validate this!
    return PDFDocument.load(requested_name).data

```

### **Dependency Inversion Principle (DIP)**

> “به انتزاع وابسته شوید، نه جزئیات مشخص.”,
> Uncle Bob.

تصور کنید میخواستیم که یک وب ویو درست کنیم که پاسخ HTTP ای را برمیگرداند که ردیف هایی از فایل CSV ای که ساختیم برمیگراند. میخواهیم از نویسنده ی CSV ای استفاده کنیم که توسط کتابخانه ی استاندارد پایتون ارائه میشود.

**بد**
```python
import csv
from io import StringIO


class StreamingHttpResponse:
    """A streaming HTTP response"""
    ... # implementation code goes here


def some_view(request):
   rows = (
       ['First row', 'Foo', 'Bar', 'Baz'],
       ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
   )
   # Define a generator to stream data directly to the client
   def stream():
       buffer_ = StringIO()
       writer = csv.writer(buffer_, delimiter=';', quotechar='"')
       for row in rows:
           writer.writerow(row)
           buffer_.seek(0)
           data = buffer_.read()
           buffer_.seek(0)
           buffer_.truncate()
           yield data

   # Create the streaming response  object with the appropriate CSV header.
   response = StreamingHttpResponse(stream(), content_type='text/csv')
   response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

   return response

```
اولین پیاده‌سازی ما حول رابط نویسنده CSV با دستکاری یک شی `StringIO` (که شبه فایل است) و انجام چندین عملیات سطح پایین به منظور جداسازی ردیف‌ها از نویسنده کار می‌کند.

یک راه بهتر این است که از این واقعیت استفاده کنیم که نویسنده به یک شی با متد `.write()‍‍` برای انجام کار ما نیاز دارد.
چرا یک شی ساختگی که بلافاصله ردیف جدید ساخته شده را برمی گرداند، به آن منتقل نکنیم تا کلاس ‍`StreamingHttpResponse` بتواند بلافاصله آن را به کلاینت بازگرداند؟

**خوب**
```python
import csv


class Echo:
   """An object that implements just the write method of the file-like
   interface.
   """
   def write(self, value):
       """Write the value by returning it, instead of storing in a buffer."""
       return value

def some_streaming_csv_view(request):
   """A view that streams a large CSV file."""
   rows = (
       ['First row', 'Foo', 'Bar', 'Baz'],
       ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
   )
   writer = csv.writer(Echo(), delimiter=';', quotechar='"')
   return StreamingHttpResponse(
       (writer.writerow(row) for row in rows),
       content_type="text/csv",
       headers={'Content-Disposition': 'attachment; filename="somefilename.csv"'},
   )

```
خیلی بهتر شد، و مثل جادو کار میکند! دلیل اینکه پیاده سازی آن بهتر از قبلیست باید واضخ باشد: کد کمتر و بازدهی بیشتر برای رسیدن به پاسخ یکسان. ما تصمیم گرفتیم که از واقعیت این که کلاس نویسنده به انتزاع `.write()` از شیء ای که دریافت میکند وابسته است، بدون اینکه به جزئیات سطح پایین عملکرد متود توجه ای کند.
این مثال از [ارسالی به مستندات جنگو](https://code.djangoproject.com/ticket/21179) توسط این نویسنده گرفته شده است.

**[⬆ برگشت به بالا](#فهرست-مطالب)**

## **Don't repeat yourself (DRY)**

تلاش کنید که اصل [DRY](https://fa.wikipedia.org/wiki/Don%27t_repeat_yourself) را متوجه بشوید.

بهترین تلاشتان را بکنید که کدی را دوباره ننویسید. تکرار کد بد است چون به این معنیست که هنگام ایجاد تغییر، باید بیشتر از دو جا را تغییر دهید.

تصور کنید که صاحب رستورانی هستید و میخواهید آمار انبارتان را داشته باشید: تمامی گوجه ها، پیاز ها، سیر ها، ادویه ها و... . اگر چندین لیست داشته باشید که آمار اینهارا ثبت کنند، هروقت که غذایی با گوجه سرو کنید باید تمامی این لیست هارا آپدیت کنید. اما اگر فقط یک لیست داشته باشید، فقط یک جا را باید آپدیت کنید!

بیشتر اوقات شما وقتی کد را چندین بار تکرار میکنید که دو چیز یا بیشتر کمی متفاوت داشته باشید، که بسیار شبیه به هم باشند، اما این شباهتشان شمارا به داشتن دو تابع یا بیشتر مجبور میکنند که اکثراً یک کار را انجام میدهند. حذف کردن کد های تکراری به معنی ساختن انتزاعی است که بتواند این کارهارا با یک تابع/ماژول/کلاس انجام دهد.

درست ساختن انتزاع مهم است. انتزاع بد میتواند از کد تکراری هم بدتر باشد! با این، اگر میتوانید یک انتزاع خوب بسازید، انجامش دهید! خودتان را تکرار نکنید، وگرنه مجبور میشوید چندین جا را برای تغییر کوچکی آپدیت کنید.

**بد:**

```python
from typing import List, Dict
from dataclasses import dataclass

@dataclass
class Developer:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    
@dataclass
class Manager:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    

def get_developer_list(developers: List[Developer]) -> List[Dict]:
    developers_list = []
    for developer in developers:
        developers_list.append({
        'experience' : developer.experience,
        'github_link' : developer.github_link
            })
    return developers_list

def get_manager_list(managers: List[Manager]) -> List[Dict]:
    managers_list = []
    for manager in managers:
        managers_list.append({
        'experience' : manager.experience,
        'github_link' : manager.github_link
            })
    return managers_list

## create list objects of developers
company_developers = [
    Developer(experience=2.5, github_link='https://github.com/1'),
    Developer(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_developer_list(developers=company_developers)

## create list objects of managers
company_managers = [
    Manager(experience=4.5, github_link='https://github.com/3'),
    Manager(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_manager_list(managers=company_managers)
```

**خوب:**

```python
from typing import List, Dict
from dataclasses import dataclass

@dataclass
class Employee:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    


def get_employee_list(employees: List[Employee]) -> List[Dict]:
    employees_list = []
    for employee in employees:
        employees_list.append({
        'experience' : employee.experience,
        'github_link' : employee.github_link
            })
    return employees_list

## create list objects of developers
company_developers = [
    Employee(experience=2.5, github_link='https://github.com/1'),
    Employee(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_employee_list(employees=company_developers)

## create list objects of managers
company_managers = [
    Employee(experience=4.5, github_link='https://github.com/3'),
    Employee(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_employee_list(employees=company_managers)
```

**[⬆ برگشت به بالا](#فهرست-مطالب)**
