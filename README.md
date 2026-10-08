# Python for SAP Datasphere – Step by Step

A beginner's book for the script operator in data flows

Oct 6, 2026 · @chesterrush

## Preface

This book takes you from zero Python knowledge to your first working script operator in SAP Datasphere. You need no programming experience. You only need curiosity and a little patience.

### Who is this book for?

It is for anyone who builds data flows in SAP Datasphere and has to use a script operator there. Maybe you come from SAP BW and know ABAP routines, transformations and InfoObjects. Maybe you come from a business department and know Excel very well. Both are a good foundation.

This book does not cover all of Python. We learn only what you really need in the script operator. Everything else is deliberately left out.

### What you can do at the end

- You understand what happens in the script operator and when it makes sense to use it.
- You can read Python code and write small programs yourself.
- You can select, calculate and rename columns and filter rows.
- You can handle text, dates, decimal numbers and missing values safely.
- You know the limits of the operator and how to find errors.

### How to read this book

Read the chapters in order. Each chapter builds on the previous one. The chapters are short, so you can finish one during a coffee break.

Type every example yourself. Copying is faster, but typing makes it stick. Each chapter ends with a short key takeaway.

### What examples look like

Code is always shown in a grey box:

```python
print("Hello Datasphere")
```

When it matters what comes out, the result is shown as a comment with `#` below it:

```python
print(2 + 3)
# Output: 5
```

### Where to practise

In the script operator itself you do not see the output of `print`. A local environment is therefore useful for practising. Good options are:

1. **Jupyter Notebook** or **JupyterLab** on your computer (via Anaconda or pip).
2. **Visual Studio Code** with the Python extension.
3. An online notebook, if your company allows it. Never use real company data there.

Locally you have to load pandas and NumPy once yourself. In Datasphere they are already there. More on this in Chapter 2.

### A note on names

Column names in the examples are written in capitals, like `NET` or `COUNTRY`. Where SAP fields appear, they keep their original German SAP abbreviations, such as `BUDAT` (posting date) or `SHKZG` (debit/credit indicator). The meaning is explained where they first appear. A German edition of this book with the same content is also available.

> Key takeaway: Small steps, type it yourself, look at every result.

## Chapter 1: What is Python – and why use it in Datasphere?

Python is a programming language that reads almost like English. It is one of the most popular languages for working with data. In SAP Datasphere you use it to reshape data inside a data flow.

### A program is a set of instructions

Think of a recipe. It tells you step by step what to do. A program is exactly that: a set of instructions for the computer.

The computer is very obedient, but also very literal. It does exactly what is written. One missing character is enough, and it understands nothing.

### Why Python?

- **Easy to read:** Python code looks tidy and needs few special characters.
- **Strong with data:** With the **pandas** library you work with tables, much like in Excel.
- **Fast with numbers:** The **NumPy** library calculates efficiently with many values at once.
- **Widely used:** You can find an answer to almost any question online.

### What is a library?

A library is a ready-made toolbox. Other people have collected useful functions in it. You do not have to build them yourself, you just use them.

In Datasphere you have two toolboxes:

| Library | Short name | Used for |
| --- | --- | --- |
| pandas | `pd` | Working with tables (columns, rows, filters) |
| NumPy | `np` | Calculating with many numbers, conditions on columns |

### Where Python appears in Datasphere

In Datasphere you build a **data flow**. It reads data from a source, changes it and writes it to a target table. For this there are graphical building blocks called operators:

- **Join** combines two tables.
- **Union** appends tables below each other.
- **Projection** selects columns, filters and calculates.
- **Aggregation** summarises.
- **Script** runs your own Python code.

### When do I need the script operator?

Use the graphical operators first if they are enough. They are easier to maintain. The script operator is worth it when the logic becomes too complicated there, for example:

- Splitting, cleaning or searching text for patterns.
- Multi-level if-then rules, as you know them from BW routines.
- Converting dates into special formats.
- Deriving or classifying values with your own logic.

