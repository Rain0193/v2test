# V2Test

## Introduction

V2Test is a lightweight data driven testing framework based on Python 3.

Engines:

* [UI] Selenium browser automation test.
* [HTTP] Requests HTTP interface test.
* [Shell] Shell command/script test.
* [MySQL] MySQL database test.

It's easy to develop new engines.

To do:

* More action for MySQL Engine.
* Remote mode. 

![case](https://user-images.githubusercontent.com/1452602/31042295-3eac8c94-a56a-11e7-9f1f-d28d6ca45782.png)
![report](https://user-images.githubusercontent.com/1452602/31042298-425799d8-a56a-11e7-80a4-96e922477a5f.png)
![run](https://user-images.githubusercontent.com/1452602/31042321-de28731e-a56a-11e7-9cc6-97011b86624b.png)

## Prerequisites

Python3 with modules:

openpyxl ddt html-testRunner selenium requests PyMySQL

## Installation

Install Git and Python 3. Homebrew is recommended for macOS.

Then, clone the repo:

```
git clone https://github.com/deepjia/v2test.git
```
For macOS, I recommend pip3 with `--user` so that `sudo` is unnecessary:

```
pip3 install -r requirements.txt --user -U
PATH=$PATH:~/Library/Python/3.6/bin
```

For Linux with Python 3 installed:

```
pip3 install -r requirements.txt
```

For Windows with Python 3 and pip installed:

```
pip install -r requirements.txt
```

## Structure

```
.
├── Cases: Case files(xlsx).
│   └── Example.xlsx: Example for case file.
├── Engines: V2Test engines.
│   ├── __init__.py
│   ├── config.py
│   ├── excel.py
│   ├── http.py
│   ├── linux32: UI driver for 32bit browser, Linux.
│   │   ├── chromedriver
│   │   └── geckodriver
│   ├── linux64: UI driver for 64bit browser, Linux.
│   │   ├── chromedriver
│   │   └── geckodriver
│   ├── mac64: UI driver for macOS.
│   │   ├── chromedriver
│   │   └── geckodriver
│   ├── shell.py
│   ├── ui.py
│   ├── win32: UI driver for 32bit browser, Windows.
│   │   ├── IEDriverServer.exe
│   │   ├── chromedriver.exe
│   │   └── geckodriver.exe
│   └── win64: UI driver for 64bit browser, Windows.
│       ├── IEDriverServer.exe
│       └── geckodriver.exe
├── Files: Test files, for example scripts to run and files to upload.
│   └── example.sh: One example for test file, a shell script.
├── README.md: What you are reading.
├── Reports: Test reports will be generated here.
├── config.ini: Configuration for framwork and engines.
├── requirements.txt: Requirements generated by pip.
└── run.py: V2Test framework, where tests start.
```

## Usage - Framework

### Config

*LOG_LEVEL=INFO or DEBUG or WARNING or ERROR*

For most users, set *LOG_LEVEL=INFO*

### Case

V2Test will scan `Cases` for all excel files, and load all cases with *Run = y* from all sheets.

One line is one step; Cases will be run step by step, and each case can have more than one lines.

*Run, Case ID, Case Name, Engine* should and should only be in the 1st line.

***Run***

Whether or not the case will be run. Set to ***y*** or ***n*** for the 1st line of each case.

(*Required* for each case)

***Case ID, Case Name***

As it is called.

(*Required* for each case)

***Engine***

The engine for the case. 

Engine *ui, http, shell* are now built in. Engine *mysql* is coming soon.

(*Required* for each case)

***Locator/Encapsulator*** and ***Value***

Used to locate elements in web pages or encapsulate parameters.

***Action*** and ***Value***

Used to interact with engines (some with framework), which is also known as "keyword".

### Case - Action

***wait***

Wait for *value* seconds.

***log\[.\*\*\]***

Log the returned.** value.

***equal\[.\*\*\]***

Check whether *Value* equals the returned.** value.

***in\[.\*\*\]***

Check whether *Value* in the returned.** value.

***!equal\[.\*\*\], !in\[.\*\*\]***

"!" means not.
(Coming soon...)

Now you can refer to the examples in `Cases`, it is easy to understand how to create test cases.

All settings are in `config.ini`.

Once finished:

```
./run.py
```
Test report will be generated in `Reports`.

## Usage - UI Engine

### Config

***DRIVER=Safari***

Since OS X El Capitan, [safaridriver](https://webkit.org/blog/6900/webdriver-support-in-safari-10/)(macOS only) is preinstalled.

Keep Safari > Preferences > Show Develop checked.

Keep Develop > Allow Remote Automation checked.

Set *DRIVER=Safari*, *BIT=64* in `config.ini`

***DRIVER=Safari, Firefox, Chrome***

Download [IEDriverServer](http://selenium-release.storage.googleapis.com/index.html) (Windows only), [geckodriver](https://github.com/mozilla/geckodriver/releases), [chromedriver](https://chromedriver.storage.googleapis.com/index.html), then unpack it and put the binary file in  `Engines/.../`

(Current version of binary is already there, but an update is recommended)

Set *DRIVER=IE or Firefox or Chrome*, *BIT=32 or 64* (depend on your browser) in `config.ini` 

***URL***

The default URL if *Value* of *Action=open* is null.

***WAIT*** 

Implicit waits, wait for *Value* seconds before before looking for elements.

### Case - Locator

***id, name, xpath, css_selector, class\_name, tag\_name, link\_text, partial\_link\_text***

Find elements by id, name, xpath etc.

***saved***

Find previously saved elements. (Saved by the *save* action.)

***index, value, visible\_text***

Find options by 

### Case - Action

***open***

Open the URL in *Value*, the default URL is in `config.ini`.

***close***

Close current browser window.

***type***

Input *Value* in text areas.

***click***

Click the element.

***press***

Press Key *Value* of the keyboard.

***[de]select\[.Key]***

Select/Deselect the option by *Key in ('value', 'visible_text', 'index')*


Select/Deselect all when *Key* and *Value* are null.

***waiting***

Explicit waits, wait for the element to appear, up to *Value* seconds.

***save***

Save the element with the name *Value*, in order to be found by locator *saved*.


## Usage - HTTP Engine

### Config

***BASEURL***

*BASEURL* is the base URL for *Value* without base URL, and the default URL for null *Value*

*TIMEOUT* is the default timeout for all requests.

### Case - Encapsulator

***\<headers\>, \</headers\>***

All *Encapsulator: Value* between *\<headers\>* and *\</headers\>* will be encapsulator to *headers={Encapsulator1: Value1, Encapsulator2: Value2, ...}*, and headers will be the parameter.

***\<params\>, \</params\>***

All *Encapsulator: Value* between *\<params\>* and *\</params\>* will be encapsulator to *params={Encapsulator1: Value1, Encapsulator2: Value2, ...}*, and params will be the parameter.

***params, headers, timeout, ...***

Normal parameters.

### Case - Action

***get, post, head, put, delete, options***

Send HTTP requests with parameters to the *Value* URL.

## Usage - Shell Engine

### Case - Action

***cmd***

Run shell command.

***file***

Run shell script.

## Usage - MySQL Engine

### Config

***HOST, USERNAME, PASSWORD, DATABASE, CHARSET***

Basic info of MySQL database.

### Case - Action

***sql***

Run SQL Query.

***commit***

Run SQL Query and commit.

***fetchone.\[\*]***

Run SQL Query and fetch one record, or fetch the value of key * from the record.

***fetchall.\****

Run SQL Query and fetch one records.

***fetchmany.\****

Run SQL Query and fetch as many as * records.