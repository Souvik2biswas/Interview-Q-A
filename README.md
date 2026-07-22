# Interview Prep: Questions & Answers

Concise, interview-ready answers for all 190 questions from your skill verification question set, organized into the same 19 sections. Code snippets are included where a quick example clarifies the concept better than prose alone.

## Table of Contents

1. [Python (Core)](#1-python-core)
2. [OOP in Python](#2-oop-in-python)
3. [Python Libraries](#3-python-libraries)
4. [NumPy](#4-numpy)
5. [Pandas](#5-pandas)
6. [Matplotlib](#6-matplotlib)
7. [Seaborn](#7-seaborn)
8. [Statistical Analysis](#8-statistical-analysis)
9. [Descriptive Statistics](#9-descriptive-statistics)
10. [Inferential Statistics](#10-inferential-statistics)
11. [Probability Theory](#11-probability-theory)
12. [Machine Learning](#12-machine-learning)
13. [Supervised Learning](#13-supervised-learning)
14. [Unsupervised Learning](#14-unsupervised-learning)
15. [Linear Regression](#15-linear-regression)
16. [Decision Tree](#16-decision-tree)
17. [NLP](#17-nlp)
18. [MySQL](#18-mysql)
19. [Advanced Excel](#19-advanced-excel)

---

## 1. Python (Core)

**1. What are mutable and immutable data types?**
Mutable objects can have their state or content modified after creation without changing their memory identity (`id()`). Examples include `list`, `dict`, and `set`. Immutable objects cannot be altered once created; any apparent modification creates a brand-new object in memory with a different `id()`. Examples include `int`, `float`, `str`, `tuple`, and `frozenset`.
- **Key Caveat:** A `tuple` is immutable, but if it contains a reference to a mutable object (like `(1, [2, 3])`), that nested list can still be modified in place.
- **Common Interview Pitfall:** Using a mutable object as a default argument in a function signature (`def fn(x=[])`) causes shared state across all function calls. Always use `None` as the default value instead (`def fn(x=None): if x is None: x = []`).

**2. Difference between list and tuple?**
- **Mutability:** Lists (`[1, 2]`) are mutable (dynamic sizing via append/remove); tuples (`(1, 2)`) are immutable.
- **Memory & Performance:** Tuples are allocated in contiguous memory blocks with fixed sizing, making them faster to construct/iterate over and more memory-efficient (`sys.getsizeof()`).
- **Hashability & Use Cases:** Tuples containing only immutable elements are hashable (`__hash__`), meaning they can serve as dictionary keys or set members. Lists cannot. Use tuples for fixed heterogenous records (e.g., coordinate pairs `(x, y)`), and lists for homogenous collections of dynamic length.

**3. What is list comprehension?**
List comprehension offers a concise, declarative syntax for generating a new list from an existing iterable:
```python
# [expression for item in iterable if condition]
even_squares = [x**2 for x in range(10) if x % 2 == 0]
```
- **Performance:** Executes at C-level speed in CPython by avoiding repeated bytecode lookup overhead for `.append()`.
- **Best Practices:** Prefer list comprehensions for straightforward filtering and transformations. For nested loops or multi-step logic, standard `for` loops maintain better readability. When producing massive datasets, use generator expressions `(x**2 for x in ...)` to avoid loading the entire list into memory.

**4. What are *args and **kwargs?**
`*args` and `**kwargs` allow a function to accept an arbitrary number of positional and keyword arguments:
- `*args` packs excess positional arguments into a **tuple**.
- `**kwargs` packs excess keyword arguments into a **dictionary**.
```python
def make_request(url, *args, timeout=30, **kwargs):
    print("Positional extras:", args)   # e.g., (1, 2)
    print("Keyword extras:", kwargs)    # e.g., {'headers': {'User-Agent': 'Bot'}}

make_request("https://api.com", 1, 2, timeout=60, headers={'User-Agent': 'Bot'})
```
- **Argument Order:** Function signatures must strictly follow: `standard args -> *args -> keyword-only args -> **kwargs`. They can also be used to unpack iterables and dicts during function calls (`func(*lst, **dct)`).

**5. Difference between shallow copy and deep copy?**
- **Shallow Copy (`copy.copy()` or `.copy()`):** Constructs a new top-level container object, but populates it with references to the child objects contained in the original. Altering a nested mutable object in the copy will mutate the original object too.
- **Deep Copy (`copy.deepcopy()`):** Recursively copies the container AND all nested objects within it, producing an entirely independent object graph.
```python
import copy
original = [[1, 2], [3, 4]]
shallow = copy.copy(original)
deep = copy.deepcopy(original)

original[0][0] = 999
# shallow[0][0] is now 999 (shared nested reference)
# deep[0][0] remains 1 (fully isolated copy)
```

**6. What is lambda function?**
An anonymous, single-expression inline function declared with the `lambda` keyword:
```python
# Syntax: lambda arguments: expression
add = lambda x, y: x + y
```
- **Use Cases:** Commonly passed as short key functions to higher-order built-ins like `sorted()`, `map()`, `filter()`, or Pandas `.apply()`. E.g., `sorted(pairs, key=lambda x: x[1])`.
- **Limitations:** Cannot contain multi-line logic, statements (like `pass` or `assert`), or type annotations. Overusing lambdas for complex logic impairs code readability; use standard `def` functions instead.

**7. What is generator and yield?**
A generator is a specialized iterator function that produces values on-demand using `yield` instead of `return`. When `yield` is reached, execution pauses, preserving the function's local scope and stack state until the next item is requested via `next()`.
```python
def stream_large_file(file_path):
    with open(file_path) as f:
        for line in f:
            yield line.strip()

# Consuming items lazily without loading the whole file into RAM
for line in stream_large_file("huge_log.txt"):
    process(line)
```
- **Memory Efficiency:** Achieves $O(1)$ space complexity for streaming or large sequences, making it ideal for processing large files, database streams, or infinite series.

**8. What is GIL (Global Interpreter Lock)?**
The Global Interpreter Lock (GIL) is a mutual exclusion lock used by CPython to ensure only one thread executes Python bytecode at any given instant.
- **Why it exists:** Simplifies CPython memory management by ensuring thread-safe reference counting (`sys.getrefcount`) without requiring fine-grained locks around every object.
- **Performance Impact:**
  - **CPU-bound operations** (e.g., heavy math, image rendering): Threads compete for the GIL, providing no multi-core performance gains. Use `multiprocessing` or C-extensions (NumPy, PyTorch) that release the GIL during execution.
  - **I/O-bound operations** (e.g., web requests, disk IO): Threads release the GIL while waiting for I/O, enabling concurrent execution.
- **Modern Context:** PEP 703 enables optional GIL-disabled builds (free-threaded Python 3.13+).

**9. Difference between == and is?**
- `==` checks **value equality** by invoking the `__eq__()` magic method to determine if two objects contain equal data.
- `is` checks **referential identity** (`id(a) == id(b)`), verifying whether both variables point to the exact same memory address.
```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b  # True (equal content)
a is b  # False (different objects in memory)
```
- **Small Integer/String Interning:** CPython pre-allocates small integers (-5 to 256) and single-word strings, so `x = 100; y = 100; x is y` evaluates to `True`. Always use `is` when comparing against `None` (`if val is None:`).

**10. What are decorators?**
A decorator is a design pattern used to modify or wrap the execution of a function or class without permanently changing its source code. Syntactically applied using `@decorator_name` above the target function.
- **Mechanism:** Takes a target function as an argument, defines an inner wrapper function that adds pre/post logic, and returns the wrapper.
```python
import functools, time

def time_it(func):
    @functools.wraps(func)  # Preserves function metadata (__name__, docstrings)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time() - start:.4f}s")
        return result
    return wrapper

@time_it
def compute():
    return sum(range(1_000_000))
```


## 2. OOP in Python

**1. What is class and object?**
- **Class:** A user-defined logical prototype or blueprint defining state (attributes) and behavior (methods) common to all objects of that type.
- **Object:** A concrete instance of a class stored in memory with specific attribute values.
```python
class BankAccount:
    bank_name = "Global Bank"  # Class attribute (shared by all instances)

    def __init__(self, owner, balance):
        self.owner = owner     # Instance attribute (unique to each object)
        self.balance = balance

acc = BankAccount("Alice", 1000)  # Instantiation
```

**2. Explain encapsulation with example**
Encapsulation bundles data (attributes) and methods operating on that data within a single class, while restricting direct access to internal state to maintain object integrity.
In Python, access restrictions rely on naming conventions:
- `_protected`: Indicates intended non-public use within class hierarchy.
- `__private`: Triggers **Name Mangling** (`_ClassName__attribute`), preventing direct external access.
```python
class Account:
    def __init__(self, balance):
        self.__balance = balance  # Private attribute

    def deposit(self, amount):
        if amount > 0: self.__balance += amount

    @property
    def balance(self):  # Getter property
        return self.__balance
```

**3. What is inheritance? Types?**
Inheritance enables a child class to inherit attributes and methods from a parent class, promoting code reusability and establishing an "is-a" relationship.
- **Types of Inheritance:**
  1. **Single:** One child inherits from one parent (`Child(Parent)`).
  2. **Multiple:** One child inherits from multiple parents (`Child(ParentA, ParentB)`).
  3. **Multilevel:** Inheritance chain across three or more levels (`GrandChild(Child)`).
  4. **Hierarchical:** Multiple child classes inherit from a single parent class.
  5. **Hybrid:** A combination of two or more inheritance structures.

**4. What is polymorphism?**
Polymorphism ("many forms") allows objects of different classes to respond to the same method call in class-specific ways.
- **Duck Typing ("If it walks like a duck..."):** Python relies heavily on dynamic polymorphism based on interfaces rather than strict class hierarchies. If an object responds to `.read()`, it can be processed as a file-like stream.
```python
class Dog:
    def speak(self): return "Woof!"
class Cat:
    def speak(self): return "Meow!"

def make_speak(animal):
    print(animal.speak())  # Polymorphic method call

make_speak(Dog())  # Woof!
make_speak(Cat())  # Meow!
```

**5. What is abstraction?**
Abstraction hides low-level implementation details and presents only high-level essential interfaces to users.
In Python, abstraction is enforced using the `abc` (Abstract Base Class) module:
```python
from abc import ABC, abstractmethod

class DatabaseConnector(ABC):
    @abstractmethod
    def connect(self):
        pass  # Subclasses MUST implement this method

class PostgresConnector(DatabaseConnector):
    def connect(self):
        print("Connected to PostgreSQL")
```
- **Rule:** Attempting to instantiate a class containing unimplemented `@abstractmethod` signatures raises a `TypeError`.

**6. Method overloading vs overriding**
- **Method Overloading:** Defining multiple methods with the same name but different signatures within the same class. Python **does not** natively support compile-time method overloading (later definitions overwrite earlier ones); it is simulated via default arguments (`def f(x=None)`), `*args`, or `@functools.singledispatch`.
- **Method Overriding:** A subclass redefines a method from its parent class using the exact same signature to provide subclass-specific behavior.

**7. What is constructor (__init__)?**
`__init__` is a dunder method invoked automatically after a new object instance has been created in memory. Its primary role is to initialize instance variables.
- **Nuance (`__new__` vs `__init__`):** `__new__(cls)` is the actual allocator method that creates and returns the raw instance; `__init__(self)` takes that instance as `self` to set up initial state.

**8. What is super() function?**
`super()` returns a proxy object that delegates method calls to a parent or sibling class based on the **Method Resolution Order (MRO)**.
```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Executes Animal.__init__
        self.breed = breed
```
- **Multiple Inheritance:** `super()` ensures cooperative multiple inheritance without hardcoding base class names or duplicating parent calls up diamond trees.

**9. What are dunder methods?**
Dunder ("double underscore") or magic methods are built-in hooks that allow user-defined classes to integrate with Python's syntax, built-in functions, and operators:
- `__repr__(self)` & `__str__(self)`: Unambiguous string representation for developers vs clean output for users.
- `__len__(self)`: Enables `len(obj)`.
- `__getitem__(self, key)`: Enables indexing (`obj[key]`).
- `__add__(self, other)`: Enables `+` operator overloading.
- `__enter__` & `__exit__`: Enables context management (`with` statements).

**10. What is multiple inheritance?**
Multiple inheritance occurs when a child class inherits directly from two or more parent classes (`class Child(BaseA, BaseB):`).
- **The Diamond Problem:** Occurs when two parent classes inherit from a common grandparent and a child inherits from both parents. Python resolves method lookup order using **C3 Linearization** algorithm to build a deterministic **Method Resolution Order (MRO)**.
- **MRO Inspection:** Check lookup order via `Child.mro()` or `Child.__mro__`.

## 3. Python Libraries

**1. Difference between NumPy and Pandas**
- **NumPy:** Built around `ndarray`, an optimized n-dimensional array for homogeneous numeric data. Focused on numerical linear algebra, mathematical vectorization, and multi-dimensional matrices.
- **Pandas:** Built on top of NumPy, providing `Series` (1D) and `DataFrame` (2D) structures designed for heterogeneous tabular data (numbers, strings, timestamps). Includes rich capabilities for missing data imputation, SQL-style joins, pivoting, time-series operations, and group-by aggregations.

**2. What is Matplotlib used for?**
Matplotlib is Python's primary 2D data visualization library. It offers full programmatic control over every plot element (axes, ticks, labels, legends, annotations) to construct publication-ready figures.
- **Object-Oriented API vs Pyplot:** The object-oriented approach (`fig, ax = plt.subplots()`) is preferred over the `pyplot` state-machine interface (`plt.plot()`) for multi-panel, custom visualizations.

**3. What is Seaborn?**
Seaborn is a high-level statistical visualization library built on top of Matplotlib and integrated with Pandas DataFrames.
- **Features:** Provides polished default color palettes and themes, specialized statistical visualizations out of the box (heatmaps, pairplots, violin plots, regression plots), and automatic statistical summaries with confidence intervals.

**4. What is Scikit-learn?**
Scikit-learn (`sklearn`) is the standard Python toolkit for classical machine learning built on NumPy, SciPy, and Matplotlib.
- **Unified Estimator API:**
  - `.fit(X, y)`: Train models on feature matrix `X` and target `y`.
  - `.predict(X)` / `.predict_proba(X)`: Generate predictions.
  - `.transform(X)` / `.fit_transform(X)`: Feature scaling and encoding.
- Includes comprehensive modules for preprocessing, cross-validation, hyperparameter tuning (`GridSearchCV`), and model metrics.

**5. What is TensorFlow / PyTorch?**
Both are major open-source deep learning frameworks providing automatic differentiation (`autograd`) and hardware acceleration (GPU/TPU).
- **PyTorch (Meta):** Uses dynamic computation graphs (eager execution), making debugging intuitive with standard Python tools. Preferred in AI research.
- **TensorFlow (Google):** Features the high-level Keras API and static graph compilation (`tf.function`). Highly optimized for production deployment environments (`TF Serving`, `TF Lite`, `TF.js`).

**6. What is OpenCV?**
OpenCV (`cv2`) is an open-source real-time computer vision and image processing framework.
- **Data Model:** Reads images into memory as 3D NumPy arrays (`[height, width, channels]`).
- **Gotcha:** OpenCV defaults to **BGR** color channel ordering (`cv2.imread`), requiring explicit conversion (`cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`) before rendering in Matplotlib.

**7. What is requests library?**
`requests` is an HTTP client library for Python designed for clean interaction with web APIs:
```python
import requests
resp = requests.get('https://api.github.com/events', timeout=5)
if resp.status_code == 200:
    data = resp.json()  # Decodes JSON body into Python dict/list
```

**8. What is BeautifulSoup?**
BeautifulSoup (`bs4`) parses HTML and XML documents into a navigable DOM tree, enabling web scrapers to extract data using tag names, CSS selectors (`soup.select()`), or attribute filters. Commonly paired with `requests`.

**9. What is pickle used for?**
`pickle` serializes Python object hierarchies into binary byte streams (`pickle.dump()`) and deserializes them back (`pickle.load()`). Used for saving trained ML models or complex Python structures to disk.
- **Security Alert:** Never unpickle unverified files from untrusted sources, as arbitrary Python code can be executed during deserialization.

**10. What is joblib?**
`joblib` provides utilities for lightweight pipelining and caching in Python.
- **Model Storage:** Superior to `pickle` for serializing large NumPy arrays and Scikit-learn models due to optimized memory-mapping.
- **Parallelism:** Offers `Parallel(n_jobs=-1)` and `delayed()` to easily execute loops across multiple CPU cores.

## 4. NumPy

**1. What is ndarray?**
`ndarray` (N-dimensional array) is NumPy's core data structure representing a homogeneous, multidimensional grid of items of fixed type and size. Stored as a contiguous C or Fortran memory buffer associated with metadata (dtype, shape, strides).

**2. Why NumPy is faster than Python lists?**
1. **Contiguous Memory:** Data elements occupy continuous memory blocks, maximizing CPU cache efficiency. Python lists store pointers to scattered Python objects.
2. **Homogeneous Typing:** Uniform dtypes eliminate runtime per-element type checking.
3. **C & SIMD Optimization:** Array operations execute via compiled C/Fortran routines utilizing SIMD (Single Instruction, Multiple Data) CPU vector instructions.

**3. What is vectorization?**
Vectorization replaces explicit Python `for` loops with batch operations on whole arrays. Universal functions (`ufuncs`) execute element-wise operations directly in C:
```python
import numpy as np
# Vectorized element-wise addition running in C
res = np.add(arr1, arr2)  # or arr1 + arr2
```

**4. Difference between reshape and resize**
- `np.reshape()`: Returns a view of the array with a new shape without altering underlying data (element count must remain equal: $rows \times cols = N$).
- `ndarray.resize()`: Modifies array dimensions **in-place**. Can alter total element count, padding extra slots with zeros or truncating existing data.

**5. What is broadcasting?**
Broadcasting allows NumPy to perform element-wise operations on arrays of different shapes without making unneeded memory copies.
- **Broadcasting Rules:** Dimensions align from right to left and are compatible if:
  1. They are equal, OR
  2. One of the dimensions is 1.
```python
A = np.ones((3, 4))   # Shape (3, 4)
B = np.arange(4)      # Shape (4,) -> expanded to (1, 4)
C = A + B             # B is broadcast along axis 0 to shape (3, 4)
```

**6. What is slicing in NumPy?**
Extracting sub-arrays via `arr[start:stop:step]` across one or more dimensions.
- **Views vs Copies:** NumPy slicing returns **views** pointing to original memory (unlike Python list slices which return new copies). Modifying a slice alters the source array. Use `.copy()` to create an isolated array copy.

**7. Difference between np.array and list**
| Feature | Python List | NumPy ndarray |
| :--- | :--- | :--- |
| **Data Types** | Heterogeneous (mixed types allowed) | Homogeneous (single fixed dtype) |
| **Memory Layout** | Array of object pointers | Contiguous memory block |
| **Operations** | Element append/concatenation | Vectorized mathematical operations |
| **Performance** | Slower, high overhead | Fast C-level execution |

**8. What is axis in NumPy?**
An axis represents a dimension along an array:
- In a 2D array of shape `(rows, cols)`:
  - `axis=0`: Represents the vertical direction (down rows). Aggregations (`np.sum(arr, axis=0)`) collapse rows to calculate column totals.
  - `axis=1`: Represents the horizontal direction (across columns). Aggregations collapse columns to calculate row totals.

**9. How to handle missing values?**
NumPy represents missing numeric data using floating-point `np.nan` (Not a Number), conforming to IEEE 754.
- **Filtering & Masking:** `np.isnan(arr)` returns a boolean mask. Filter values via `arr[~np.isnan(arr)]`.
- **NaN-Safe Functions:** Standard operations propagate `nan` (`np.sum`, `np.mean`). Use NaN-aware functions that ignore missing entries: `np.nansum()`, `np.nanmean()`, `np.nanmedian()`, `np.nanstd()`.

**10. Random module usage**
`numpy.random` generates pseudo-random samples for statistical simulation and ML.
- **Modern API (`default_rng`):** Replaces legacy functions like `np.random.seed()` with PCG64 bit generators:
```python
rng = np.random.default_rng(seed=42)
uniform_vals = rng.uniform(0, 1, size=10)
normal_vals  = rng.normal(loc=0, scale=1, size=(3, 3))
shuffled_arr = rng.choice([10, 20, 30, 40], size=5, replace=True)
```

## 5. Pandas

**1. What is DataFrame?**
A DataFrame is a 2-dimensional, heterogeneously typed, tabular data structure with aligned row and column labels. Conceptually similar to a SQL table or Excel spreadsheet built on top of NumPy arrays.
- **Components:** Consists of three parts: Data values, an **Index** (row labels), and **Columns** (column labels). Each column is a 1D Pandas `Series`.

**2. loc vs iloc**
- `.loc[]`: **Label-based** indexing. Takes row/column names. Slicing with `.loc['a':'c']` is **inclusive** of both the start and end labels.
- `.iloc[]`: **Integer-positional** indexing (0-indexed). Takes integer positions. Slicing with `.iloc[0:3]` is **exclusive** of the end index (matches standard Python slice semantics).
```python
# Label-based: Rows 'r1' to 'r3', columns 'age' and 'salary'
df.loc['r1':'r3', ['age', 'salary']]

# Integer-positional: Rows 0 to 2, columns 0 and 1
df.iloc[0:3, 0:2]
```

**3. isnull vs notnull**
- `isnull()` (alias `isna()`): Returns a boolean DataFrame/Series with `True` for missing (`NaN`, `None`, `NaT`) values.
- `notnull()` (alias `notna()`): Returns `True` for valid, non-missing values.
- Used for masking and aggregation: `df['col'].isnull().sum()` counts missing entries in a column.

**4. merge vs join**
- `merge()`: Flexible function for combining DataFrames based on values in shared join key columns (similar to SQL `INNER`, `LEFT`, `RIGHT`, `OUTER` joins): `pd.merge(df1, df2, on='user_id', how='left')`.
- `join()`: Convenience method that joins DataFrames primarily on their **indexes** rather than arbitrary columns: `df1.join(df2, how='inner')`.

**5. groupby usage**
Follows the **Split-Apply-Combine** strategy:
1. **Split:** Divides data into groups based on specified keys.
2. **Apply:** Applies an aggregation (`sum`, `mean`, `count`), transformation (`z-score`), or filter to each group.
3. **Combine:** Merges results into a single output object.
```python
# Group by department, calculate total sales and mean age
df.groupby('dept').agg(total_sales=('sales', 'sum'), avg_age=('age', 'mean'))
```

**6. apply vs map vs applymap**
- `Series.map()`: Element-wise substitution on a single Series using a dict, Series, or function.
- `Series.apply()` / `DataFrame.apply()`: Applies a function along an axis of a DataFrame (`axis=0` for columns, `axis=1` for rows) or element-wise across a Series.
- `DataFrame.applymap()`: Applies an element-wise function across an entire DataFrame. *(Note: Renamed to `DataFrame.map()` in Pandas 2.1+)*.

**7. Handling missing values**
- **Detection:** `df.isna().sum()`.
- **Removal:** `df.dropna(axis=0, how='any', subset=['col'])`.
- **Imputation:** `df.fillna(val)` using constant values, statistical measures (`df['age'].mean()`), forward-fill (`method='ffill'`), or backward-fill (`method='bfill'`).
- **Interpolation:** `df['val'].interpolate(method='linear')` estimates missing continuous values based on surrounding rows.

**8. Sorting data**
- `df.sort_values(by=['col1', 'col2'], ascending=[True, False])`: Sorts rows by values in specified columns.
- `df.sort_index(ascending=True)`: Sorts the DataFrame by row or column index labels.

**9. Filtering rows**
- **Boolean Indexing:** `df[(df['age'] > 25) & (df['status'] == 'Active')]` *(Use bitwise `&`, `|`, `~` with parentheses)*.
- `.isin()`: `df[df['city'].isin(['NY', 'LA'])]`.
- `.query()`: SQL-like expression evaluation: `df.query("age > 25 and status == 'Active'")`.

**10. Reading different file formats**
Pandas provides high-level I/O readers returning DataFrames and matching writers:
- CSV: `pd.read_csv()` / `df.to_csv()`
- Excel: `pd.read_excel()` / `df.to_excel()`
- Parquet: `pd.read_parquet()` / `df.to_parquet()` *(Fast columnar storage)*
- SQL: `pd.read_sql(query, engine)` / `df.to_sql()`
- JSON: `pd.read_json()` / `df.to_json()`

## 6. Matplotlib

**1. What is Matplotlib?**
Matplotlib is Python's foundational, low-level plotting library. It provides granular programmatic control over figure architecture, axes, tick positioning, color mapping, and layout rendering.

**2. Types of plots**
- **Line plot (`ax.plot`):** Shows trends over continuous variables (time series).
- **Bar chart (`ax.bar` / `ax.barh`):** Compares quantities across categorical variables.
- **Histogram (`ax.hist`):** Visualizes frequency distribution of continuous variables.
- **Scatter plot (`ax.scatter`):** Displays bivariate relationship/correlation between two numeric variables.
- **Box plot (`ax.boxplot`):** Depicts 5-number summary (Min, Q1, Median, Q3, Max) and outliers.
- **Pie chart (`ax.pie`):** Shows relative proportions of parts to a whole (best limited to < 5 categories).

**3. Difference between plot and scatter**
- `plt.plot()` / `ax.plot()`: Draws points connected by line segments. Fast rendering; ideal for continuous time-series data. Markers have uniform styling.
- `plt.scatter()` / `ax.scatter()`: Draws individual unconnected data points. Supports per-point dynamic sizing (`s=sizes`) and dynamic coloring (`c=colors`, `cmap=...`), but carries higher rendering overhead for large datasets.

**4. How to label axes**
```python
fig, ax = plt.subplots()
ax.plot(x, y, label='Revenue')
ax.set_xlabel('Month', fontsize=12)
ax.set_ylabel('USD ($)', fontsize=12)
ax.set_title('Monthly Revenue Growth', fontsize=14, fontweight='bold')
ax.legend(loc='upper left')
```

**5. Subplots usage**
`plt.subplots(nrows, ncols)` instantiates a grid of subplot `Axes` within a single `Figure`:
```python
fig, axes = plt.subplots(2, 2, figsize=(10, 8))
axes[0, 0].plot(x, y)          # Top-left plot
axes[0, 1].scatter(x, z)       # Top-right plot
fig.tight_layout()             # Adjusts spacing to prevent label overlap
```

**6. Histogram usage**
`ax.hist(data, bins=20, density=True)` segments continuous numeric data into discrete intervals (bins) and plots bar heights representing frequency counts or probability densities. Helps identify skewness, modality, and distribution symmetry.

**7. Bar chart vs pie chart**
- **Bar Chart:** Uses horizontal/vertical bar lengths to encode values. Highly accurate human perceptual decoding; handles negative values and large numbers of categories easily.
- **Pie Chart:** Uses angles and arc areas. Difficult for human eyes to compare non-adjacent slices accurately. Never use for more than 5 categories or when precise comparison matters.

**8. Customizing colors**
- Named colors (`'red'`, `'navy'`), Hex codes (`'#1f77b4'`), or RGB tuples (`(0.1, 0.2, 0.5)`).
- **Colormaps (`cmap`):** Used for continuous scalar mapping:
  - Sequential (e.g., `'viridis'`, `'Blues'`): For ordered numeric ranges.
  - Diverging (e.g., `'coolwarm'`, `'RdBu'`): For data centered around a critical midpoint (0).
  - Categorical (e.g., `'tab10'`): For distinct qualitative categories.

**9. Adding legends**
`ax.legend()` places a legend mapping visual styles (colors, line patterns) to labels:
- Requires setting `label='Name'` inside individual plotting calls.
- Parameters: `loc='best'`, `bbox_to_anchor=(1, 1)` (placing legend outside plot), `ncol=2`.

**10. Saving plots**
`fig.savefig('output_plot.png', dpi=300, bbox_inches='tight')` exports figures to high-resolution formats (PNG, PDF, SVG).
- **Critical Ordering:** Always call `savefig()` **before** `plt.show()`, because `plt.show()` resets the current figure state in memory.

## 7. Seaborn

**1. Difference from Matplotlib**
Seaborn is a high-level abstraction built on top of Matplotlib that integrates seamlessly with Pandas DataFrames.
- **Higher Abstraction:** Creates complex statistical plots (e.g., categorical hue splits, regression lines, confidence intervals) in single function calls.
- **Aesthetics:** Provides modern default visual styles, grid themes, and color palettes.

**2. What is heatmap?**
`sns.heatmap(df.corr(), annot=True, cmap='coolwarm', fmt='.2f')` renders 2D matrices as color-encoded grids.
- **Primary Use Case:** Visualizing feature correlation matrices, confusion matrices, or cross-tabulated pivoting tables to spot patterns or collinearity.

**3. Pairplot usage**
`sns.pairplot(df, hue='species')` generates a matrix of pairwise scatter plots for all numeric features in a DataFrame, placing univariate histograms or KDE plots along the diagonal. Excellent for quick exploratory data analysis (EDA).

**4. Distplot / histplot**
- `sns.distplot()`: Legacy function combining a histogram with a Kernel Density Estimate (KDE) curve (deprecated since Seaborn 0.11.0).
- `sns.histplot()` / `sns.displot()`: Modern replacements providing explicit binning control, cumulative frequency plots, and multi-element categorical overlays (`hue=...`).

**5. Boxplot usage**
`sns.boxplot(x='category', y='value', data=df)` visualizes the 5-number summary of continuous variables across categories:
- Box displays Interquartile Range (IQR = Q3 - Q1).
- Internal bar marks the Median (Q2).
- Whiskers extend to $1.5 \times \text{IQR}$.
- Individual points beyond whiskers are highlighted as outliers.

**6. Countplot**
`sns.countplot(x='category', data=df, palette='Set2')` displays the frequency count of categorical values. Equivalent to creating a bar chart of `df['category'].value_counts()`.

**7. Correlation matrix**
Calculated using `df.corr(method='pearson')` to compute pairwise linear correlation coefficients between columns (ranging from -1 to +1). Displayed using `sns.heatmap()` to identify redundant features or strong predictor relationships.

**8. KDE plot**
Kernel Density Estimate (`sns.kdeplot(data)`): A non-parametric method for estimating and smoothing the continuous Probability Density Function (PDF) of a random variable.

**9. Styling plots**
- Styles: `sns.set_style('whitegrid')` (`'darkgrid'`, `'white'`, `'ticks'`).
- Context: `sns.set_context('paper')` (`'notebook'`, `'talk'`, `'poster'`) scales font sizes and line weights for different presentation targets.
- Palettes: `sns.set_palette('deep')` or `palette='husl'`.

**10. When to use Seaborn**
Use Seaborn for statistical data exploration, multi-variable relationships, categorical aggregations, and quick publication-ready charts directly from Pandas DataFrames. Switch to raw Matplotlib when building non-standard, highly customized, or low-level multi-axes figure layouts.


## 8. Statistical Analysis

**1. What is statistics?**
Statistics is the mathematical discipline of collecting, organizing, analyzing, interpreting, and presenting quantitative data. It provides the mathematical framework for modeling uncertainty, estimating parameters, testing hypotheses, and making data-driven decisions.

**2. Types of statistics**
- **Descriptive Statistics:** Summarizes, organizes, and presents characteristics of a known sample dataset (e.g., mean, median, standard deviation, histograms) without generalizing beyond the observed sample.
- **Inferential Statistics:** Uses sample data to draw probabilistic inferences, estimate parameters, and test hypotheses about an underlying population (e.g., confidence intervals, t-tests, ANOVA, regression).

**3. What is distribution?**
A statistical distribution is a mathematical function or tabular listing that expresses all possible values of a random variable along with their associated probabilities or frequencies of occurrence. Characterized by center, spread, skewness, and kurtosis.

**4. What is normal distribution?**
A symmetric, bell-shaped continuous probability distribution defined by two parameters: mean ($\mu$) and standard deviation ($\sigma$).
- **Empirical Rule (68-95-99.7 Rule):**
  - ~68% of data falls within $\mu \pm 1\sigma$
  - ~95% of data falls within $\mu \pm 2\sigma$
  - ~99.7% of data falls within $\mu \pm 3\sigma$
- **Significance:** Forms the statistical foundation for linear models, Z-tests, and the Central Limit Theorem.

**5. What is skewness?**
A measure of the asymmetry of a probability distribution around its mean:
- **Positive (Right) Skew:** Mean > Median > Mode. Tail extends toward higher positive values (e.g., income distribution).
- **Negative (Left) Skew:** Mean < Median < Mode. Tail extends toward lower negative values (e.g., age of death in developed countries).
- **Zero Skew:** Symmetric distribution (e.g., Normal distribution).

**6. What is kurtosis?**
A measure of the "tailedness" and extreme value probability of a probability distribution relative to a normal distribution:
- **Mesokurtic (Excess Kurtosis = 0):** Normal distribution.
- **Leptokurtic (Excess Kurtosis > 0):** Heavy tails, high peak, higher risk of extreme outliers (e.g., financial market returns).
- **Platykurtic (Excess Kurtosis < 0):** Light tails, flat peak, fewer extreme outliers (e.g., uniform distribution).

**7. What is variance?**
The average of the squared deviations of data points from their sample mean ($\bar{x}$):
$$
s^2 = \frac{\sum_{i=1}^n (x_i - \bar{x})^2}{n - 1}
$$
Quantifies total dispersion or variability. Measured in squared units of the original variable.

**8. What is standard deviation?**
The square root of variance ($s = \sqrt{s^2}$). Measures the average distance of data points from the mean in the **same units** as the original variable, making it directly interpretable.

**9. What is correlation?**
A standardized statistical measure quantifying the strength and direction of a linear relationship between two continuous variables.
- **Pearson Correlation Coefficient ($r$):** Ranges from -1 to +1:
  - $+1$: Perfect positive linear relationship.
  - $0$: No linear relationship.
  - $-1$: Perfect negative linear relationship.
- **Note:** Correlation measures linear association only, not non-linear relationships or causation.

**10. What is covariance?**
A non-standardized measure of the joint variability of two random variables:
$$
\text{Cov}(X, Y) = \frac{\sum_{i=1}^n (x_i - \bar{x})(y_i - \bar{y})}{n - 1}
$$
- **Interpretation:** Positive covariance indicates variables tend to move in the same direction; negative covariance indicates opposite directions. Unlike correlation, its magnitude depends on feature scale, making direct comparison across variable pairs impossible.

## 9. Descriptive Statistics

**1. Mean, Median, Mode**
- **Mean ($\bar{x}$):** Arithmetic average. Sensitive to outliers.
- **Median:** The middle value when data is sorted. Highly robust to extreme outliers and skewed distributions.
- **Mode:** The most frequently occurring value. Useful for categorical data distributions.

**2. Range**
The absolute difference between the maximum and minimum values in a dataset ($\text{Range} = X_{\text{max}} - X_{\text{min}}$). Extremely sensitive to outliers.

**3. Variance**
The sample variance ($s^2$) measures dispersion around the mean in squared units, serving as an intermediate calculation for standard deviation and statistical modeling.

**4. Standard deviation**
The sample standard deviation ($s$) measures data spread in original units. For normal distributions, it defines the intervals for confidence limits and outlier identification.

**5. Percentiles**
A value below which a given percentage of data observations fall. E.g., the 85th percentile is the score below which 85% of all observations lie.

**6. Quartiles**
Three cut-off points dividing a sorted dataset into four equal parts:
- **$Q_1$ (25th percentile):** Lower quartile.
- **$Q_2$ (50th percentile):** Median.
- **$Q_3$ (75th percentile):** Upper quartile.

**7. IQR**
Interquartile Range ($\text{IQR} = Q_3 - Q_1$). Measures the dispersion of the middle 50% of the data. Immune to extreme tail outliers.

**8. Outliers detection**
- **Tukey’s IQR Rule:** Any point $< Q_1 - 1.5 \times \text{IQR}$ or $> Q_3 + 1.5 \times \text{IQR}$.
- **Z-Score Rule:** Any point with $|Z| > 3$ ($Z = \frac{x - \mu}{\sigma}$), applicable to normally distributed data.
- **Visual Inspection:** Box plots, scatter plots, and isolation forests for multidimensional data.

**9. Summary statistics**
A set of quantitative measures providing a concise snapshot of a dataset's center, spread, and shape: Count, Mean, Standard Deviation, Min, $Q_1$, Median, $Q_3$, Max. Computed in Python using `df.describe()`.

**10. Visualization of distribution**
- **Histogram:** Displays frequency density across continuous bins.
- **Box Plot:** Visualizes medians, IQR, and outliers.
- **KDE Plot:** Smooth probability density curve estimation.
- **Violin Plot:** Combines a box plot with a rotated KDE curve on both sides.

## 10. Inferential Statistics

**1. What is hypothesis testing?**
A formal statistical procedure that uses sample evidence to evaluate the validity of a hypothesis about a population parameter under uncertainty.

**2. Null vs alternative hypothesis**
- **Null Hypothesis ($H_0$):** The baseline default statement assuming no effect, no difference, or no relationship exists ($\mu_1 = \mu_2$).
- **Alternative Hypothesis ($H_1$ or $H_a$):** The claim being tested, asserting that a statistically meaningful effect or difference exists ($\mu_1 \neq \mu_2$).

**3. p-value meaning**
The probability of obtaining a test statistic at least as extreme as the one observed in the sample, assuming the null hypothesis ($H_0$) is strictly true.
- If $p \le \alpha$ (significance level, e.g., 0.05), reject $H_0$.
- **Misconception:** The p-value is **not** the probability that the null hypothesis is true.

**4. Confidence interval**
An estimated range of values derived from sample data that is likely to contain the true population parameter with a specified confidence level (e.g., 95%).
- **Interpretation:** If sampling is repeated 100 times, approximately 95 of the constructed confidence intervals will contain the true population parameter.

**5. Type I and Type II errors**
| Decision | $H_0$ is True | $H_0$ is False |
| :--- | :--- | :--- |
| **Reject $H_0$** | **Type I Error ($\alpha$)** (False Positive) | Correct Decision ($1 - \beta$) (Power) |
| **Fail to Reject $H_0$** | Correct Decision ($1 - \alpha$) | **Type II Error ($\beta$)** (False Negative) |

**6. t-test vs z-test**
- **Z-Test:** Used when comparing sample means when the **population standard deviation ($\sigma$) is known** and/or sample size is large ($n \ge 30$). Assumes Normal distribution.
- **T-Test:** Used when the **population standard deviation ($\sigma$) is unknown** and estimated from the sample ($s$), or sample size is small ($n < 30$). Uses Student's t-distribution with heavier tails.

**7. ANOVA**
Analysis of Variance (ANOVA) tests whether statistically significant differences exist among the means of **three or more groups**.
- Computes the $F$-statistic ($\text{Variance between groups} / \text{Variance within groups}$).
- Prevents inflation of Type I error rate that occurs when conducting multiple pairwise t-tests.

**8. Sampling methods**
- **Simple Random Sampling:** Every member of the population has an equal probability of selection.
- **Stratified Sampling:** Population is divided into homogeneous subgroups (strata), and random samples are drawn proportionally from each.
- **Systematic Sampling:** Selecting every $k$-th individual from an ordered sampling frame.
- **Cluster Sampling:** Dividing population into heterogeneous groups (clusters) and randomly selecting entire clusters.

**9. Central Limit Theorem**
The Central Limit Theorem (CLT) states that as sample size ($n$) increases (typically $n \ge 30$), the sampling distribution of the sample mean approaches a Normal distribution, regardless of the shape of the underlying population distribution.
- **Standard Error of Mean:** $\text{SE} = \frac{\sigma}{\sqrt{n}}$.

**10. Statistical significance**
A conclusion that observed sample results are very unlikely to have occurred due to random sampling variation alone ($p < \alpha$). Statistical significance does **not** imply practical/business significance or large effect size.

## 11. Probability Theory

**1. What is probability?**
A numerical measure ranging from 0 (impossible event) to 1 (certain event) representing the likelihood that a specific random event will occur.

**2. Conditional probability**
The probability of event $A$ occurring given that event $B$ has already occurred:
$$
P(A|B) = \frac{P(A \cap B)}{P(B)}, \quad \text{where } P(B) > 0
$$

**3. Bayes theorem**
A mathematical formula used to update the conditional probability of a hypothesis ($A$) as new empirical evidence ($B$) becomes available:
$$
P(A|B) = \frac{P(B|A) \cdot P(A)}{P(B)}
$$
- **Components:** $P(A)$ is the Prior, $P(B|A)$ is the Likelihood, and $P(A|B)$ is the Posterior probability.

**4. Independent events**
Two events $A$ and $B$ are independent if the occurrence of $B$ does not alter the probability of $A$:
$$
P(A \cap B) = P(A) \cdot P(B) \quad \text{and} \quad P(A|B) = P(A)
$$

**5. Random variable**
A variable whose numerical values are outcomes of a random phenomenon.
- **Discrete:** Takes countable values (e.g., coin flips, customer counts).
- **Continuous:** Takes uncountably infinite values along a continuous interval (e.g., height, temperature, transaction amounts).

**6. Probability distribution**
- **Discrete:** Probability Mass Function (PMF), $P(X = x)$, assigning probabilities to discrete outcomes.
- **Continuous:** Probability Density Function (PDF), $f(x)$, where area under the curve represents probability ($\int_a^b f(x) \, dx = P(a \le X \le b)$).

**7. Expected value**
The theoretical long-run weighted average value of a random variable ($E[X]$):
- Discrete: $E[X] = \sum_{i} x_i P(x_i)$
- Continuous: $E[X] = \int_{-\infty}^{\infty} x f(x) \, dx$

**8. Law of large numbers**
As the number of independent trials ($n$) increases, the sample average ($\bar{X}_n$) is guaranteed to converge toward the true expected population value ($E[X]$).

**9. Bernoulli distribution**
A discrete probability distribution for a single trial with exactly two possible outcomes: Success ($X=1$) with probability $p$, and Failure ($X=0$) with probability $q = 1-p$. Mean $= p$, Variance $= p(1-p)$.

**10. Normal distribution**
A continuous bell-shaped distribution symmetric about mean $\mu$, governed by the probability density function:
$$
f(x) = \frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2}
$$
- Forms the basis of Gaussian noise assumptions, standardized Z-scores ($Z = \frac{X - \mu}{\sigma}$), and parametric statistics.


## 12. Machine Learning

**1. What is ML?**
Machine Learning is a subfield of Artificial Intelligence focused on developing algorithms that learn statistical patterns from data to perform inference, classification, regression, or decision-making on unseen data without explicit rule-based programming.

**2. Types of ML**
- **Supervised Learning:** Learns a mapping function $f(X) \rightarrow Y$ from labeled input-output pairs. Examples: Classification, Regression.
- **Unsupervised Learning:** Discovers hidden structures, patterns, or clusters in unlabeled feature data $X$. Examples: Clustering (K-Means), Dimensionality Reduction (PCA).
- **Reinforcement Learning:** An agent learns optimal policy actions ($a$) within a dynamic environment ($s$) through trial-and-error to maximize cumulative scalar rewards ($r$). Examples: Q-learning, PPO, AlphaGo.
- **Semi-Supervised Learning:** Combines a small amount of labeled data with a large volume of unlabeled data to improve learning efficiency.

**3. Overfitting vs underfitting**
- **Overfitting:** Model fits training data too closely, learning noise and random variance. Characterized by low training error but high test/validation error. Mitigated via regularization ($L_1/L_2$), cross-validation, pruning, dropout, or acquiring more data.
- **Underfitting:** Model is too simple to capture underlying data structure. Characterized by high training error and high test error. Mitigated by increasing model complexity, adding polynomial features, or reducing regularization.

**4. Bias vs variance**
- **Bias:** Error introduced by approximating a complex real-world problem with an overly simple model (causes underfitting).
- **Variance:** Sensitivity of the model to small fluctuations or noise in the training set (causes overfitting).
- **Bias-Variance Tradeoff:** Total Expected Error $= \text{Bias}^2 + \text{Variance} + \text{Irreducible Error} (\sigma^2)$. Increasing model capacity decreases bias but increases variance.

**5. Feature scaling**
Standardizes feature ranges so distance-based metrics (KNN, K-Means, SVM) or gradient descent algorithms converge efficiently without large-scale features dominating cost functions:
- **Standardization (Z-score):** $X_{\text{std}} = \frac{X - \mu}{\sigma}$ (Centers mean at 0, unit std dev = 1). Robust to outliers.
- **Min-Max Normalization:** $X_{\text{norm}} = \frac{X - X_{\text{min}}}{X_{\text{max}} - X_{\text{min}}}$ (Rescales values strictly to $[0, 1]$). Sensitive to outliers.

**6. Train-test split**
Partitioning a dataset into a **Training Set** (used to optimize model parameters, e.g., 70-80%) and a **Test Set** (held out strictly to evaluate generalization performance on unseen data, e.g., 20-30%). For imbalanced datasets, use **Stratified Split** to preserve target class proportions.

**7. Cross-validation**
Resampling procedure used to assess model generalization without relying on a single fixed train-test split.
- **K-Fold Cross-Validation:** Dataset is split into $K$ equal folds. Model is trained on $K-1$ folds and validated on the remaining fold, repeated $K$ times. Final metric is averaged over all $K$ iterations.
- **Stratified K-Fold:** Ensures each fold maintains identical class proportions as the full dataset.

**8. Model evaluation metrics**
- **Classification:**
  - Accuracy: $\frac{TP + TN}{TP + TN + FP + FN}$ (Misleading for imbalanced data).
  - Precision: $\frac{TP}{TP + FP}$ (Focuses on minimizing False Positives).
  - Recall (Sensitivity): $\frac{TP}{TP + FN}$ (Focuses on minimizing False Negatives).
  - F1-Score: $2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$ (Harmonic mean).
  - ROC-AUC: Measures area under Receiver Operating Characteristic curve ($TPR$ vs $FPR$).
- **Regression:** MAE, MSE, RMSE ($\sqrt{\text{MSE}}$), $R^2$ score.

**9. Hyperparameters**
Parameters whose values are explicitly configured *before* model training begins to control algorithm behavior (e.g., learning rate $\alpha$, number of trees `n_estimators`, tree depth `max_depth`, regularization penalty $\lambda$).
- **Optimization Techniques:** Grid Search (exhaustive search), Random Search (sampling random combinations), Bayesian Optimization (Gaussian processes modeling parameter spaces).

**10. Feature engineering**
The domain-driven process of transforming raw data into informative features to enhance machine learning model performance:
- One-Hot Encoding / Target Encoding for categorical variables.
- Interaction terms ($X_1 \times X_2$), polynomial expansion.
- Date/Time extraction (DayOfWeek, Hour, Quarter).
- Log transforms ($y \rightarrow \log(1+y)$) to handle skewed target distributions.

## 13. Supervised Learning

**1. What is supervised learning?**
Supervised learning algorithms build a mathematical model from a dataset containing both feature inputs ($X$) and ground-truth target labels ($y$) to infer a mapping function capable of predicting labels for new unseen inputs.

**2. Classification vs regression**
- **Classification:** Predicts discrete categorical class labels (e.g., Binary: Spam/Not Spam; Multiclass: Iris species).
- **Regression:** Predicts continuous numeric scalar values (e.g., house price forecasting, stock index prediction).

**3. Examples of algorithms**
- Linear Models: Linear Regression, Logistic Regression, Ridge/Lasso.
- Tree-Based Ensembles: Decision Trees, Random Forests, XGBoost, LightGBM, CatBoost.
- Distance & Kernel Methods: K-Nearest Neighbors (KNN), Support Vector Machines (SVM).
- Probabilistic & Neural: Naive Bayes, Multilayer Perceptrons (MLP / Deep Neural Networks).

**4. Training process**
1. Initialize model weight parameters $W$.
2. Pass training batch inputs $X$ to compute predictions $\hat{y} = f(X; W)$.
3. Evaluate discrepancy between $\hat{y}$ and true labels $y$ via a **Loss Function** $L(y, \hat{y})$ (e.g., Cross-Entropy, MSE).
4. Compute loss gradients $\frac{\partial L}{\partial W}$ and update weights via **Gradient Descent**: $W \leftarrow W - \alpha \frac{\partial L}{\partial W}$.
5. Repeat across epochs until convergence.

**5. Labelled data**
A collection of data samples where each observation contains both feature vector measurements ($X_1, X_2, \dots, X_p$) and a human-annotated or system-recorded target ground truth label ($y$).

**6. Evaluation metrics**
- **Classification:** Confusion Matrix, Precision, Recall, F1-Score, ROC-AUC, Log-Loss.
- **Regression:**
  - Mean Absolute Error (MAE): $\frac{1}{n} \sum |y_i - \hat{y}_i|$
  - Mean Squared Error (MSE): $\frac{1}{n} \sum (y_i - \hat{y}_i)^2$
  - Root Mean Squared Error (RMSE): $\sqrt{\text{MSE}}$
  - $R^2$ Score: $1 - \frac{\sum_{i=1}^n (y_i - \hat{y}_i)^2}{\sum_{i=1}^n (y_i - \bar{y})^2}$

**7. Overfitting**
When a supervised model learns specific noise, outliers, or spurious patterns in training data, leading to stellar training performance but poor generalization on test data.
- **Remedies:** $L_1$ (Lasso) / $L_2$ (Ridge) regularization penalty terms, reducing model capacity, applying early stopping during training, or increasing training sample size.

**8. Feature importance**
Quantifies the relative contribution of each feature to model prediction accuracy.
- **Tree Impurity Decrease (Gini Importance):** Total reduction in node impurity brought by splits on a feature.
- **Permutation Importance:** Measures the drop in validation score when feature values are randomly shuffled.
- **SHAP (SHapley Additive exPlanations):** Game-theoretic feature attributions explaining individual predictions.

**9. Real-world examples**
Credit scoring risk modeling, fraud detection, medical diagnostic screening, automated document classification, price forecasting, customer churn prediction.

**10. Limitations**
- High dependence on large quantities of accurate, high-quality labeled data.
- Susceptible to domain shift (poor performance when production distribution deviates from training data distribution).
- Risk of perpetuating historical bias present in training target labels.

## 14. Unsupervised Learning

**1. What is unsupervised learning?**
A category of machine learning algorithms that analyze and find underlying structure, hidden groupings, or low-dimensional representations in datasets containing only input features ($X$) without target labels.

**2. Clustering vs association**
- **Clustering:** Groups data points into clusters based on geometric feature distance/similarity (e.g., customer segmentation via K-Means).
- **Association Rule Learning:** Discovers co-occurrence patterns and conditional rules among item sets in transaction databases (e.g., Market Basket Analysis via Apriori algorithm: $\{ \text{Diapers} \} \Rightarrow \{ \text{Beer} \}$).

**3. KMeans algorithm**
Partitions $n$ observations into $K$ distinct clusters:
1. Randomly initialize $K$ cluster centroids.
2. **Assignment Step:** Assign each data point to its closest centroid using Euclidean distance.
3. **Update Step:** Recalculate centroids as the mean of all data points assigned to each cluster.
4. Iterate steps 2 and 3 until centroid positions converge (WCSS — Within-Cluster Sum of Squares is minimized).

**4. Hierarchical clustering**
Generates a tree-like hierarchy of nested clusters visualized as a **Dendrogram**.
- **Agglomerative (Bottom-up):** Starts with every data point as an individual cluster, iteratively merging the closest cluster pairs based on linkage criteria (Ward, Complete, Average) until one root cluster remains.
- **Divisive (Top-down):** Starts with all data points in one single cluster and recursively splits into smaller clusters.

**5. PCA**
Principal Component Analysis (PCA) is an orthogonal linear transformation technique used for dimensionality reduction.
- Identifies directions (Principal Components) along which data variance is maximized.
- Computes eigenvectors and eigenvalues of the feature covariance matrix.
- Projects high-dimensional data onto top $k$ principal components, eliminating feature correlation while preserving maximum variance.

**6. Dimensionality reduction**
The process of reducing input feature count from $D$ to $k$ ($k \ll D$) while maintaining essential signal.
- **Benefits:** Mitigates the **Curse of Dimensionality**, eliminates multicollinearity, reduces memory/computation overhead, enables 2D/3D visualization.
- **Techniques:** Linear (PCA, LDA), Non-Linear (t-SNE, UMAP).

**7. Applications**
Customer persona segmentation, anomaly & credit card fraud detection, recommendation engine candidate generation, topic modeling (LDA), image data compression.

**8. Challenges**
- Absence of objective ground-truth labels makes quantitative evaluation difficult (relies on heuristic validation like Silhouette Score or Inertia).
- High sensitivity to scale and outlier data points.
- Interpretation of derived components/clusters can be subjective.

**9. Distance metrics**
- **Euclidean Distance ($L_2$ norm):** $d(u, v) = \sqrt{\sum_{i=1}^n (u_i - v_i)^2}$. Sensitive to scale and high dimensions.
- **Manhattan Distance ($L_1$ norm):** $d(u, v) = \sum_{i=1}^n |u_i - v_i|$. Robust for grid-based dimensions.
- **Cosine Distance:** $1 - \frac{u \cdot v}{\|u\| \|v\|}$. Measures directional angle rather than magnitude; standard in NLP text vector similarity.

**10. Real-world use cases**
Segmenting user bases by behavioral patterns, anomaly detection in network security logs, grouping genomic sequences by expression similarity, compressing high-dimensional image vectors.

## 15. Linear Regression

**1. What is linear regression?**
A parametric supervised learning algorithm that models a continuous target variable $y$ as a linear combination of feature variables $X$:
$$
\hat{y} = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \dots + \beta_p X_p + \epsilon
$$
Evaluated by minimizing Ordinary Least Squares (OLS) residual sum of squares: $\text{RSS} = \sum_{i=1}^n (y_i - \hat{y}_i)^2$.

**2. Assumptions**
1. **Linearity:** The relationship between features and target is linear.
2. **Independence:** Errors/residuals are independently distributed (no auto-correlation).
3. **Homoscedasticity:** Constant residual variance across predictor values.
4. **Normality:** Residual errors follow a Normal distribution $N(0, \sigma^2)$.
5. **No Severe Multicollinearity:** Independent variables are not highly correlated with one another.

**3. R² score**
Coefficient of Determination ($R^2$):
$$
R^2 = 1 - \frac{\text{SS}_{\text{res}}}{\text{SS}_{\text{tot}}} = 1 - \frac{\sum_{i=1}^n (y_i - \hat{y}_i)^2}{\sum_{i=1}^n (y_i - \bar{y})^2}
$$
Proportion of target variable variance explained by predictors ($0 \le R^2 \le 1$). Adjusted $R^2$ penalizes adding non-informative features: $R^2_{\text{adj}} = 1 - \left[\frac{(1-R^2)(n-1)}{n-p-1}\right]$.

**4. MSE vs MAE**
- **Mean Squared Error (MSE):** $\frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2$. Squares residuals, heavily penalizing large error outliers. Differentiable (smooth gradient).
- **Mean Absolute Error (MAE):** $\frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i|$. Linear error penalties, making it robust to extreme outliers. Non-differentiable at 0.

**5. Multicollinearity**
When two or more predictor features are strongly correlated, making it mathematically unstable to invert $(X^T X)^{-1}$ in OLS.
- **Consequences:** Inflates coefficient variance, making feature importance unstable and uninterpretable.
- **Detection:** Variance Inflation Factor ($\text{VIF}_j = \frac{1}{1 - R_j^2}$; a $\text{VIF} > 5$ or $10$ indicates severe multicollinearity).
- **Remedies:** Drop collinear features, apply PCA, or use Ridge/Lasso regularization.

**6. Gradient descent**
First-order iterative optimization algorithm used to estimate parameters $\theta$ by minimizing cost function $J(\theta)$:
$$
\theta^{(t+1)} = \theta^{(t)} - \alpha \nabla_\theta J(\theta)
$$
Where $\alpha$ is the learning rate. Variants: Batch GD, Stochastic GD (SGD), Mini-Batch GD.

**7. Simple vs multiple regression**
- **Simple Linear Regression:** Fits a line using a single predictor feature ($y = \beta_0 + \beta_1 X_1$).
- **Multiple Linear Regression:** Fits a hyperplane using two or more predictor features ($y = \beta_0 + \beta_1 X_1 + \dots + \beta_p X_p$).

**8. Outliers effect**
Because OLS minimizes *squared* residuals, extreme leverage points disproportionately pull the regression slope toward themselves, skewing parameters and degrading model generalization.

**9. Interpretation of coefficients**
Coefficient $\beta_j$ represents the expected change in target variable $y$ for a 1-unit increase in predictor $X_j$, holding all other predictors constant.

**10. When to use**
Ideal when target data is continuous, relationships are approximately linear, high model interpretability is required, and baseline benchmarks are needed prior to fitting non-linear models.

## 16. Decision Tree

**1. What is decision tree?**
A non-parametric supervised model that partitions feature space into axis-aligned rectangular regions using a flowchart-like tree structure of decision nodes, branches, and leaf nodes.

**2. Gini vs entropy**
Metrics used to evaluate split quality by measuring node impurity:
- **Gini Impurity:** $G = 1 - \sum_{i=1}^C p_i^2$. Measures frequency of misclassifying a randomly chosen element. Default in Scikit-learn; computationally faster (no logarithms).
- **Entropy:** $H = -\sum_{i=1}^C p_i \log_2(p_i)$. Measures information disorder. Used to compute **Information Gain** ($\text{IG} = H_{\text{parent}} - H_{\text{children}}$).

**3. Overfitting**
Unconstrained decision trees grow until every leaf node is completely pure ($100\%$ training accuracy), memorizing noise and outliers.
- **Remedies:** Pruning, limiting `max_depth`, setting `min_samples_split` / `min_samples_leaf`, or using ensemble models (Random Forest).

**4. Pruning**
Technique to trim unnecessary branches to prevent overfitting:
- **Pre-Pruning:** Stops tree growth early by setting hyperparameters (`max_depth`, `max_leaf_nodes`).
- **Post-Pruning (Cost-Complexity Pruning):** Grows a full tree and trims branches that contribute minimally to performance based on cost-complexity parameter $\alpha$ (`ccp_alpha`).

**5. Tree depth**
The maximum path length from the root node to any leaf. Deep trees capture complex feature interactions but overfit; shallow trees underfit. Key hyperparameter for tuning model variance.

**6. Feature selection**
Decision trees perform implicit feature selection during training by selecting the feature that yields the maximum impurity reduction (highest Information Gain or Gini reduction) at each node split.

**7. Advantages**
- Highly interpretable and visually intuitive.
- Non-parametric: Requires zero feature scaling or normalization.
- Handles non-linear feature relationships and interactions natively.
- Handles both numerical and categorical inputs.

**8. Disadvantages**
- High variance (small changes in training data produce radically different tree structures).
- Step-wise axis-aligned split boundaries struggle with smooth diagonal decision surfaces.
- Greedy split optimization can get trapped in local optima.

**9. Use cases**
Medical diagnostics flowcharts, credit risk approval decisioning, churn prediction, and serving as weak base learners in Random Forests and Gradient Boosted Decision Trees (XGBoost).

**10. Comparison with other models**
More interpretable than Neural Networks or Ensemble methods, but individual decision trees generally exhibit lower predictive accuracy and higher variance than Random Forests or XGBoost.


## 17. NLP

**1. What is NLP?**
Natural Language Processing (NLP) is an interdisciplinary field combining computational linguistics, artificial intelligence, and machine learning to enable computers to process, understand, analyze, and generate human natural language in text or speech format.

**2. Tokenization**
The foundational preprocessing step of breaking raw unstructured text into smaller semantic units called **Tokens** (words, subwords, or characters).
- **Word Tokenization:** Splits by whitespace/punctuation (`"I love AI"` $\rightarrow$ `["I", "love", "AI"]`).
- **Subword Tokenization (BPE, WordPiece, SentencePiece):** Splits rare words into frequent subword units (`"unaffordable"` $\rightarrow$ `["un", "afford", "able"]`). Standard in modern Transformer models (BERT, GPT).

**3. Stopwords**
High-frequency functional words in a language (e.g., `"the"`, `"is"`, `"in"`, `"at"`) that carry minimal domain-specific semantic signal.
- **Filtering:** Often removed in traditional sparse models (TF-IDF, Bag-of-Words) to reduce vocabulary dimension and computational noise.
- **Modern Context:** Kept intact in Transformer-based architectures (BERT, GPT) because syntax, sentence structure, and attention context rely on them.

**4. Stemming vs Lemmatization**
- **Stemming:** Heuristic, rule-based algorithm (e.g., Porter/Lancaster Stemmer) that chops off prefixes and suffixes to reduce words to a base stem. Fast, but can produce non-dictionary pseudo-words (e.g., `"studies"` $\rightarrow$ `"studi"`, `"caring"` $\rightarrow$ `"car"`).
- **Lemmatization:** Uses morphological analysis and context-aware dictionary lookup (WordNet) to reduce words to their valid dictionary canonical form (Lemma) based on Part-of-Speech (POS): e.g., `"better"` $\rightarrow$ `"good"` (Adjective), `"running"` $\rightarrow$ `"run"` (Verb).

**5. Bag of Words**
A classic text vectorization model that represents a document as a fixed-length vector of token occurrence counts or frequencies, discarding sentence grammar, word order, and context.
- **Limitation:** Creates high-dimensional, highly sparse vectors ($V \times N$) susceptible to out-of-vocabulary terms and unable to capture semantic relationships (`"not good"` has identical component counts to `"good, not"`).

**6. TF-IDF**
Term Frequency–Inverse Document Frequency weights token importance by balancing raw frequency in a document against rarity across the corpus:
$$
\text{TF-IDF}(t, d, D) = \text{TF}(t, d) \times \text{IDF}(t, D)
$$
- $\text{TF}(t, d) = \frac{\text{Count of } t \text{ in } d}{\text{Total tokens in } d}$
- $\text{IDF}(t, D) = \log\left(\frac{N}{|\{d \in D : t \in d\}|}\right)$
- Downweights ubiquitous terms (like `"and"`, `"the"`) while boosting distinctive domain terms (like `"hyperparameter"`).

**7. Word embeddings**
Dense continuous vector representations ($d \in [50, 768]$) of words mapping semantic meaning into continuous geometric space.
- **Static Embeddings (Word2Vec, GloVe, FastText):** Learns fixed vectors using skip-gram or continuous bag-of-words (CBOW). Vectors satisfy semantic analogical vector math: $\vec{v}_{\text{King}} - \vec{v}_{\text{Man}} + \vec{v}_{\text{Woman}} \approx \vec{v}_{\text{Queen}}$.
- **Contextualized Embeddings (BERT, RoBERTa, GPT):** Generates dynamic vectors dependent on surrounding sentence context (e.g., `"bank"` of a river vs financial `"bank"` get distinct embeddings).

**8. Sentiment analysis**
An NLP classification task that categorizes the subjective sentiment expressed in text into polarity classes (Positive, Negative, Neutral) or fine-grained emotional dimensions (Joy, Anger, Sadness).
- **Techniques:** Lexicon-based (VADER, TextBlob), Fine-tuned Classifiers (Logistic Regression + TF-IDF), or Transformer Encoders (`distilbert-base-uncased-finetuned-sst-2-english`).

**9. Named Entity Recognition**
Named Entity Recognition (NER) is an Information Extraction task that locates and classifies named entities in unstructured text into predefined categories (e.g., Person, Organization, Location, Date/Time, Monetary Values).
- Evaluated using sequence tagging frameworks (BIO tagging scheme: **B**eginning, **I**nside, **O**utside of entity) with Conditional Random Fields (CRF) or Transformer models.

**10. Applications**
Virtual assistants & conversational LLM chatbots, neural machine translation (Google Translate), automated document summarization, search engine query ranking, spam filtering, legal/financial contract auditing, and voice speech recognition.

## 18. MySQL

**1. What is SQL?**
Structured Query Language (SQL) is the standard declarative programming language used to define, query, manipulate, and control relational database management systems (RDBMS) like MySQL, PostgreSQL, and Oracle.
- **Sublanguages:** DDL (Data Definition Language: `CREATE`, `ALTER`, `DROP`), DML (Data Manipulation Language: `SELECT`, `INSERT`, `UPDATE`, `DELETE`), DCL (Data Control Language: `GRANT`, `REVOKE`), TCL (Transaction Control Language: `COMMIT`, `ROLLBACK`).

**2. Types of joins**
Joins combine columns from one or more tables based on a related join key:
- **`INNER JOIN`:** Returns only matching rows present in both tables.
- **`LEFT JOIN` (Left Outer):** Returns all rows from the left table, plus matched columns from the right table (unmatched right values return `NULL`).
- **`RIGHT JOIN` (Right Outer):** Returns all rows from the right table, plus matched columns from the left table.
- **`FULL OUTER JOIN`:** Returns all rows when there is a match in either left or right table. *(Note: MySQL does not natively support `FULL OUTER JOIN`; simulate it using `LEFT JOIN ... UNION ... RIGHT JOIN`)*.
- **`CROSS JOIN`:** Computes Cartesian product of two tables ($N \times M$ rows).

**3. GROUP BY**
Groups rows sharing identical values in specified columns so aggregate functions (`SUM`, `AVG`, `COUNT`, `MAX`, `MIN`) can be computed per group:
```sql
SELECT department_id, COUNT(*) AS emp_count, AVG(salary) AS avg_sal
FROM employees
GROUP BY department_id;
```

**4. HAVING vs WHERE**
- **`WHERE`:** Filters individual rows **before** any grouping or aggregation occurs. Cannot contain aggregate functions (`WHERE salary > 50000`).
- **`HAVING`:** Filters grouped summary rows **after** `GROUP BY` aggregations have been calculated (`HAVING COUNT(*) > 5`).

**5. Primary key**
A column (or composite set of columns) that uniquely identifies each row in a database table.
- **Constraints:** Must contain unique values and cannot contain `NULL` values. A table can have at most **one** Primary Key.
- **Storage:** Automatically creates a clustered B-tree index in MySQL InnoDB, determining physical row storage order on disk.

**6. Index**
A physical data structure (typically a B+ Tree in MySQL InnoDB) that speeds up `SELECT` query data retrieval by allowing logarithmic search time ($O(\log N)$) instead of performing expensive full table scans ($O(N)$).
- **Trade-off:** Speeds up reads (`SELECT`), but slightly slows down writes (`INSERT`, `UPDATE`, `DELETE`) because index trees must be rebalanced, and consumes additional disk storage.

**7. Normalization**
The database design process of organizing fields and tables to minimize data redundancy and prevent update/deletion anomalies.
- **1NF (First Normal Form):** Atomic values; no repeating groups.
- **2NF (Second Normal Form):** Meets 1NF; removes partial dependencies (non-key columns must depend on the *entire* primary key).
- **3NF (Third Normal Form):** Meets 2NF; removes transitive dependencies (non-key columns must depend *only* on the primary key).

**8. Subqueries**
A `SELECT` query nested inside a larger outer SQL statement (within `SELECT`, `FROM`, `WHERE`, or `HAVING` clauses).
- **Scalar Subquery:** Returns a single value.
- **Correlated Subquery:** References columns from the outer query, re-evaluating for every row processed by the outer query (`WHERE EXISTS (...)`).

**9. Aggregate functions**
Built-in SQL functions operating on sets of values across multiple rows to return a single summary scalar value:
- `COUNT(col)` / `COUNT(*)`: Row count.
- `SUM(col)`: Total sum.
- `AVG(col)`: Mean average.
- `MIN(col)` / `MAX(col)`: Minimum / Maximum values.
- Ignore `NULL` values automatically (except `COUNT(*)`).

**10. Window functions**
Performs calculations across a set of table rows (a "window") related to the current row, returning a value for **every single row** without collapsing rows like `GROUP BY`:
```sql
SELECT employee_id, department_id, salary,
       AVG(salary) OVER(PARTITION BY department_id) AS dept_avg_salary,
       DENSE_RANK() OVER(PARTITION BY department_id ORDER BY salary DESC) AS sal_rank
FROM employees;
```

## 19. Advanced Excel

**1. VLOOKUP vs XLOOKUP**
- **`VLOOKUP`:** Legacy formula `VLOOKUP(lookup_value, table_array, col_index_num, [range_lookup])`.
  - **Limitations:** Only searches left-to-right (lookup column must be leftmost column in range); breaks if table columns are inserted/deleted; defaults to approximate match (`TRUE`) unless `FALSE` is specified.
- **`XLOOKUP`:** Modern formula `XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])`.
  - **Advantages:** Searches in any direction (left, right, up, down); safe against column insertion/deletion; defaults to exact match; includes native error handling (`if_not_found`).

**2. INDEX MATCH**
A powerful lookup combination replacing `VLOOKUP`:
`=INDEX(return_range, MATCH(lookup_value, lookup_range, 0))`
- **`MATCH`:** Returns the relative row position of `lookup_value` within `lookup_range`.
- **`INDEX`:** Retrieves the value at that specified row position from `return_range`.
- **Advantages:** Works leftward, uses less computational memory than referencing entire multi-column `VLOOKUP` tables, and is resilient to inserted columns.

**3. Pivot tables**
An interactive data summarization engine built into Excel used to quickly aggregate, analyze, group, filter, and transform large datasets without writing formulas.
- **Core Quadrants:** Filters, Columns, Rows, Values.
- Supports calculated fields, custom group groupings (by date ranges/quarters), and interactive **Slicers**.

**4. Conditional formatting**
Dynamic formatting tool that automatically alters cell styling (fill color, font weight, data bars, icon sets, heatmaps) based on logical rules or user-defined formulas (`=A1>AVERAGE($A$1:$A$100)`). Used for visual outlier detection and KPI status indicators.

**5. IF / nested IF**
- **`IF`:** Logical test evaluation: `=IF(logical_test, value_if_true, value_if_false)`.
- **Nested `IF`:** Combining multiple `IF` statements to evaluate sequential conditions: `=IF(A1>=90, "A", IF(A1>=80, "B", IF(A1>=70, "C", "F")))`.
- **Modern Alternative:** `=IFS(condition1, val1, condition2, val2, ...)` or `=SWITCH()`.

**6. Data validation**
Data governance feature restricting user input into specific cells based on rules:
- **Options:** Dropdown lists (`List`), numeric boundaries (`Whole Number`, `Decimal`), Date ranges, Text length limits, or custom formulas.
- Prevents data entry typos and enforces standardized database inputs.

**7. Power Query**
Excel’s embedded Extract, Transform, Load (ETL) data engine powered by the M formula language.
- Enables importing data from disparate sources (SQL, CSV, Web APIs, SharePoint), unpivoting columns, cleaning missing records, merging queries, and establishing reproducible automated data pipelines.

**8. Macros**
Automated sequences of Excel commands and actions recorded or written using Visual Basic for Applications (VBA). Triggered via buttons, hotkeys, or workbook events to automate repetitive reporting workflows.

**9. Dashboard creation**
The design process of compiling high-level visual analytics onto a single interactive Excel sheet:
- Combines Pivot Tables, Pivot Charts, interactive Slicers, dynamic shape cards, and conditional formatting KPI meters into an executive reporting layout.

**10. What-if analysis**
Decision support tools for exploring how varying input variables impacts formula outcomes:
- **Goal Seek:** Back-calculates the exact input value needed to achieve a target output result.
- **Data Tables:** Evaluates how changing 1 or 2 variables affects output across a grid matrix.
- **Scenario Manager:** Stores and compares multiple sets of input assumptions (e.g., Best Case, Base Case, Worst Case).


---

*Good luck with the interview. If you'd like this as a Word doc or PDF for printing, or want to go deeper on any specific topic, just say the word.*