There is also a technical reason. Graphical operators run directly in the database. With the script operator, the data is transferred to a separate compute environment, processed with Python there and sent back. With large data volumes this costs noticeable time ([field report by zpartner](https://www.zpartner.eu/data-flows-the-python-script-operator-and-why-you-should-avoid-it/)).

### Comparison with SAP BW

If you come from BW, this comparison helps:

| SAP BW | SAP Datasphere |
| --- | --- |
| Transformation | Data flow |
| Start/end routine in ABAP | Script operator in Python |
| `RESULT_PACKAGE` | the DataFrame `data` |
| Data package | Batch (a subset of the rows) |

The idea is the same: a package of data comes in, you change it, the result moves on.

> Key takeaway: Graphical operators first. Python when the logic gets too complex.

## Chapter 2: The script operator in a data flow

The script operator receives a table, runs your Python code on it and passes a table on. Nothing more happens there. Once you have this picture in mind, the rest is craftsmanship.

### The picture: input – function – output

1. The data from the previous operators arrives at the **input**.
2. Datasphere turns it into a pandas table, a **DataFrame**. It is called `data`.
3. Your function `transform` receives `data`, changes it and returns a DataFrame.
4. Datasphere sends the result to the **output** and on to the next operator.

### The default script

When you add the operator, a script is already there. It looks like this:

```python
def transform(data):
    return data
```

This means: “Take the data and return it unchanged.” It is only a starting point. You replace the `return` line with your own logic.

You will learn exactly what `def` and `return` mean in Chapter 8. For now this is enough: your work happens between these two lines.

### Step by step: adding the operator

1. Open your data flow in the **Data Builder**.
2. Drag the **Script** operator from the toolbar onto the canvas.
3. Connect the output of the previous operator to the input of the script operator.
4. In the **Columns** section of the properties panel, define which columns come out at the output (via the **+** button). This is the **output schema**.
5. Open the script editor and write your code in the `transform` function.
6. Connect the output of the script operator to the next operator or the target table.
7. Save, deploy and run the data flow.

The exact menu names may change slightly with new releases. The principle stays the same.

### The output schema is a contract

The output schema is the list of columns with their names and data types. Your code must deliver exactly these columns. Same names, matching types.

If they do not match, the run fails. This is the most common mistake at the beginning. Chapter 16 is dedicated entirely to this topic.

### The sandbox: a closed room

Your code runs in a **sandbox**. This is a closed-off area. It protects the system from dangerous code. For you this means:

- **No `import`.** You cannot load any further libraries.
- **No files.** Reading and writing files is blocked.
- **No network.** You cannot call websites or interfaces.
- **No classes and no asynchronous programming.** You do not need either here.

In return, some things are already available without loading them:

| Name | What it is |
| --- | --- |
| `pd` | pandas |
| `np` | NumPy |
| `Decimal` | decimal numbers for exact amounts |

The operator runs on **Python 3.11**.

### Practise locally, paste into Datasphere

On your own computer these names do not exist automatically. There you write at the top of the file:

```python
import pandas as pd
import numpy as np
from decimal import Decimal
```

Important: you do **not** copy these three lines into Datasphere. There, `import` is forbidden and the names already exist. Copy only the `transform` function.

One more note: SAP states Python 3.11 for the operator, but no pandas version. Locally you may have a newer pandas version installed. It behaves differently in small details. Where this matters for this book, the text says so. Always test new code once in Datasphere itself as well.

> Key takeaway: Table in, `transform` function, table out – and the output schema must match.

## Chapter 3: First steps – variables, comments, indentation

In this chapter you write your first lines of Python. You learn three things: how to store values, how to write notes into the code and why spaces matter in Python.

### Variables: labelled boxes

A variable is a box with a name on it. You put a value in and take it out again later.

```python
quantity = 10
price = 2.5
print(quantity * price)
# Output: 25.0
```

The equals sign `=` does not mean “is equal to” here. It means: “Put the value on the right into the box on the left.”

You can replace the content at any time:

```python
quantity = 10
quantity = quantity + 5
print(quantity)
# Output: 15
```

Read the second line from right to left: take the old value, add 5, put the result back into `quantity`.

### Rules for names

- Only letters, digits and underscore `_`.
- Do not start with a digit: `2quantity` does not work, `quantity2` does.
- No spaces: `net amount` does not work, `net_amount` does.
- Upper and lower case matter: `Quantity` and `quantity` are two different boxes.
- Avoid special characters such as umlauts. They work, but often cause trouble.

Good names say what is inside. `net_revenue` is better than `x`.

### Comments: notes for humans

Python ignores everything after a `#`. That is where you write explanations for yourself and your colleagues.

```python
# German VAT rate
vat = 0.19
gross = 100 * (1 + vat)  # gives 119.0
```

Write comments that explain **why**. The **what** is already in the code. A year from now you will be grateful.

### Calculating

Python calculates like a pocket calculator:

| Symbol | Meaning | Example | Result |
| --- | --- | --- | --- |
| `+` | plus | `7 + 2` | `9` |
| `-` | minus | `7 - 2` | `5` |
| `*` | times | `7 * 2` | `14` |
| `/` | divided by | `7 / 2` | `3.5` |
| `//` | integer division | `7 // 2` | `3` |
| `%` | remainder | `7 % 2` | `1` |
| `**` | to the power of | `7 ** 2` | `49` |

Note: decimal numbers are written with a **point**, not a comma. `2.5` is correct, `2,5` means something completely different.

### Indentation: spaces have meaning

This is the most important peculiarity of Python. In other languages, brackets or keywords like `ENDIF` mark what belongs together. In Python this is done by the **spaces at the start of the line**.

```python
def transform(data):
    # everything indented belongs to the function
    result = data
    return result
```

The three lines below `def` are indented by **four spaces**. That is how Python knows they belong to the function.

Rules for indentation:

1. Always use four spaces per level.
2. Never mix tabs and spaces.
3. A colon `:` at the end of a line is always followed by an indented line.
4. Lines at the same level must be indented by exactly the same amount.

A mistake here leads to a message like `IndentationError`. Then check the spaces at the start of the line.

### Showing output with print

`print` displays a value. This is very helpful when practising locally. In the script operator itself you do not see the output, but it does no harm either.

> Key takeaway: `=` puts something into a box. Four spaces show what belongs together.

## Chapter 4: Data types – numbers, text, True/False, None

Every value in Python has a type. The type decides what you can do with the value. You calculate with numbers, you join texts together.

### The five basic types

| Type | Python name | Example | In Datasphere |
| --- | --- | --- | --- |
| Whole number | `int` | `42` | Integer, Integer64 |
| Decimal fraction | `float` | `3.14` | – (amounts arrive as Decimal, Chapter 14) |
| Text | `str` | `"Berlin"` | String, LargeString |
| True/False | `bool` | `True`, `False` | Boolean |
| Nothing | `None` | `None` | NULL |

On top of that there is `Decimal` for exact amounts. Chapter 14 is dedicated to it.

### Finding out the type

With `type()` you ask Python for the type:

```python
print(type(42))       # <class 'int'>
print(type("42"))     # <class 'str'>
print(type(42.0))     # <class 'float'>
```

Note: `42` and `"42"` are different. The first is a number, the second is a text that only looks like a number.

### Texts (strings)

Texts are written in quotation marks. Single `'...'` and double `"..."` work the same.

```python
city = "Walldorf"
country = 'DE'
```

You can join texts with `+`:

```python
key = country + "-" + city
print(key)
# Output: DE-Walldorf
```

**f-strings** are more convenient. You put an `f` in front of the quotation mark and place variables in curly brackets:

```python
quantity = 3
text = f"{quantity} pieces were ordered"
print(text)
# Output: 3 pieces were ordered
```

Useful text commands:

| Command | What it does | Result for `"  Hallo  "` |
| --- | --- | --- |
| `.strip()` | remove outer spaces | `"Hallo"` |
| `.upper()` | all upper case | `"  HALLO  "` |
| `.lower()` | all lower case | `"  hallo  "` |
| `.replace("a", "e")` | replace | `"  Hello  "` |
| `len(...)` | count characters | `9` |

### Taking parts of a text

Every character has a position. Counting starts at **0**, not at 1.

```python
material = "MAT-00123"
print(material[0])     # M
print(material[0:3])   # MAT
print(material[4:])    # 00123
print(material[-3:])   # 123
```

`[0:3]` means: from position 0 up to, but not including, position 3. `[-3:]` means: the last three characters. You may know this as offset and length from ABAP.

### True and False (Boolean)

A Boolean has only two possible values: `True` or `False`. Capitalised! You usually get them from comparisons:

| Comparison | Meaning | `5 ? 3` gives |
| --- | --- | --- |
| `==` | equal | `False` |
| `!=` | not equal | `True` |
| `>` | greater | `True` |
| `<` | less | `False` |
| `>=` | greater or equal | `True` |
| `<=` | less or equal | `False` |

Careful: `=` stores a value, `==` compares. Mixing them up is the classic beginner mistake.

### None: the empty value

`None` means “there is nothing here”. It is not the same as `0` or an empty text `""`. In tables you usually meet `pd.NA` instead of `None`. More on this in Chapter 15.

### Converting types

```python
int("42")      # text to whole number: 42
float("3.5")   # text to decimal fraction: 3.5
str(42)        # number to text: "42"
```

If the conversion does not work, for example `int("abc")`, Python raises a `ValueError`.

> Key takeaway: Every value has a type. `"42"` is text, `42` is a number.

## Chapter 5: Lists and dictionaries

Often you do not want to store one value, but many. Lists and dictionaries exist for this. You need both constantly in the script operator, for example for column lists and mapping tables.

### Lists: a row of values

A list is written in square brackets `[ ]`. The values are separated by commas.

```python
plants = ["1000", "1010", "2000"]
quantities = [5, 12, 7]
```

You access single entries by their position. Here, too, counting starts at 0:

```python
print(plants[0])    # 1000
print(plants[-1])   # 2000 (the last one)
print(len(plants))  # 3
```

### Changing lists

```python
plants.append("3000")       # add at the end
print(plants)
# Output: ['1000', '1010', '2000', '3000']

print("1010" in plants)     # True
print("9999" in plants)     # False
```

The word `in` checks whether a value is in the list. You will need this often when filtering.

### What lists are good for in the script operator

Most often you use lists to **select columns**:

```python
columns = ["CUSTOMER", "COUNTRY", "REVENUE"]
result = data[columns]
```

You will see this in more detail in Chapter 10. Here it is only about the principle: a list of column names tells pandas which columns you want.

### Dictionaries: looking things up by key

A dictionary (dict for short) is like a phone book. For each **key** there is a **value**. It is written in curly brackets `{ }`.

```python
country_names = {
    "DE": "Germany",
    "AT": "Austria",
    "CH": "Switzerland",
}
```

The key is left of the colon, the value is on the right. The comma after the last entry is allowed and makes later additions easier.

### Looking up values

```python
print(country_names["AT"])
# Output: Austria
```

If the key does not exist, Python raises a `KeyError`. Safer is `.get()` with a fallback value:

```python
print(country_names.get("FR", "unknown"))
# Output: unknown
```

### What dicts are good for in the script operator

Dicts are perfect for **mappings** that you may have solved in BW by reading master data or with fixed values in a routine:

- country code to country name
- old account key to new account key
- status code to readable text
- old column name to new column name

Two examples you will see again later:

```python
# rename columns (Chapter 10)
data = data.rename(columns={"KUNNR": "CUSTOMER", "LAND1": "COUNTRY"})

# translate values (Chapter 19)
data["COUNTRY_TEXT"] = data["COUNTRY"].map(country_names)
```

`KUNNR` and `LAND1` are the SAP field names for customer number and country key.

Important for Datasphere: a dict in the code is hard-wired. If the mapping changes often, it belongs in its own table that you connect with a join.

### Lists and dicts compared

|  | List | Dictionary |
| --- | --- | --- |
| Brackets | `[ ]` | `{ }` |
| Access by | position (0, 1, 2 …) | key ("DE", "AT" …) |
| Typical use | selecting columns, allowed values | renaming, translating values |

> Key takeaway: List = a row with positions. Dict = a lookup table with keys.

## Chapter 6: Conditions – if, elif, else

With conditions your program makes decisions: if this is true, do that, otherwise something else. You know this from Excel as `IF()` and from ABAP as `IF ... ELSEIF ... ELSE ... ENDIF`.

### The simplest form: if

```python
quantity = 120
if quantity > 100:
    print("Large order")
```

Read it like this: “If quantity is greater than 100, then print Large order.”

Pay attention to two things:

1. The `if` line ends with a **colon**.
2. What should be executed is **indented**.

There is no `ENDIF`. The condition ends where the indentation ends.

### Otherwise: else

```python
if quantity > 100:
    category = "Large"
else:
    category = "Normal"
```

`else` has no condition of its own. It applies whenever the `if` does not.

### Several levels: elif

`elif` is short for “else if”. With it you check several cases one after the other:

```python
if quantity >= 1000:
    category = "A"
elif quantity >= 100:
    category = "B"
elif quantity > 0:
    category = "C"
else:
    category = "none"
```

Python checks from top to bottom. As soon as one condition is true, it runs that block and skips the rest. That is why **order matters**: the strictest condition comes first.

### Combining conditions: and, or, not

| Word | Meaning | Example |
| --- | --- | --- |
| `and` | both must be true | `country == "DE" and quantity > 10` |
| `or` | one is enough | `country == "DE" or country == "AT"` |
| `not` | reverses | `not cancelled` |

A complete example:

```python
country = "AT"
quantity = 50

if (country == "DE" or country == "AT") and quantity > 10:
    print("DACH order with a relevant quantity")
```

Brackets make clear what belongs together. Better one bracket too many than one too few.

### Checking with in

Instead of many `or` you can use a list:

```python
if country in ["DE", "AT", "CH"]:
    region = "DACH"
```

This is shorter and easier to read. DACH stands for Germany, Austria and Switzerland.

### Very important: if works on one value, not on a column

Everything in this chapter works with **single values**. In the script operator, however, you have a whole table with thousands of rows.

That is why this line does **not** work:

```python
# WRONG – leads to an error
if data["QTY"] > 100:
    ...
```

Python does not know whether you mean “all rows” or “any row”. It reports: `The truth value of a Series is ambiguous`.

For whole columns there are separate tools. You will learn them in Chapter 11 (filtering) and Chapter 19 (recipes with `np.where` and `np.select`). This chapter is still important: you need `if` in your own small functions that you apply to every value.

> Key takeaway: `if` + colon + indentation. Top to bottom, strictest first.

## Chapter 7: Loops

A loop repeats code for every entry of a list. You should understand loops, but rarely use them for data in the script operator. You will find out why at the end of this chapter.

### The for loop

```python
plants = ["1000", "1010", "2000"]

for plant in plants:
    print("Processing plant", plant)
```

Output:

```
Processing plant 1000
Processing plant 1010
Processing plant 2000
```

Read it like this: “For each `plant` in the list `plants`, run the indented block.” The name `plant` is your choice. On each pass it holds the next entry.

Here too: colon at the end, block indented.

### Number sequences with range

`range` creates a sequence of numbers:

```python
for i in range(3):
    print(i)
# Output: 0, 1, 2 (one per line)
```

`range(3)` gives 0, 1 and 2. The 3 itself is not included. This is the same logic as when slicing texts.

### Collecting values

A typical pattern: you start with an empty result and fill it in the loop.

```python
quantities = [5, 12, 7]
total = 0
for q in quantities:
    total = total + q
print(total)
# Output: 24
```

Of course `sum(quantities)` is shorter. But the pattern shows how loops think.

### Looping over a dictionary

```python
country_names = {"DE": "Germany", "AT": "Austria"}

for code, name in country_names.items():
    print(code, "=", name)
```

`.items()` gives key and value as a pair. You catch both with two names.

### Building lists in one line

Python has a compact notation for loops that create a list:

```python
columns = ["customer", "country", "revenue"]
upper = [c.upper() for c in columns]
print(upper)
# Output: ['CUSTOMER', 'COUNTRY', 'REVENUE']
```

This is called a **list comprehension**. Read it as: “A list of `c.upper()` for each `c` in `columns`.” It is useful for processing column names in one go.

### Loops in the script operator: use sparingly!

The obvious idea would be: I go through every row of the table with a loop. Technically this works, but it is **very slow**.

```python
# Works, but slow – please avoid
for index, row in data.iterrows():
    ...
```

pandas can process whole columns at once. This is called **vectorised**. Instead of touching one row 100,000 times, pandas calculates once for the whole column. This is often a hundred times faster.

Comparison:

```python
# Slow: row by row
results = []
for value in data["QTY"]:
    results.append(value * 2)
data["DOUBLE"] = results

# Fast: the whole column at once
data["DOUBLE"] = data["QTY"] * 2
```

Both versions give the same result. The second is shorter, faster and easier to read.

### When loops still make sense

- Looping over a **short list of column names** to treat them all the same way.
- Looping over a **small dictionary** to apply rules.

But never over the **rows** of the table when there is a column-based solution.

```python
# Good example: trim all text columns
for col in ["NAME", "CITY", "STREET"]:
    data[col] = data[col].str.strip()
```

Here the loop runs only three times, and each time a whole column is processed.

> Key takeaway: Loops over column names – yes. Loops over rows – only in an emergency.

## Chapter 8: Functions and the transform function

A function is a named piece of code that takes something and gives something back. The `transform` function is the heart of the script operator. After this chapter you will understand every line of it.

### Defining a function

```python
def gross(net):
    return net * 1.19
```

Line by line:

1. `def` means “define a function”.
2. `gross` is its name.
3. `(net)` is the **parameter**: the value the function takes.
4. The colon starts the function body.
5. `return` gives back the result and ends the function.

### Calling a function

Defining alone does nothing yet. Only the **call** runs it:

```python
result = gross(100)
print(result)
# Output: 119.0
```

When calling, Python puts the `100` into the parameter `net`. Then the body runs, and `return` delivers `119.0`.

### Several parameters

```python
def gross(net, rate):
    return net * (1 + rate)

print(gross(100, 0.07))
# Output: 107.0
```

The values are passed in the order in which the parameters are listed.

### Without return you get None

If you forget `return`, the function returns `None`. In the script operator this is a hard error. Datasphere expects a DataFrame and gets nothing.

```python
# WRONG – return is missing
def transform(data):
    data["NEW"] = 1
```

### The transform function decoded

Now you fully understand the default script:

```python
def transform(data):
    return data
```

- `transform` is the fixed name. Datasphere looks for exactly this name. Do not change it.
- `data` is the parameter. Datasphere passes the input table here as a DataFrame.
- `return data` gives the table back.

**You never call `transform` yourself.** Datasphere does that for you, once per batch.

### A structure you can always use

Get used to a fixed structure. It makes your code readable and errors easier to find:

```python
def transform(data):
    # 1. Make a copy
    df = data.copy()

    # 2. Calculations and cleaning
    df["GROSS"] = df["NET"] * Decimal("1.19")

    # 3. Filter rows (if needed)
    df = df[df["GROSS"] > 0]

    # 4. Select columns matching the output schema
    df = df[["DOC_NO", "NET", "GROSS"]]

    # 5. Return the result
    return df
```

The copy in step 1 prevents a common pandas warning. More on this in Chapter 10.

You use `Decimal("1.19")` instead of `1.19` because amounts arrive in Datasphere as Decimal. Decimal and floating-point numbers cannot be mixed. Chapter 14 explains why.

### Your own helper functions

You may define further functions inside `transform`. This is useful for rules you want to apply to every value:

```python
def transform(data):
    def category(qty):
        if qty >= 1000:
            return "A"
        elif qty >= 100:
            return "B"
        else:
            return "C"

    df = data.copy()
    df["CATEGORY"] = df["QTY"].apply(category)
    return df
```

`.apply(category)` calls your function for every value of the column. This is slower than a pure column calculation, but often the most readable. For simple rules there are faster ways (Chapter 19).

### Short functions with lambda

For very small functions there is a short notation:

```python
df["NAME"] = df["NAME"].apply(lambda x: x.title())
```

`lambda x: x.title()` means: “Take `x` and return `x.title()`.” You often see `lambda` in examples online and in the SAP help.

> Key takeaway: `transform` always has this name, receives `data` and must always return a DataFrame.

## Chapter 9: pandas basics – DataFrame and Series

A DataFrame is a table with rows and named columns, like an Excel sheet. A Series is a single column of it. From now on you will use these two terms in every chapter.

### Building a DataFrame to practise with

In Datasphere you are given `data`. Locally you build a DataFrame yourself to practise. The easiest way is with a dictionary: each key is a column name, each value is a list.

```python
data = pd.DataFrame({
    "DOC_NO":   ["4711", "4712", "4713", "4714"],
    "CUSTOMER": ["C01", "C02", "C01", "C03"],
    "COUNTRY":  ["DE", "AT", "DE", "CH"],
    "QTY":      [10, 250, 3, 1200],
    "NET":      [Decimal("100.00"), Decimal("2500.00"),
                 Decimal("30.00"), Decimal("9800.00")],
})
print(data)
```

Output:

```
  DOC_NO CUSTOMER COUNTRY   QTY      NET
0   4711      C01      DE    10   100.00
1   4712      C02      AT   250  2500.00
2   4713      C01      DE     3    30.00
3   4714      C03      CH  1200  9800.00
```

We will use this DataFrame again and again in the next chapters. Save it as a template.

`NET` is deliberately a Decimal column here, because that is how amounts arrive in Datasphere (Chapter 14). Locally you need the line `from decimal import Decimal` from Chapter 2 for this. That way your practice code behaves like it will in the system later.

### The parts

- **Columns**: `DOC_NO`, `CUSTOMER`, `COUNTRY`, `QTY`, `NET`.
- **Rows**: four of them here.
- **Index**: the numbers 0 to 3 on the far left. They number the rows. You can usually ignore them.

### Getting a quick overview

| Command | What it shows |
| --- | --- |
| `data.head()` | the first 5 rows |
| `data.shape` | (number of rows, number of columns), here `(4, 5)` |
| `data.columns` | all column names |
| `data.dtypes` | the data type of each column |
| `len(data)` | the number of rows |

`data.dtypes` is particularly important. It shows whether pandas sees a column as a number, text or date.

### What do the dtypes mean?

| dtype | Meaning |
| --- | --- |
| `int64` | whole number (this is how Integer64 arrives) |
| `uint8` to `uint64` | whole number without sign (this is how Integer arrives, Chapter 14) |
| `float64` | floating-point number |
| `bool` | True/False |
| `datetime64[ns]` | date and time |
| `object` | text, Decimal or columns with missing values |

In Datasphere, text columns and Decimal columns arrive as `object`. A number column with missing values can also be `object`. Chapter 15 explains this.

### Getting one column: the Series

With square brackets and the column name you get a column:

```python
quantities = data["QTY"]
print(quantities)
```

```
0      10
1     250
2       3
3    1200
Name: QTY, dtype: int64
```

The result is a **Series**: a column with an index and values.

### Calculating with a Series

The nice thing about a Series: you calculate with it as if it were a single number. pandas applies the calculation to every row.

```python
print(data["QTY"] * 2)
# 20, 500, 6, 2400

print(data["NET"] / data["QTY"])
# unit price per row: 10.00, 10.00, 10.00, 8.166666666666666666666666667
```

In the second example pandas divides row 0 by row 0, row 1 by row 1, and so on. This is called calculating **row by row**.

### Summarising

A Series can summarise itself:

| Command | Result for `data["QTY"]` |
| --- | --- |
| `.sum()` | 1463 |
| `.mean()` | 365.75 |
| `.min()` | 3 |
| `.max()` | 1200 |
| `.count()` | 4 (number of non-missing values) |

Careful in the script operator: these values refer only to the **current batch**, not to the whole table. Chapter 17 explains why.

> Key takeaway: DataFrame = table. Series = one column. Calculations on a Series apply to all rows.

## Chapter 10: Selecting, renaming and calculating columns

This is the daily bread of the script operator: selecting columns, calculating new columns, adjusting names. We work with the practice DataFrame from Chapter 9.

### Selecting several columns

You get one column with single square brackets. For several columns you need **double** square brackets:

```python
selection = data[["DOC_NO", "COUNTRY", "NET"]]
```

Why double? The outer brackets access the DataFrame. The inner brackets form a **list** of column names (Chapter 5).

| Notation | Result |
| --- | --- |
| `data["COUNTRY"]` | a Series |
| `data[["COUNTRY"]]` | a DataFrame with one column |
| `data[["COUNTRY", "NET"]]` | a DataFrame with two columns |

The order in the list decides the order of the columns in the result.

### Calculating a new column

You create a new column by assigning a value to it:

```python
df = data.copy()
df["GROSS"] = df["NET"] * Decimal("1.19")
```

If the column `GROSS` does not exist yet, it is created. If it already exists, it is overwritten.

`NET` is a Decimal column. That is why you calculate with `Decimal("1.19")` and not with `1.19` (Chapter 14).

More examples:

```python
# fixed value for all rows
df["SOURCE"] = "ERP"

# calculated from two columns
df["UNIT_PRICE"] = df["NET"] / df["QTY"]

# joining texts
df["KEY"] = df["COUNTRY"] + "_" + df["CUSTOMER"]
```

When joining with `+`, all parts must be text. If a column is a number, convert it first: `df["QTY"].astype(str)`.

Careful when dividing: if `QTY` is 0 in a row, the Decimal division stops with an error. Filter such rows out first (Chapter 11) or set the value deliberately with `np.where` (Chapter 19).

### Why .copy()?

You may already have seen this pattern:

```python
df = data[["DOC_NO", "NET"]]
df["GROSS"] = df["NET"] * Decimal("1.19")   # warning!
```

pandas then shows a `SettingWithCopyWarning`. The reason: `df` may only be a view on a part of `data`. pandas is not sure what you want to change.

The solution is simple: make a real copy.

```python
df = data[["DOC_NO", "NET"]].copy()
df["GROSS"] = df["NET"] * Decimal("1.19")   # no warning
```

Rule of thumb: after every selection or filter, if you change columns afterwards, add `.copy()`.

### Renaming columns

With `.rename()` and a dictionary from old to new:

```python
df = df.rename(columns={
    "NET":     "NET_AMOUNT",
    "COUNTRY": "COUNTRY_CODE",
})
```

Columns not in the dictionary remain unchanged. Do not forget ` df =  ` in front. Otherwise the result is lost.

### Adjusting all column names at once

Sometimes all names must be in capitals because the output schema requires it:

```python
df.columns = [c.upper() for c in df.columns]
```

This is the list comprehension from Chapter 7.

### Removing columns

```python
df = df.drop(columns=["QTY", "CUSTOMER"])
```

Usually it is clearer to simply select the **wanted** columns at the end. Then you see at a glance what comes out.

### Putting columns in the right order

The last step before `return` should almost always be a selection that exactly matches the output schema:

```python
output = ["DOC_NO", "COUNTRY_CODE", "NET_AMOUNT", "GROSS"]
return df[output]
```

This catches two errors at once: surplus columns and a wrong order. If a column is missing, pandas immediately raises a `KeyError` with its name. That is a useful hint.

### Complete example

```python
def transform(data):
    df = data.copy()
    df["GROSS"] = df["NET"] * Decimal("1.19")
    df["KEY"] = df["COUNTRY"] + "_" + df["CUSTOMER"]
    df = df.rename(columns={"NET": "NET_AMOUNT"})
    return df[["DOC_NO", "KEY", "NET_AMOUNT", "GROSS"]]
```

> Key takeaway: Double brackets for several columns, `.copy()` before changing, select exactly the output schema at the end.

## Chapter 11: Filtering rows

Filtering means keeping only the rows that meet a condition. In pandas this happens in two steps. First you ask a yes/no question for every row, then you keep the yes rows.

### Step 1: the yes/no column

A comparison on a Series gives `True` or `False` for every row:

```python
print(data["COUNTRY"] == "DE")
```

```
0     True
1    False
2     True
3    False
Name: COUNTRY, dtype: bool
```

This Series is called a **mask**. It says for every row: keep or not.

### Step 2: applying the mask

You put the mask in square brackets after the DataFrame:

```python
mask = data["COUNTRY"] == "DE"
only_de = data[mask]
```

Or shorter, in one line:

```python
only_de = data[data["COUNTRY"] == "DE"]
```

The result contains only rows 0 and 2.

### All comparisons work

```python
data[data["QTY"] > 100]            # quantity greater than 100
data[data["QTY"] <= 10]            # quantity at most 10
data[data["CUSTOMER"] != "C01"]    # all except C01
```

### Several conditions: & and |

This is where it gets tricky. For columns you do **not** use `and` and `or` from Chapter 6, but symbols:

| For single values | For columns (pandas) | Meaning |
| --- | --- | --- |
| `and` | `&` | and |
| `or` | \` | \` |
| `not` | `~` | not |

And: **every partial condition must be in round brackets.**

```python
# correct
data[(data["COUNTRY"] == "DE") & (data["QTY"] > 5)]

# wrong – missing brackets lead to errors
data[data["COUNTRY"] == "DE" & data["QTY"] > 5]
```

The reason is technical: `&` binds more strongly than `==`. Just remember: brackets around every condition.

For long filters the code is easier to read if you name the masks one by one:

```python
is_de = data["COUNTRY"] == "DE"
is_large = data["QTY"] > 100
result = data[is_de | is_large]
```

### Filtering by a list of values: isin

Instead of many `|` you use `.isin()`:

```python
dach = data[data["COUNTRY"].isin(["DE", "AT", "CH"])]
```

The other way round, all except these values, with `~`:

```python
rest = data[~data["COUNTRY"].isin(["DE", "AT", "CH"])]
```

### Filtering by a range: between

```python
medium = data[data["QTY"].between(10, 500)]
```

`between` includes both limits, here 10 and 500.

### Filtering by text

You will learn how to search in texts in Chapter 12. A preview:

```python
# documents starting with "47"
data[data["DOC_NO"].str.startswith("47")]
```

### Filtering by missing values

```python
# rows in which CUSTOMER is filled
data[data["CUSTOMER"].notna()]

# rows in which CUSTOMER is empty
data[data["CUSTOMER"].isna()]
```

Never use `== None`. In pandas this does not work as expected. Details follow in Chapter 15.

### In the script operator

Filtering is one of the few operations that work without problems in batch mode. Every row is checked on its own. Whether it is in the first or tenth batch does not matter.

```python
def transform(data):
    df = data[(data["COUNTRY"] == "DE") & (data["NET"] > 0)].copy()
    df["GROSS"] = df["NET"] * Decimal("1.19")
    return df[["DOC_NO", "NET", "GROSS"]]
```

Note the `.copy()` directly after the filter. After that you change `df`, and this keeps pandas quiet.

By the way, a simple filter is also possible in the graphical projection operator. Use the script operator for filters that would be too cumbersome there.

> Key takeaway: First the mask, then `data[mask]`. For columns use `&`, `|`, `~` and brackets around every condition.

## Chapter 12: Working with text

For text columns pandas has the magic word `.str`. After it you can apply almost all text commands from Chapter 4 to a whole column. Cleaning text is one of the most common reasons for a script operator.

### The principle

For a single text you write `name.upper()`. For a column you put `.str` in between:

```python
df["NAME"] = df["NAME"].str.upper()
```

The `.str` tells pandas: “Treat every value of this column as text.”

### The most important commands

| Command | What it does | `" smith ltd "` becomes |
| --- | --- | --- |
| `.str.strip()` | remove outer spaces | `"smith ltd"` |
| `.str.upper()` | all upper case | `" SMITH LTD "` |
| `.str.lower()` | all lower case | `" smith ltd "` |
| `.str.title()` | capitalise each word | `" Smith Ltd "` |
| `.str.len()` | number of characters | `11` |
| `.str.replace("ltd", "Ltd")` | replace | `" smith Ltd "` |

Commands can be chained. Python processes them from left to right:

```python
df["NAME"] = df["NAME"].str.strip().str.upper()
```

### Cutting out parts

As with single texts, just with `.str` in front:

```python
# first 4 characters, e.g. company code from "1000-4711"
df["BUKRS"] = df["OBJECT_KEY"].str[:4]

# from character 5 onwards
df["BELNR"] = df["OBJECT_KEY"].str[5:]
```

`BUKRS` and `BELNR` are the SAP field names for company code and document number.

### Splitting at a separator: split

`.str.split()` splits a text at a character. With `expand=True` each part becomes its own column:

```python
# "1000-4711" -> "1000" and "4711"
parts = df["OBJECT_KEY"].str.split("-", expand=True)
df["BUKRS"] = parts[0]
df["BELNR"] = parts[1]
```

### Leading zeros

In SAP data, leading zeros are a constant topic. In BW you used the ALPHA conversion for this.

```python
# pad with zeros to 10 digits: "4711" -> "0000004711"
df["CUSTOMER"] = df["CUSTOMER"].str.zfill(10)

# remove zeros: "0000004711" -> "4711"
df["CUSTOMER"] = df["CUSTOMER"].str.lstrip("0")
```

Careful with `lstrip("0")`: `"0000000000"` becomes an empty text `""`. Check whether this can occur in your data.

### Searching: contains, startswith, endswith

These commands return a mask of `True` and `False`. You can use it to filter (Chapter 11):

```python
# contains "Ltd"
df[df["NAME"].str.contains("Ltd", na=False)]

# starts with "DE"
df[df["IBAN"].str.startswith("DE", na=False)]
```

`na=False` means: missing values count as “not found”. Without it, missing values disturb the filter.

Ignoring upper and lower case:

```python
df["NAME"].str.contains("ltd", case=False, na=False)
```

### Recognising patterns with regular expressions

Regular expressions (regex for short) describe text patterns. You cannot import the `re` module in the sandbox. But pandas understands regex directly in `contains`, `replace` and `extract`.

| Pattern | Meaning |
| --- | --- |
| `\d` | one digit |
| `\d+` | one or more digits |
| `[A-Z]` | one capital letter |
| `^` | start of text |
| `$` | end of text |
| `\s+` | one or more spaces |

Three useful examples:

```python
# remove everything except digits: "Tel. 0621-123" -> "0621123"
df["PHONE"] = df["PHONE"].str.replace(r"\D", "", regex=True)

# collapse several spaces into one
df["NAME"] = df["NAME"].str.replace(r"\s+", " ", regex=True)

# extract a five-digit postcode from an address
df["POSTCODE"] = df["ADDRESS"].str.extract(r"(\d{5})", expand=False)
```

The `r` in front of the text makes sure Python does not process the backslashes itself. Always put it in front of a regex.

### Numbers as text, text as numbers

```python
# number to text
df["QTY_TXT"] = df["QTY"].astype(str)

# text to number, invalid values become empty
df["QTY"] = pd.to_numeric(df["QTY_TXT"], errors="coerce")
```

SAP exports from German systems often use the German number format, such as `"1.234,56"`: a point as thousands separator and a comma as decimal separator. You have to convert it first: remove the points, then replace the comma with a point.

```python
s = df["AMOUNT_TXT"].str.replace(".", "", regex=False)
s = s.str.replace(",", ".", regex=False)
df["AMOUNT"] = pd.to_numeric(s, errors="coerce")
```

> Key takeaway: For text columns always put `.str` in front. Use `na=False` when searching and `r"..."` for regex.

## Chapter 13: Dates and times

Dates arrive in the script operator as a pandas **Timestamp**, in columns of type `datetime64[ns]`. For date columns there is the magic word `.dt`, just like `.str` for texts. Careful: there is a range limit that often bites in SAP data.

### Which column types become Timestamp?

| Datasphere type | In Python |
| --- | --- |
| Date | `pd.Timestamp`, dtype `datetime64[ns]` |
| DateTime | `pd.Timestamp`, dtype `datetime64[ns]` |
| Time | `pd.Timestamp`, dtype `datetime64[ns]` |
| TimeStamp | `pd.Timestamp`, dtype `datetime64[ns]` |

Even a pure date always has a time in pandas, namely 00:00.

### The range limit: 1677 to 2262

With `datetime64[ns]`, pandas can only represent dates between **21 September 1677** and **11 April 2262**. The SAP documentation for the operator states this too.

Why does this matter? In SAP, “valid indefinitely” is often stored as **31 December 9999**. This date does not fit. If you receive such values as text and want to convert them, you must handle them first (see below).

### Turning text into a date

SAP dates sometimes arrive as text in the format `YYYYMMDD`, for example `"20261005"`. `pd.to_datetime` converts them:

```python
df["BUDAT"] = pd.to_datetime(df["BUDAT_TXT"], format="%Y%m%d", errors="coerce")
```

- `format` describes how the text is built.
- `errors="coerce"` turns invalid values into an empty value `NaT` instead of a crash.

`BUDAT` is the SAP field name for the posting date.

The most important format codes:

| Code | Meaning | Example |
| --- | --- | --- |
| `%Y` | year, 4 digits | 2026 |
| `%m` | month, 2 digits | 10 |
| `%d` | day, 2 digits | 05 |
| `%H` | hour | 14 |
| `%M` | minute | 30 |
| `%S` | second | 00 |

The German format `"05.10.2026"` is read with `format="%d.%m.%Y"`.

### Handling 99991231 and 00000000

```python
txt = df["DATBI_TXT"].replace({"99991231": "22620101", "00000000": pd.NA})
df["DATBI"] = pd.to_datetime(txt, format="%Y%m%d", errors="coerce")
```

Here we replace “indefinite” with a distant but representable date. Empty SAP dates `00000000` become an empty value. Agree with the business department which replacement value they want. `DATBI` is the SAP field name for “valid to”.

### Parts of a date: .dt

```python
df["YEAR"]    = df["BUDAT"].dt.year
df["MONTH"]   = df["BUDAT"].dt.month
df["DAY"]     = df["BUDAT"].dt.day
df["QUARTER"] = df["BUDAT"].dt.quarter
df["WEEKDAY"] = df["BUDAT"].dt.dayofweek   # 0 = Monday
df["WEEK"]    = df["BUDAT"].dt.isocalendar().week
```

### Dates as text

With `.dt.strftime()` you turn a date into text in any format:

```python
df["BUDAT_DE"]  = df["BUDAT"].dt.strftime("%d.%m.%Y")   # 05.10.2026
df["PERIOD"]    = df["BUDAT"].dt.strftime("%Y%m")       # 202610
df["BUDAT_SAP"] = df["BUDAT"].dt.strftime("%Y%m%d")     # 20261005
```

This is useful when the target column in the output schema is a string.

### Calculating with dates

Time spans are called `Timedelta` in pandas:

```python
# add 30 days payment term
df["DUE"] = df["BUDAT"] + pd.Timedelta(days=30)

# difference between two dates in days
df["DURATION"] = (df["END"] - df["START"]).dt.days
```

For months there is `pd.DateOffset`, because months have different lengths:

```python
df["NEXT_MONTH"] = df["BUDAT"] + pd.DateOffset(months=1)
```

### Start and end of month

```python
df["MONTH_START"] = df["BUDAT"].dt.to_period("M").dt.to_timestamp()
df["MONTH_END"]   = df["BUDAT"] + pd.offsets.MonthEnd(0)
```

`MonthEnd(0)` jumps to the end of the current month. If the date is already the last day, it stays the same.

### The example from the SAP help

The SAP documentation shows an example that reformats a birthday stored as text:

```python
def transform(data):
    timestamps = data['birthday'].apply(lambda x: pd.Timestamp(x))
    data['birthday'] = timestamps.apply(
        lambda x: f'{x.month_name()} {x.day}, {x.year}, {x.day_name()}'
    )
    return data
```

You can now read every line: `apply` with `lambda` (Chapter 8), an f-string (Chapter 4) and parts of a Timestamp. With `.dt` it is vectorised and therefore faster. `strftime` does not help directly here, because `%d` always prints the day with two digits (05 instead of 5). That is why we build the text from parts:

```python
def transform(data):
    ts = pd.to_datetime(data['birthday'])
    data['birthday'] = (
        ts.dt.month_name() + ' ' + ts.dt.day.astype(str) + ', '
        + ts.dt.year.astype(str) + ', ' + ts.dt.day_name()
    )
    return data
```

This version assumes that `birthday` has no missing values. Otherwise `dt.day` becomes a floating-point number, and day 5 becomes the text `5.0`. With missing values, stick to the SAP example.

> Key takeaway: Date columns with `.dt`, text to date with `pd.to_datetime(..., errors="coerce")`. 31.12.9999 does not fit.

## Chapter 14: Decimal and calculating with money

Amounts and quantities from SAP usually arrive as **Decimal**. In Python these are objects of the class `Decimal`, and the column has the dtype `object`. Decimal calculates exactly, while normal floating-point numbers (`float`) have small rounding errors.

### The problem with float

Try this:

```python
print(0.1 + 0.2)
# Output: 0.30000000000000004
```

This is not a Python bug. Computers store floating-point numbers in binary, and 0.1 cannot be represented exactly in binary. For statistics this does not matter. For accounting it is unacceptable.

### Decimal calculates exactly

```python
print(Decimal("0.1") + Decimal("0.2"))
# Output: 0.3
```

In Datasphere, `Decimal` is available without `import`. Locally you need `from decimal import Decimal`.

### Important: always create Decimal from text

```python
Decimal("0.1")   # correct: exactly 0.1
Decimal(0.1)     # wrong: 0.1000000000000000055511151231257827021181583404541015625
```

The second line takes over the rounding error of the floating-point number. So always write fixed values in quotation marks.

### Which Datasphere types are Decimal?

| Datasphere type | In Python | dtype |
| --- | --- | --- |
| Decimal | `decimal.Decimal` | `object` |
| DecimalFloat | `decimal.Decimal` | `object` |
| Integer | `int` | smallest fitting type from `uint8` to `uint64` |
| Integer64 | `int` | `int64` |

**Careful with Integer:** According to the SAP documentation, the operator chooses the smallest **unsigned** type for Integer that can hold all values. Unsigned types have no negative numbers. If you calculate 3 − 5 in a `uint8` column, the result is not −2 but 254, and without any error message. So convert Integer columns before calculations that can become negative:

```python
df["DIFFERENCE"] = df["ACTUAL"].astype("int64") - df["PLAN"].astype("int64")
```

If the column contains missing values, it is `object` anyway (Chapter 15). Then this problem does not occur, but you must fill the missing values before `astype("int64")`.

### Calculating with Decimal columns

You can combine Decimal columns with each other and with fixed Decimal values:

```python
df["GROSS"] = df["NET"] * Decimal("1.19")
df["TOTAL"] = df["AMOUNT1"] + df["AMOUNT2"]
```

Avoid mixing Decimal and float:

```python
# WRONG – TypeError: Decimal and float do not fit together
df["GROSS"] = df["NET"] * 1.19
```

Mixing with whole numbers (`int`) works, however: `df["NET"] * 2` is fine.

### Filtering with Decimal

The example from the SAP help:

```python
def transform(data):
    data = data[data.salary > Decimal("1299.99")]
    return data
```

`data.salary` is a short form of `data["salary"]`. It only works for column names without spaces and special characters. When in doubt, use the notation with square brackets.

### Rounding

Decimal rounds with `.quantize()`. You pass a pattern with the desired number of decimal places:

```python
value = Decimal("19.005")
print(value.quantize(Decimal("0.01")))
# Output: 19.00 (default: banker's rounding)
```

By default, Decimal rounds half “to the even digit” (banker's rounding). For classic commercial rounding up from 5, pass the rounding mode as text:

```python
print(value.quantize(Decimal("0.01"), rounding="ROUND_HALF_UP"))
# Output: 19.01
```

You apply this to a whole column with `apply`:

```python
def round2(x):
    return x.quantize(Decimal("0.01"), rounding="ROUND_HALF_UP")

df["GROSS"] = df["GROSS"].apply(round2)
```

### Creating fixed Decimal columns

This example comes from the SAP help. It creates a column with a very large fixed value:

```python
def transform(data):
    data['start_date_utc_long'] = Decimal('123456789012345678901')
    return data
```

The target column in the output schema there is `decimal(21,0)`, that is 21 digits without decimal places. `int64` could no longer hold such a number.

### Choosing precision and scale correctly

The target column has a **precision** (total digits) and a **scale** (decimal places). `decimal(15,2)` allows 13 digits before and 2 after the decimal point. If you calculate more decimal places, round yourself first. That way you decide the result, not the system.

### When float is fine after all

For key figures such as averages, shares or scores, `float` is perfectly fine. If you come from Decimal, convert deliberately:

```python
def to_float(x):
    return np.nan if pd.isna(x) else float(x)

df["SHARE"] = df["AMOUNT"].apply(to_float) / df["TOTAL"].apply(to_float)
```

The small helper function is necessary because `astype(float)` stops with a `TypeError` on missing values (`pd.NA`).

> Key takeaway: Money = Decimal. Always create Decimal from text, never mix it with float, round yourself.

## Chapter 15: Missing values (NULL, pd.NA)

Missing values are the most common cause of surprising errors in the script operator. A NULL from the database arrives in pandas as `pd.NA`. As soon as a column contains missing values, its dtype is `object`, even if it otherwise contains only numbers.

### The many faces of “empty”

| Notation | Where from | Meaning |
| --- | --- | --- |
| `pd.NA` | NULL from Datasphere | missing value (pandas) |
| `None` | plain Python | missing value (Python) |
| `NaN` | float calculations | “not a number” |
| `NaT` | date columns | “not a time” |
| `""` | empty text | **not** NULL, but a text without characters |
| `0` | number | **not** NULL, but the number zero |

SAP sources in particular often deliver empty texts `""` or zeros where you would expect a NULL. Check your data before building rules.

### Finding missing values

```python
df["CUSTOMER"].isna()     # True where empty
df["CUSTOMER"].notna()    # True where filled
```

`isna()` recognises `pd.NA`, `None`, `NaN` and `NaT` alike. That is convenient.

### Why == None does not work

```python
# WRONG
df[df["CUSTOMER"] == None]
```

Columns with missing values have the type `object` in Datasphere. There, `== None` returns `False` for **every** row, including the empty ones. So the filter finds nothing, without an error message. Always use `isna()` or `notna()`.

### Missing values in comparisons and filters

In an `object` column, a comparison with a missing value gives `False`. Only with `!=` does it give `True`. This has a tricky consequence:

```python
# rows with an empty COUNTRY are dropped here
df[df["COUNTRY"] == "DE"]

# rows with an empty COUNTRY STAY IN here
df[df["COUNTRY"] != "DE"]
```

If you want to exclude missing values, say so explicitly:

```python
df[(df["COUNTRY"] != "DE") & df["COUNTRY"].notna()]
```

With some pandas types that you create yourself with `astype` (for example `"Int64"` or `"string"`), the comparison gives `<NA>` instead. Then the filter fails with the message `Cannot mask with non-boolean array containing NA / NaN values`. `.fillna(False)` on the mask protects you in both cases and never hurts:

```python
mask = (df["QTY"] > 100).fillna(False)
df = df[mask]
```

You already know this from text filters: `na=False` in `str.contains`.

### Filling missing values: fillna

```python
df["COUNTRY"] = df["COUNTRY"].fillna("XX")
df["QTY"]     = df["QTY"].fillna(0)
df["NET"]     = df["NET"].fillna(Decimal("0"))
```

Think about the business meaning first: is a missing quantity really 0? Or is “unknown” the more honest answer? Clarify this with the business department.

### Turning empty texts and placeholders into real NULLs

The other way round, you sometimes want to turn empty texts or SAP placeholders into real missing values:

```python
df["CUSTOMER"] = df["CUSTOMER"].replace({"": pd.NA, "#": pd.NA})
```

In BW, `#` often stands for “not assigned”. Decide consistently how this should look in the target model.

### Calculating with missing values

A calculation with a missing value gives a missing value:

```python
# a row with NET = <NA> gives GROSS = <NA>
df["GROSS"] = df["NET"] * Decimal("1.19")
```

This is usually what you want. If not, fill first with `fillna`.

Careful with `apply` and your own functions: your function then also receives `pd.NA` as a value. Catch it at the start of the function:

```python
def category(qty):
    if pd.isna(qty):
        return "unknown"
    if qty >= 1000:
        return "A"
    return "B"
```

`pd.isna(x)` works for a single value. Without this check, `qty >= 1000` would raise an error for `pd.NA`.

### Removing rows with missing values

```python
# remove rows in which CUSTOMER or COUNTRY is empty
df = df.dropna(subset=["CUSTOMER", "COUNTRY"])
```

Without `subset`, `dropna` removes every row in which any column is empty. That is rarely what you want.

### Counting missing values

For checking while you practise locally:

```python
print(df.isna().sum())
```

This shows for each column how many values are empty.

> Key takeaway: Check for empty with `isna()`, never with `== None`. Watch out for `!=`. Protect masks with `.fillna(False)`. In your own functions, check `pd.isna(x)` first.

## Chapter 16: Matching the output schema

The returned DataFrame must have the same column names and matching data types as the operator's output schema. Otherwise the run fails. This chapter gives you a checklist that gets this right every time.

### Three things must match

1. **Return type:** It must be a DataFrame. Not a Series, not a list, not `None`.
2. **Column names:** Exactly the names from the output schema, spelled the same way.
3. **Data types:** Every column must match its type in the schema.

### 1. Always return a DataFrame

Typical pitfalls:

```python
return df["NET"]      # WRONG: Series
return df[["NET"]]    # correct: DataFrame with one column
```

```python
df.rename(columns={"A": "B"})   # result is lost
return df                       # still has the old names
```

Most pandas commands do not change the DataFrame directly. They return a new one. So always write `df = df.command(...)`.

### 2. Matching column names exactly

Upper and lower case matter. `Net` is not `NET`. Hidden spaces are another classic.

The safest way is a fixed list at the end:

```python
OUTPUT = ["DOC_NO", "COUNTRY", "NET", "GROSS"]

def transform(data):
    df = data.copy()
    df["GROSS"] = df["NET"] * Decimal("1.19")
    return df[OUTPUT]
```

Note: the list may also be outside `transform`. By convention, capital letters signal: this is a fixed value.

If a column is missing, pandas raises a `KeyError` with the missing name. Then you know immediately where to look.

### 3. Making data types match

With `.astype()` you change the type of a column. The table shows what fits which Datasphere type:

| Target in schema | How to prepare the column |
| --- | --- |
| String, LargeString | `df["X"] = df["X"].astype(str)` |
| Integer, Integer64 | `df["X"] = df["X"].astype("int64")` |
| Decimal | values as `Decimal`, see Chapter 14 |
| Boolean | `df["X"] = df["X"].fillna(False).astype(bool)` |
| Date, DateTime, TimeStamp | `df["X"] = pd.to_datetime(df["X"])` |

### Pitfalls when converting

**Missing values and astype(str):** What `pd.NA` becomes depends on the pandas version. In older versions it becomes the text `"<NA>"`, in newer ones it stays a missing value. You almost never want the text `"<NA>"` in the target table. So decide yourself and fill first:

```python
df["X"] = df["X"].fillna("").astype(str)
```

**Missing values and int64:** An `int64` column cannot contain missing values. `astype("int64")` then fails. Fill first with `fillna(0)` if 0 is correct from a business point of view. If missing values should arrive as NULL in the target, leave the column as it came in (`object` with `pd.NA`). Test this behaviour once in your system before you rely on it.

**Missing values and astype(bool):** `pd.NA` cannot be converted to True/False, you get a `TypeError`. So fill first with `fillna(False)`.

**Texts and astype(bool):** Every non-empty text becomes `True`, including `"False"` or `"N"`. So translate flags with a dictionary:

```python
df["CANCELLED"] = df["CANCEL_FLAG"].map({"X": True, "": False}).fillna(False)
```

**Numbers with a comma as text:** `astype(float)` does not understand `"1,5"`. See Chapter 12.

**float into a Decimal column:** Convert deliberately, otherwise rounding errors end up in the target:

```python
df["AMOUNT"] = df["AMOUNT_FLOAT"].apply(
    lambda x: Decimal(str(x)) if pd.notna(x) else pd.NA
)
```

The detour via `str(x)` makes sure Decimal takes the displayed number and not the binary inaccuracy.

### The checklist before deploying

- [ ] Does `transform` end with `return` and return a DataFrame?
- [ ] Is exactly the column list of the output schema selected at the end?
- [ ] Are all column names spelled exactly the same?
- [ ] Do text columns contain no `"<NA>"` or `"nan"` texts?
- [ ] Are amounts Decimal and not float?
- [ ] Are all dates between 1677 and 2262?
- [ ] Does the length of the texts match the length in the schema?

### Checking locally

While practising you can write a small check:

```python
result = transform(data)
print(type(result))      # <class 'pandas.core.frame.DataFrame'>
print(list(result.columns))
print(result.dtypes)
```

Compare the output with the output schema in the operator. Only when everything matches do you copy the code to Datasphere.

> Key takeaway: Return a DataFrame, select columns with a fixed list, convert types deliberately – and handle missing values first.

## Chapter 17: Limits – batches and forbidden functions

The script operator never sees the whole table at once, only packages of rows called batches. In addition, the sandbox blocks certain functions. Knowing these two limits avoids the most expensive mistakes in thinking.

### What is a batch?

For large tables, Datasphere splits the data into several packages. The `transform` function is called separately for each package. So `data` always contains only the rows of the current batch.

This is like data packages in BW: a start routine also sees only its package, not the whole request.

You do not control how large a batch is or how many there are. Your code must deliver the same correct result for any split.

### What works in batch mode?

Everything that treats **each row on its own**:

- calculating new columns from other columns of the same row
- cleaning texts, reformatting dates
- filtering rows
- translating values via a dictionary
- renaming and selecting columns

### What does NOT work reliably?

Everything that needs **several rows together**. The SAP documentation explicitly names removing duplicates as an example.

| Plan | Why it fails | Better like this |
| --- | --- | --- |
| Remove duplicates (`drop_duplicates`) | duplicates can be in different batches | aggregation operator or primary key in the target |
| Totals, averages (`sum`, `mean`) | the result applies to one batch only | aggregation operator |
| Grouping (`groupby`) | groups are torn apart | aggregation operator |
| Share of the grand total | the grand total is unknown | calculate the total beforehand and join it in |
| Ranking, numbering | counting restarts in every batch | move the logic into an SQL view |
| Previous/next row (`shift`) | the neighbouring row may be in another batch | SQL view with a window function |

### The tricky part

When testing with small data volumes, everything often fits into a single batch. Then a `drop_duplicates` seems to deliver the correct result. Only in production with millions of rows do you suddenly get duplicate records or wrong totals.

So check every piece of logic with the question: **Is the result still correct if I cut the table into two halves at any point?**

You can reproduce this test locally:

```python
part1 = data.iloc[:2]
part2 = data.iloc[2:]
result = pd.concat([transform(part1), transform(part2)])
# compare with transform(data)
```

`iloc[:2]` selects the first two rows, `pd.concat` appends DataFrames below each other.

### Forbidden Python functions

These built-in functions trigger an error in the sandbox:

| Function | What it would do |
| --- | --- |
| `eval`, `exec` | run text as code |
| `breakpoint` | start the debugger |
| `memoryview` | direct memory access |
| `__import__` / `import` | load modules |
| `input` | wait for keyboard input |
| `open` | open files |

According to SAP, building your own classes (`class`) and coroutines (`async`) is also restricted.

### Forbidden NumPy functions

All functions that read or write files are blocked: `np.load`, `np.save`, `np.savez`, `np.savez_compressed`, `np.loadtxt`, `np.savetxt`, `np.genfromtxt`, `np.fromregex`, `np.fromfile`, `np.memmap` and `np.DataSource`.

### Forbidden pandas functions

All `read_...` functions are blocked, for example `pd.read_csv`, `pd.read_excel` or `pd.read_json`. So are all `to_...` functions that write to files or databases, for example `to_csv`, `to_excel`, `to_sql` or `to_pickle`.

`to_json`, `to_latex` and `to_markdown` remain allowed. Without parameters they return a text, which you could write into a column, for example.

The list may change with new releases. If you get errors, check the current SAP help for the script operator.

### What this means for your design

- Do not load lookup tables from files. Use joins in the data flow for that.
- Do not call interfaces. External data comes in via connections and replication.
- Build aggregations before or after the script operator with graphical operators.

> Key takeaway: The script operator is for logic per row. Everything that needs several rows together belongs somewhere else.

## Chapter 18: Reading and fixing errors

Errors are part of programming like typos are part of writing. A Python error message almost always tells you exactly what is wrong. You only need to know where to look.

### Where you see errors

- **In the script editor:** Syntax errors such as missing colons are reported, depending on the release, already in the editor or at the latest when the flow runs.
- **In the run log:** You see runtime errors in the **Data Flow Monitor**, in the log information of the respective run. You open it directly from the data flow editor.
- **Locally while practising:** directly below the cell or in the terminal.

### Reading an error message

A Python error message is called a **traceback**. Read it **from bottom to top**. The last line is the most important:

```
Traceback (most recent call last):
  File "<script>", line 4, in transform
    df["GROSS"] = df["NETT"] * Decimal("1.19")
KeyError: 'NETT'
```

This is how you read it:

1. **Last line:** `KeyError: 'NETT'`. There is no column called `NETT`.
2. **Line above:** the code that caused the error.
3. **`line 4`:** the line number in your script.

The fix here is a typo: `NET` instead of `NETT`.

### The most common errors and how to fix them

| Error | Typical cause | Fix |
| --- | --- | --- |
| `SyntaxError` | colon, bracket or quotation mark missing | check this line and the one before carefully |
| `IndentationError` | inconsistent indentation | four spaces per level, no tabs |
| `NameError` | variable not created before use, or misspelled | check spelling, upper/lower case |
| `KeyError` | column or dict key does not exist | check column names, use `.get()` for dicts |
| `TypeError` | wrong types combined, e.g. text + number or Decimal × float | align types (Chapters 14 and 16) |
| `ValueError` | value does not fit, e.g. `int("abc")` | `errors="coerce"` or clean the data |
| `AttributeError` | method does not fit the type, e.g. `.str` on numbers | check the column type, use `.astype(str)` if needed |
| `truth value of a Series is ambiguous` | `if`, `and`, `or` on whole columns | masks with `&`, \` |
| `Cannot mask with ... NA` | missing values in the filter mask | `.fillna(False)` (Chapter 15) |
| Errors about schema or columns | return value does not match the output schema | checklist from Chapter 16 |

### How to hunt for errors

1. **Read the last line of the message.** Which error type, which name?
2. **Find the line number.** Which line in your script is meant?
3. **Make it small.** Comment out parts with `#` until the error disappears. Then you know where it is.
4. **Reproduce it locally.** Build a small DataFrame with exactly the problematic values and test there.
5. **Search for the error.** Copy the last line of the message into a search engine. Very likely someone has had the same problem.

### Using the data flow as a test tool

In the script operator you cannot work with `print`. A trick: write intermediate results into an additional helper column. Add this column temporarily to the output schema:

```python
def transform(data):
    df = data.copy()
    df["DEBUG"] = df["QTY"].astype(str) + " | " + df["COUNTRY"].astype(str)
    # ... further logic ...
    return df[["DOC_NO", "QTY", "COUNTRY", "DEBUG"]]
```

In the data preview or the target table you then see what your code “saw”. Remove the column as soon as everything works.

### Typical thinking errors that produce no error message

The most dangerous errors are those where everything runs, but the result is wrong:

- **Batch logic:** totals or duplicate checks in the script (Chapter 17).
- **Forgotten assignment:** `df.rename(...)` without ` df =  ` in front.
- **Wrong order with `elif`:** the looser condition comes before the stricter one.
- **Empty texts vs. NULL:** `""` is not recognised by `isna()`.
- **`!=` with missing values:** empty rows stay in the filter (Chapter 15).
- **Unsigned Integer:** 3 − 5 gives 254 (Chapter 14).
- **float instead of Decimal:** cent differences in totals.

So after every new script, check a few rows by hand: a typical one, one with missing values, one with extreme values.

> Key takeaway: Read the error message from the bottom, make the problem small, reproduce it locally. And spot-check results by hand.

## Chapter 19: Recipe collection

Here you find ten ready-made recipes for typical tasks in the script operator. Every recipe is batch-safe, does without `import` and can be adapted directly. Column names are examples: replace them with your own.

### Recipe 1: If-then for a whole column (np.where)

`np.where` is Excel's `IF()` for columns: condition, value if yes, value if no.

```python
def transform(data):
    df = data.copy()
    df["ORDER_TYPE"] = np.where(df["QTY"] > 100, "Large", "Normal")
    return df
```

If the condition column contains missing values, protect it: `np.where((df["QTY"] > 100).fillna(False), ...)`.

### Recipe 2: Multi-level rules (np.select)

For `if/elif/else` on columns. The conditions are checked from top to bottom, the first match wins.

```python
def transform(data):
    df = data.copy()
    conditions = [
        df["QTY"] >= 1000,
        df["QTY"] >= 100,
        df["QTY"] > 0,
    ]
    values = ["A", "B", "C"]
    df["CLASS"] = np.select(conditions, values, default="none")
    return df
```

This is faster than `apply` with your own function and easy to read. Both lists must have the same length.

### Recipe 3: Translating values (map)

```python
STATUS_TEXT = {"A": "Open", "B": "In progress", "C": "Done"}

def transform(data):
    df = data.copy()
    df["STATUS_TXT"] = df["STATUS"].map(STATUS_TEXT).fillna("Unknown")
    return df
```

`.map` returns a missing value for keys it does not find. `.fillna` replaces it with a default text.

### Recipe 4: Mapping old to new keys, leaving the rest unchanged

```python
MAPPING = {"400000": "4000000", "400100": "4001000"}

def transform(data):
    df = data.copy()
    df["ACCOUNT_NEW"] = df["ACCOUNT"].replace(MAPPING)
    return df
```

`.replace` changes only the values that are in the dictionary. All others are kept. For many or frequently changing mappings: a mapping table via a join.

### Recipe 5: Cleaning master data keys

```python
def transform(data):
    df = data.copy()
    df["CUSTOMER"] = (
        df["CUSTOMER"]
        .fillna("")
        .astype(str)
        .str.strip()
        .str.upper()
        .str.zfill(10)
    )
    return df
```

The brackets around the whole expression allow line breaks. This keeps a long chain of commands readable.

Careful: a missing value becomes `"0000000000"` here. If empty should stay empty, filter or flag these rows first (Recipe 9).

### Recipe 6: Splitting and building a composite key

```python
def transform(data):
    df = data.copy()
    parts = df["OBJECT_KEY"].str.split("/", expand=True)
    df["BUKRS"] = parts[0]
    df["GJAHR"] = parts[1]
    df["KEY"] = df["BUKRS"] + "_" + df["GJAHR"] + "_" + df["BELNR"]
    return df
```

`GJAHR` is the SAP field name for the fiscal year.

### Recipe 7: SAP date, period and fiscal year

```python
def transform(data):
    df = data.copy()
    txt = df["BUDAT_TXT"].replace({"00000000": pd.NA, "99991231": "22620101"})
    df["BUDAT"] = pd.to_datetime(txt, format="%Y%m%d", errors="coerce")
    df["PERIOD"] = df["BUDAT"].dt.strftime("%Y%m")
    # fiscal year starts in April
    df["GJAHR"] = df["BUDAT"].dt.year + (df["BUDAT"].dt.month >= 4).astype(int) - 1
    return df
```

In the last line, `True` equals 1 and `False` equals 0. A date in May 2026 gives 2026 + 1 − 1 = 2026. A date in February 2026 gives 2026 + 0 − 1 = 2025. Adapt the logic to your fiscal year.

Careful: if `BUDAT` is empty in a row, the whole `GJAHR` column becomes a floating-point column with missing values. An Integer output schema then no longer fits. Remove such rows before the calculation with `df = df[df["BUDAT"].notna()].copy()` and then convert `GJAHR` with `.astype("int64")`.

### Recipe 8: Amount with sign and rounding

```python
def transform(data):
    df = data.copy()
    sign = np.where(df["SHKZG"] == "H", -1, 1)
    df["AMOUNT_SIGNED"] = df["AMOUNT"] * sign

    def round2(x):
        if pd.isna(x):
            return x
        return Decimal(x).quantize(Decimal("0.01"), rounding="ROUND_HALF_UP")

    df["AMOUNT_SIGNED"] = df["AMOUNT_SIGNED"].apply(round2)
    return df
```

You know the debit/credit indicator `SHKZG` from FI: `S` (Soll) is debit, `H` (Haben) is credit. Credit becomes negative. The factor is a whole number, which is why multiplying with Decimal works.

### Recipe 9: Flagging data quality instead of deleting

```python
def transform(data):
    df = data.copy()
    error = (
        df["CUSTOMER"].isna()
        | (df["CUSTOMER"] == "")
        | ~df["COUNTRY"].isin(["DE", "AT", "CH"])
    )
    df["DQ_FLAG"] = np.where(error.fillna(True), "CHECK", "OK")
    return df
```

Instead of silently removing faulty rows, you flag them. This way the business department can evaluate them later.

### Recipe 10: Cleaning all text columns at once

```python
TEXT_COLUMNS = ["NAME", "CITY", "STREET"]

def transform(data):
    df = data.copy()
    for col in TEXT_COLUMNS:
        df[col] = (
            df[col]
            .str.strip()
            .str.replace(r"\s+", " ", regex=True)
        )
    return df
```

The loop runs over three column names, not over rows. Each pass processes a whole column.

### Combining recipes

In practice you put several recipes one after another into one `transform` function. Stick to the structure from Chapter 8: copy, clean, calculate, filter, select columns, return.

> Key takeaway: `np.where` for yes/no, `np.select` for levels, `.map` and `.replace` for mappings.

## Chapter 20: Exercises with solutions

Ten exercises lead you from your first expression to a complete script operator. Try each exercise yourself first before you read the solution. All exercises use the example DataFrame from Chapter 9.

### Exercise 1: Variables

Create the variables `quantity = 8` and `price = 12.5`. Calculate the total value and print it with an f-string: “Total value: 100.0”.

**Solution:**

```python
quantity = 8
price = 12.5
total = quantity * price
print(f"Total value: {total}")
```

### Exercise 2: Splitting text

From `doc = "1000-2026-4711"` you should get company code, year and document number.

**Solution:**

```python
doc = "1000-2026-4711"
parts = doc.split("-")
bukrs = parts[0]   # "1000"
gjahr = parts[1]   # "2026"
belnr = parts[2]   # "4711"
```

### Exercise 3: Condition

Write a function `traffic_light(value)` that returns “red” if the value is below 0, “amber” up to and including 100, and “green” otherwise.

**Solution:**

```python
def traffic_light(value):
    if value < 0:
        return "red"
    elif value <= 100:
        return "amber"
    else:
        return "green"
```

### Exercise 4: Selecting columns

Write a `transform` function that returns only `DOC_NO` and `NET`.

**Solution:**

```python
def transform(data):
    return data[["DOC_NO", "NET"]]
```

Do not forget the double brackets. Otherwise a Series comes back.

### Exercise 5: New column

Add the column `UNIT_PRICE` as `NET` divided by `QTY`. Return `DOC_NO`, `QTY`, `NET` and `UNIT_PRICE`.

**Solution:**

```python
def transform(data):
    df = data.copy()
    df["UNIT_PRICE"] = df["NET"] / df["QTY"]
    return df[["DOC_NO", "QTY", "NET", "UNIT_PRICE"]]
```

### Exercise 6: Filtering

Keep only rows from Germany or Austria with a quantity above 5.

**Solution:**

```python
def transform(data):
    mask = data["COUNTRY"].isin(["DE", "AT"]) & (data["QTY"] > 5)
    return data[mask]
```

Result: documents 4711 and 4712.

### Exercise 7: Classifying

Create the column `CLASS`: “A” from quantity 1000, “B” from 100, otherwise “C”. Use `np.select`.

**Solution:**

```python
def transform(data):
    df = data.copy()
    df["CLASS"] = np.select(
        [df["QTY"] >= 1000, df["QTY"] >= 100],
        ["A", "B"],
        default="C",
    )
    return df
```

Result: 4711 = C, 4712 = B, 4713 = C, 4714 = A.

### Exercise 8: Country names

Add `COUNTRY_TEXT` with the full country names. Unknown countries should be called “Other”.

**Solution:**

```python
COUNTRIES = {"DE": "Germany", "AT": "Austria", "CH": "Switzerland"}

def transform(data):
    df = data.copy()
    df["COUNTRY_TEXT"] = df["COUNTRY"].map(COUNTRIES).fillna("Other")
    return df
```

### Exercise 9: Missing values

Suppose `CUSTOMER` contains missing values. Fill them with “UNKNOWN” and write all customer numbers in capitals.

**Solution:**

```python
def transform(data):
    df = data.copy()
    df["CUSTOMER"] = df["CUSTOMER"].fillna("unknown").str.upper()
    return df
```

The order matters: fill first, then apply `.str`.

### Exercise 10: Final test

The output schema has the columns `DOC_NO` (String), `COUNTRY_TEXT` (String), `NET` (Decimal), `GROSS` (Decimal) and `CLASS` (String). The input column `NET` is Decimal. Build the complete operator:

- only rows with `NET` greater than 0
- `GROSS` with 19 % VAT, rounded commercially to 2 decimal places
- `COUNTRY_TEXT` as in Exercise 8
- `CLASS` as in Exercise 7

**Solution:**

```python
COUNTRIES = {"DE": "Germany", "AT": "Austria", "CH": "Switzerland"}
OUTPUT = ["DOC_NO", "COUNTRY_TEXT", "NET", "GROSS", "CLASS"]

def transform(data):
    def round2(x):
        if pd.isna(x):
            return x
        return x.quantize(Decimal("0.01"), rounding="ROUND_HALF_UP")

    # 1. filter and copy
    mask = (data["NET"] > Decimal("0")).fillna(False)
    df = data[mask].copy()

    # 2. calculate
    df["GROSS"] = (df["NET"] * Decimal("1.19")).apply(round2)
    df["COUNTRY_TEXT"] = df["COUNTRY"].map(COUNTRIES).fillna("Other")
    df["CLASS"] = np.select(
        [df["QTY"] >= 1000, df["QTY"] >= 100],
        ["A", "B"],
        default="C",
    )

    # 3. output schema
    return df[OUTPUT]
```

If you managed this exercise without looking, you are ready for your first real script operator.

> Key takeaway: Practising beats reading. Rebuild every solution locally and then change it yourself.

## Appendix: Cheat sheet and glossary

This appendix is for looking things up. Keep it next to your keyboard when you build your first operator.

### The basic template

```python
OUTPUT = ["COLUMN1", "COLUMN2", "COLUMN3"]

def transform(data):
    df = data.copy()          # 1. copy
    # 2. clean
    # 3. calculate
    # 4. filter (then .copy())
    return df[OUTPUT]         # 5. output schema
```

### Cheat sheet pandas

| Task | Code |
| --- | --- |
| get a column | `df["A"]` |
| select columns | `df[["A", "B"]]` |
| new column | `df["C"] = df["A"] * 2` |
| fixed value | `df["SOURCE"] = "ERP"` |
| rename | `df = df.rename(columns={"A": "X"})` |
| remove | `df = df.drop(columns=["A"])` |
| filter | `df[df["A"] > 5]` |
| and / or / not | `(b1) & (b2)`, \`(b1) |
| value in list | `df["A"].isin(["x", "y"])` |
| range | `df["A"].between(1, 10)` |
| empty / filled | `df["A"].isna()`, `df["A"].notna()` |
| fill empty | `df["A"].fillna(0)` |
| change type | `df["A"].astype(str)` |
| text to number | `pd.to_numeric(df["A"], errors="coerce")` |
| if-then | `np.where(cond, yes, no)` |
| levels | `np.select([c1, c2], [v1, v2], default=v3)` |
| translate | `df["A"].map(dict)` |
| own function | `df["A"].apply(function)` |

### Cheat sheet text (.str)

| Task | Code |
| --- | --- |
| remove spaces | `.str.strip()` |
| upper / lower | `.str.upper()`, `.str.lower()` |
| replace | `.str.replace("a", "b", regex=False)` |
| slice | `.str[0:4]`, `.str[-3:]` |
| split | `.str.split("-", expand=True)` |
| pad with zeros | `.str.zfill(10)` |
| remove zeros | `.str.lstrip("0")` |
| contains | `.str.contains("x", na=False)` |
| starts with | `.str.startswith("x", na=False)` |
| extract pattern | `.str.extract(r"(\d{5})", expand=False)` |

### Cheat sheet dates (.dt)

| Task | Code |
| --- | --- |
| text to date | `pd.to_datetime(s, format="%Y%m%d", errors="coerce")` |
| year, month, day | `.dt.year`, `.dt.month`, `.dt.day` |
| quarter | `.dt.quarter` |
| as text | `.dt.strftime("%d.%m.%Y")` |
| add days | `+ pd.Timedelta(days=30)` |
| add months | `+ pd.DateOffset(months=1)` |
| end of month | `+ pd.offsets.MonthEnd(0)` |

### Cheat sheet Decimal

| Task | Code |
| --- | --- |
| fixed value | `Decimal("1.19")` |
| commercial rounding | `x.quantize(Decimal("0.01"), rounding="ROUND_HALF_UP")` |
| from float | `Decimal(str(x))` |
| to float (function from Chapter 14) | `.apply(to_float)` |

### The ten golden rules

1. Graphical operators first, Python only for complex logic.
2. The function is always called `transform` and always returns a DataFrame.
3. No `import`. `pd`, `np` and `Decimal` are already there.
4. First `.copy()`, then change.
5. At the end, select exactly the columns of the output schema.
6. Process columns instead of rows, loop only over column names.
7. For columns use `&`, `|`, `~` and brackets around every condition.
8. Check missing values with `isna()`, watch out for `!=`, protect masks with `.fillna(False)`.
9. Money as Decimal, Decimal from text, never mixed with float.
10. No logic across several rows: batches!

### Glossary

| Term | Explanation |
| --- | --- |
| Batch | subset of the rows that `transform` receives at once |
| Boolean | truth value: `True` or `False` |
| DataFrame | table in pandas with rows and named columns |
| Data flow | graphical data processing in the Data Builder of Datasphere |
| Decimal | exact number type for amounts |
| Dictionary | lookup table with key and value, `{ }` |
| dtype | data type of a column in pandas |
| Function | named code building block with `def`, takes values and returns one |
| Indentation | spaces at the start of a line, define code blocks |
| Index | row numbering of a DataFrame |
| Library | ready-made collection of functions, here pandas and NumPy |
| List | ordered row of values, `[ ]` |
| Mask | Series of `True`/`False` used for filtering |
| `pd.NA` | missing value (NULL) in pandas |
| Operator | building block in a data flow |
| Output schema | defined columns and types at the operator's output |
| Parameter | placeholder for a value that a function takes |
| Sandbox | shielded environment with restricted functions |
| Series | a single column in pandas |
| String | text |
| Traceback | Python error message, read from bottom to top |
| Variable | named storage place for a value |
| Vectorised | calculating on a whole column instead of row by row |

### Where to go from here

Once you have worked through this book, you can solve most tasks in the script operator. For deeper study, the official pandas documentation (section “10 minutes to pandas”) and the SAP help on the script operator in Datasphere are good next steps. Check the list of blocked functions there regularly, as it may change with releases.

Good luck with your first data flow!
