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
Mutable objects can be changed after creation — `list`, `dict`, `set` — and keep the same identity when modified. Immutable objects (`int`, `float`, `str`, `tuple`, `frozenset`) can't be changed in place; any "change" creates a new object.

**2. Difference between list and tuple?**
Lists (`[]`) are mutable and slightly slower/heavier; tuples (`()`) are immutable, faster, and hashable (usable as dict keys). Use tuples for fixed data, lists when the collection needs to grow, shrink, or change.

**3. What is list comprehension?**
A concise one-line syntax for building a list from an iterable:
```python
squares = [x**2 for x in range(10) if x % 2 == 0]
```
It's generally faster and more readable than an equivalent loop with `.append()`.

**4. What are *args and **kwargs?**
`*args` collects extra positional arguments into a tuple; `**kwargs` collects extra keyword arguments into a dict:
```python
def f(*args, **kwargs):
    print(args)    # tuple
    print(kwargs)  # dict
```

**5. Difference between shallow copy and deep copy?**
A shallow copy (`copy.copy()`) creates a new outer object, but nested objects still point to the originals. A deep copy (`copy.deepcopy()`) recursively copies everything, so nested objects become fully independent.

**6. What is lambda function?**
An anonymous, single-expression function: `square = lambda x: x**2`. Commonly used for short throwaway functions passed to `map()`, `filter()`, or `sorted(key=...)`.

**7. What is generator and yield?**
A generator is a function that produces values lazily with `yield` instead of `return`, pausing its state between calls rather than computing everything upfront:
```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1
```
This makes it memory-efficient for large or infinite sequences.

**8. What is GIL (Global Interpreter Lock)?**
A mutex in CPython that lets only one thread execute Python bytecode at a time, even on multi-core machines. It simplifies memory management but means CPU-bound multithreading doesn't get true parallelism (use `multiprocessing` instead); I/O-bound tasks are largely unaffected.

**9. Difference between == and is?**
`==` checks value equality (same data); `is` checks identity (same object in memory). Two separate-but-equal lists give `==` → True but `is` → False.

**10. What are decorators?**
A function that wraps another function to extend or modify its behavior without changing its code, applied with `@` syntax:
```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@logger
def greet():
    print("Hello")
```

## 2. OOP in Python

**1. What is class and object?**
A class is a blueprint defining attributes and methods; an object is a specific instance created from it with its own data:
```python
class Car:
    def __init__(self, brand):
        self.brand = brand

my_car = Car("Toyota")
```

**2. Explain encapsulation with example**
Encapsulation bundles data and methods together and restricts direct access to internal state, typically via `_protected` or `__private` naming. E.g., a `BankAccount` class keeps `__balance` private and only lets it change through validated `deposit()`/`withdraw()` methods.

**3. What is inheritance? Types?**
Inheritance lets a child class acquire attributes/methods from a parent class, promoting reuse. Types: single, multiple, multilevel, hierarchical, and hybrid inheritance.

**4. What is polymorphism?**
The ability of different classes to be used through the same interface, where the same method name behaves differently depending on the calling object — e.g., `.speak()` producing different output for `Dog` vs `Cat`.

**5. What is abstraction?**
Hiding internal implementation details and exposing only essential features, often via abstract base classes (the `abc` module) that define method signatures without implementation — focusing on "what" an object does, not "how".

**6. Method overloading vs overriding**
Overloading (same name, different parameters) isn't natively supported in Python — it's simulated with default arguments or `*args`. Overriding is when a subclass redefines a parent method with the same signature, replacing its behavior.

**7. What is constructor (__init__)?**
A special method automatically called when an object is created, used to initialize instance attributes: `def __init__(self, name): self.name = name`.

**8. What is super() function?**
Gives access to a parent class's methods from within a child class — commonly used to call the parent's `__init__` so the child extends rather than fully replaces initialization:
```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed
```

**9. What are dunder methods?**
"Double underscore" special methods like `__init__`, `__str__`, `__len__`, `__add__` that let custom objects integrate with Python's built-in syntax and operators (printing, length, `+`, comparisons, etc.).

**10. What is multiple inheritance?**
When a class inherits from more than one parent, e.g., `class C(A, B):`. Python resolves method lookup order using the MRO (Method Resolution Order / C3 linearization) to decide which parent's method takes precedence.

## 3. Python Libraries

**1. Difference between NumPy and Pandas**
NumPy provides fast n-dimensional arrays and numerical operations (linear algebra, broadcasting); Pandas builds on NumPy to provide labeled, tabular structures (Series, DataFrame) suited to structured, heterogeneous data. NumPy for pure numerical computation, Pandas for data wrangling.

**2. What is Matplotlib used for?**
A foundational plotting library for static, animated, or interactive visualizations — line plots, bar charts, histograms, scatter plots — with fine-grained control over every plot element.

**3. What is Seaborn?**
A statistical visualization library built on Matplotlib that gives higher-level, better-styled plots (heatmaps, pairplots, violin plots) with less code, integrating naturally with Pandas DataFrames.

**4. What is Scikit-learn?**
A machine learning library with a consistent API for classification, regression, clustering, dimensionality reduction, model selection, and preprocessing, built on NumPy/SciPy — the standard toolkit for classical ML in Python.

**5. What is TensorFlow / PyTorch?**
Both are deep learning frameworks for building and training neural networks with GPU acceleration and automatic differentiation. TensorFlow (Google) emphasizes production deployment; PyTorch (Meta) is known for its dynamic computation graph and Pythonic, research-friendly style.

**6. What is OpenCV?**
An open-source computer vision library for image/video processing — filtering, object detection, face recognition, feature extraction — with Python and C++ bindings.

**7. What is requests library?**
A library for making HTTP requests (GET, POST, etc.) to interact with web APIs in a simple way: `requests.get(url)`.

**8. What is BeautifulSoup?**
A library for parsing HTML/XML and navigating its tree structure, commonly used for web scraping to extract data from web pages.

**9. What is pickle used for?**
Serializing (pickling) and deserializing (unpickling) Python objects into a byte stream — useful for saving trained models or complex objects to disk and reloading them later.

**10. What is joblib?**
A library optimized for efficiently serializing large NumPy arrays (faster than pickle for that data) and for simple parallel computing; commonly used to save/load scikit-learn models.

## 4. NumPy

**1. What is ndarray?**
NumPy's core data structure — an n-dimensional, homogeneously-typed array supporting fast vectorized operations, unlike Python's built-in list.

**2. Why NumPy is faster than Python lists?**
NumPy arrays store elements in contiguous memory of a fixed type and use vectorized, compiled C-level operations, avoiding Python's per-element loop overhead and type-checking.

**3. What is vectorization?**
Performing operations on entire arrays at once (`a + b`) instead of looping element by element, letting NumPy push the loop into optimized C code for much faster execution.

**4. Difference between reshape and resize**
`reshape()` returns the data in a new shape without changing its size (dimensions must be compatible) and typically returns a view. `resize()` can change the total number of elements — as an `ndarray` method it modifies in place, padding with zeros or truncating as needed.

**5. What is broadcasting?**
A mechanism letting NumPy perform arithmetic on arrays of different but compatible shapes by implicitly "stretching" the smaller one without copying data:
```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6]])
b = np.array([10, 20, 30])
a + b   # b is broadcast across each row
```

**6. What is slicing in NumPy?**
Extracting a sub-array with `start:stop:step` notation along one or more axes, e.g., `arr[1:4, :2]`. Unlike list slices, NumPy slices are views by default, so modifying a slice can modify the original array.

**7. Difference between np.array and list**
`np.array` is fixed-type, contiguous in memory, and supports vectorized math; a Python `list` can hold mixed types and is more flexible for insert/append, but is slower for numerical computation.

**8. What is axis in NumPy?**
The dimension along which an operation applies — `axis=0` operates down columns (across rows), `axis=1` operates across rows (along columns) for a 2D array, determining the direction of reduction functions like `sum()`.

**9. How to handle missing values?**
NumPy represents missing numeric data as `np.nan`, detected with `np.isnan()` and handled via masking, filtering, or NaN-aware functions like `np.nanmean()` and `np.nansum()`.

**10. Random module usage**
`numpy.random` generates random numbers/arrays for simulations and ML — `np.random.rand()`, `np.random.randint()`, `np.random.normal()` — with reproducibility via `np.random.seed()` or the newer `default_rng()` API.

## 5. Pandas

**1. What is DataFrame?**
A 2-dimensional, labeled, tabular data structure with rows and columns of potentially different types — conceptually similar to a spreadsheet or SQL table, built on top of NumPy.

**2. loc vs iloc**
`.loc` selects by label/index name (inclusive of the end label in slices); `.iloc` selects by integer position (exclusive of the end, like standard Python slicing).

**3. isnull vs notnull**
`isnull()` returns a boolean mask marking `True` where values are missing; `notnull()` is the inverse, marking `True` where values are present.

**4. merge vs join**
`merge()` combines DataFrames on common columns (like SQL joins) with more flexibility over join keys and type; `join()` is a convenience method primarily for combining on the index.

**5. groupby usage**
Splits a DataFrame into groups by one or more keys, applies an aggregation to each, and combines the results:
```python
df.groupby('category')['sales'].sum()
```

**6. apply vs map vs applymap**
`map()` works element-wise on a Series only; `apply()` works on a Series or DataFrame (row/column-wise) with more complex functions; `applymap()` (now `DataFrame.map()` in recent pandas) applies a function element-wise across an entire DataFrame.

**7. Handling missing values**
Drop with `dropna()`, fill with `fillna()` (mean/median/mode/forward-fill/backward-fill), or estimate with `interpolate()` — the choice depends on how much data is missing and why.

**8. Sorting data**
`sort_values(by='col')` sorts rows by column value(s); `sort_index()` sorts by the row/column index; both accept `ascending=False`.

**9. Filtering rows**
Boolean indexing is standard: `df[df['age'] > 30]`, combined with `&`/`|` for multiple conditions, or `.query()` for SQL-like syntax.

**10. Reading different file formats**
`read_csv()`, `read_excel()`, `read_json()`, `read_sql()`, `read_parquet()` each return a DataFrame, with matching `to_*()` methods for writing back out.

## 6. Matplotlib

**1. What is Matplotlib?**
A low-level, highly customizable plotting library for static, animated, and interactive visualizations, forming the foundation many other libraries (like Seaborn) build on.

**2. Types of plots**
Line plots, bar charts, histograms, scatter plots, pie charts, box plots, and 3D plots — chosen based on whether you're showing trends, comparisons, distributions, or relationships.

**3. Difference between plot and scatter**
`plt.plot()` draws connected lines and suits continuous/ordered data like time series; `plt.scatter()` draws unconnected markers with per-point customization (size, color), better for showing relationships between two variables.

**4. How to label axes**
`plt.xlabel('...')`, `plt.ylabel('...')`, `plt.title('...')`, and `plt.legend()` for series labels.

**5. Subplots usage**
`plt.subplots(nrows, ncols)` creates a figure with a grid of axes objects, each holding a separate plot — useful for comparing several charts side by side.

**6. Histogram usage**
`plt.hist(data, bins=n)` bins continuous data into intervals and plots the frequency in each bin, visualizing the distribution/shape of a numeric variable.

**7. Bar chart vs pie chart**
Bar charts compare quantities across discrete categories and scale well to many categories; pie charts show proportions of a whole and work best with only a few categories, since angles are harder to compare precisely than bar lengths.

**8. Customizing colors**
Set via the `color` parameter (name, hex, or RGB tuple) in most plotting functions; colormaps (`cmap`) provide continuous color scales for heatmaps/scatter plots.

**9. Adding legends**
`plt.legend()` displays a legend using each plot call's `label` parameter, customizable with `loc`, `title`, and `fontsize`.

**10. Saving plots**
`plt.savefig('filename.png', dpi=300)` saves the current figure — call it before `plt.show()`, since `show()` can clear the figure afterward.

## 7. Seaborn

**1. Difference from Matplotlib**
Seaborn is built on Matplotlib but gives higher-level functions with better default styling, built-in statistical computations (e.g., automatic confidence intervals), and native support for Pandas DataFrames — less code for common statistical plots.

**2. What is heatmap?**
`sns.heatmap()` visualizes matrix-like data (e.g., a correlation matrix) as a grid of colored cells, with color intensity representing value magnitude — useful for spotting patterns across many variables at once.

**3. Pairplot usage**
`sns.pairplot()` plots pairwise relationships across all numeric columns as a grid of scatter plots, with histograms/KDEs on the diagonal — a quick way to explore relationships and distributions together.

**4. Distplot / histplot**
Both visualize the distribution of a single numeric variable, combining a histogram with an optional KDE curve. `distplot` is deprecated in favor of `histplot` and `displot` in modern Seaborn.

**5. Boxplot usage**
`sns.boxplot()` shows the median, quartiles, and potential outliers of a numeric variable (optionally split by category) via the "box and whiskers" summary.

**6. Countplot**
`sns.countplot()` shows the count of observations in each category — essentially a bar chart of frequencies computed automatically from raw data.

**7. Correlation matrix**
A table of pairwise correlation coefficients between numeric variables, typically computed with `df.corr()` and visualized with `sns.heatmap()` to quickly spot strongly related features.

**8. KDE plot**
Kernel Density Estimate plot (`sns.kdeplot()`) — a smoothed, continuous estimate of a variable's probability distribution, like a smoothed histogram.

**9. Styling plots**
`sns.set_style()` (e.g., "darkgrid", "whitegrid"), `sns.set_palette()` for color schemes, and `sns.set_context()` to scale elements for presentations vs. papers.

**10. When to use Seaborn**
When you want quick, attractive statistical visualizations directly from a Pandas DataFrame with minimal code — especially for exploratory analysis of distributions, categorical comparisons, or correlations. For fully custom, low-level control, use Matplotlib directly.

## 8. Statistical Analysis

**1. What is statistics?**
The discipline of collecting, organizing, analyzing, interpreting, and presenting data to uncover patterns and support decisions under uncertainty.

**2. Types of statistics**
Descriptive statistics summarizes features of a dataset (mean, charts, etc.); inferential statistics uses a sample to draw conclusions about a larger population.

**3. What is distribution?**
A function or listing showing all possible values of a variable and how frequently each occurs, describing the "shape" of the data (normal, uniform, skewed, etc.).

**4. What is normal distribution?**
A symmetric, bell-shaped distribution defined by its mean and standard deviation, where values cluster around the mean and probability tapers off equally in both directions — foundational to many statistical methods.

**5. What is skewness?**
A measure of asymmetry around the mean — positive skew means a longer right tail (high-value outliers), negative skew means a longer left tail.

**6. What is kurtosis?**
A measure of how heavy-tailed or peaked a distribution is relative to normal — high kurtosis means fatter tails/more extreme outliers, low kurtosis means fewer.

**7. What is variance?**
The average of the squared deviations from the mean, quantifying how spread out the data is.

**8. What is standard deviation?**
The square root of variance, expressed in the same units as the original data — more directly interpretable than variance.

**9. What is correlation?**
A standardized measure (−1 to 1) of the strength and direction of the linear relationship between two variables, unaffected by their scale.

**10. What is covariance?**
A measure of how two variables move together — positive means same direction, negative means opposite — but unlike correlation it isn't standardized, so its magnitude is hard to interpret directly.

## 9. Descriptive Statistics

**1. Mean, Median, Mode**
Mean is the arithmetic average; median is the middle value when sorted (robust to outliers); mode is the most frequent value (useful for categorical data).

**2. Range**
The difference between the maximum and minimum values — simple but sensitive to outliers.

**3. Variance**
The average squared deviation from the mean, in squared units of the original data.

**4. Standard deviation**
The square root of variance — the most common spread measure since it's in the data's original units.

**5. Percentiles**
The value below which a given percentage of the data falls — e.g., the 90th percentile is the value below which 90% of observations lie.

**6. Quartiles**
Percentiles dividing data into four equal parts: Q1 (25th), Q2 (50th/median), Q3 (75th).

**7. IQR**
Interquartile Range = Q3 − Q1, representing the spread of the middle 50% of the data; commonly used to detect outliers.

**8. Outliers detection**
Common methods: the IQR rule (beyond Q1 − 1.5×IQR or Q3 + 1.5×IQR), Z-scores (beyond ±3 standard deviations), and visual tools like box plots.

**9. Summary statistics**
Count, mean, std, min, max, and quartiles — concisely describing a dataset's center and spread; `df.describe()` computes these in one call.

**10. Visualization of distribution**
Histograms, box plots, KDE plots, and violin plots each show shape, spread, and outliers of a numeric variable from a different angle.

## 10. Inferential Statistics

**1. What is hypothesis testing?**
A method for using sample data to test a claim about a population, deciding whether observed results are likely due to chance or reflect a real effect.

**2. Null vs alternative hypothesis**
The null hypothesis (H₀) assumes no effect/difference exists; the alternative (H₁) is what you're testing for — that an effect or difference exists.

**3. p-value meaning**
The probability of observing results at least as extreme as what was measured, assuming the null hypothesis is true; a small p-value (typically < 0.05) suggests the result is unlikely to be due to chance alone.

**4. Confidence interval**
A range, computed from sample data, likely to contain the true population parameter at a stated confidence level (e.g., a 95% CI: if you repeated the sampling many times, ~95% of such intervals would contain the true value).

**5. Type I and Type II errors**
Type I is rejecting a true null hypothesis (false positive); Type II is failing to reject a false null hypothesis (false negative).

**6. t-test vs z-test**
Both compare means, but a t-test is used when the population standard deviation is unknown and/or the sample is small (t-distribution); a z-test is used when the population standard deviation is known and the sample is large.

**7. ANOVA**
Analysis of Variance — tests whether the means of three or more groups differ significantly, using variance ratios (F-statistic) instead of many pairwise t-tests.

**8. Sampling methods**
Simple random sampling, stratified sampling (proportional within subgroups), systematic sampling (every nth item), and cluster sampling (sampling entire groups).

**9. Central Limit Theorem**
The sampling distribution of the sample mean approaches a normal distribution as sample size increases, regardless of the population's original distribution — underpinning much of inferential statistics.

**10. Statistical significance**
A result unlikely to have occurred by random chance alone (typically p < 0.05); significance doesn't automatically mean practical/real-world importance.

## 11. Probability Theory

**1. What is probability?**
A numerical measure (0 to 1) of how likely an event is, where 0 is impossible and 1 is certain.

**2. Conditional probability**
The probability of event A given that event B has already occurred: P(A|B) = P(A∩B) / P(B).

**3. Bayes theorem**
A formula for updating a hypothesis's probability given new evidence: P(A|B) = [P(B|A) × P(A)] / P(B) — widely used in classification (e.g., Naive Bayes) and belief updating.

**4. Independent events**
Two events are independent if one's occurrence doesn't affect the other's probability: P(A∩B) = P(A) × P(B).

**5. Random variable**
A variable whose possible values are outcomes of a random phenomenon — discrete (countable, e.g., dice rolls) or continuous (any value in a range, e.g., height).

**6. Probability distribution**
A function describing the likelihood of each possible outcome of a random variable — a probability mass function for discrete variables or a density function for continuous ones.

**7. Expected value**
The long-run average value of a random variable over many repetitions — the sum (or integral) of each outcome weighted by its probability.

**8. Law of large numbers**
As the number of trials increases, the sample average converges to the true population/expected value.

**9. Bernoulli distribution**
A discrete distribution for a single trial with two outcomes (success/failure) at probability p — the building block for the binomial distribution over multiple trials.

**10. Normal distribution**
A continuous, symmetric, bell-shaped distribution defined by mean and standard deviation; per the Central Limit Theorem it naturally arises as the distribution of sums/averages of many independent random variables.

## 12. Machine Learning

**1. What is ML?**
A field of AI where systems learn patterns from data to make predictions or decisions, without being explicitly programmed with fixed rules for every case.

**2. Types of ML**
Supervised learning (learns from labeled data), unsupervised learning (finds patterns in unlabeled data), and reinforcement learning (learns via trial-and-error using rewards/penalties from an environment).

**3. Overfitting vs underfitting**
Overfitting: the model learns training data too well, including noise, and performs poorly on new data. Underfitting: the model is too simple to capture the pattern, performing poorly even on training data.

**4. Bias vs variance**
Bias is error from overly simplistic assumptions (underfitting); variance is error from excessive sensitivity to training data fluctuations (overfitting) — good models balance the two.

**5. Feature scaling**
Transforming features to a common scale (standardization or min-max normalization) so features with larger numeric ranges don't dominate distance- or gradient-based algorithms.

**6. Train-test split**
Dividing data into a training set (to fit the model) and a test set (held out to evaluate on unseen data), typically 80/20 or 70/30.

**7. Cross-validation**
Repeatedly splitting data into train/validation folds (e.g., k-fold CV) to get a more robust performance estimate than a single train-test split.

**8. Model evaluation metrics**
Classification: accuracy, precision, recall, F1-score, ROC-AUC. Regression: MSE, RMSE, MAE, R².

**9. Hyperparameters**
Settings chosen before training (learning rate, number of trees, k in KNN) rather than learned from data — typically tuned via grid search, random search, or Bayesian optimization.

**10. Feature engineering**
Creating, transforming, or selecting input variables to improve model performance — e.g., interaction terms, encoding categoricals, extracting date parts, or binning continuous values.

## 13. Supervised Learning

**1. What is supervised learning?**
A type of ML where a model learns from labeled data (input-output pairs) to predict outputs for new, unseen inputs.

**2. Classification vs regression**
Classification predicts discrete categories (spam or not spam); regression predicts continuous numeric values (house prices).

**3. Examples of algorithms**
Linear/logistic regression, decision trees, random forests, support vector machines, k-nearest neighbors, naive Bayes, and gradient boosting (XGBoost, LightGBM).

**4. Training process**
The model is fed labeled data, makes predictions, compares them to actual labels via a loss function, and iteratively adjusts its parameters (e.g., via gradient descent) to minimize that loss.

**5. Labelled data**
Data where each input example is paired with the correct output/target, which the model uses to learn the input-to-output mapping.

**6. Evaluation metrics**
Classification: accuracy, precision, recall, F1, confusion matrix, ROC-AUC. Regression: MAE, MSE, RMSE, R².

**7. Overfitting**
When a model memorizes training data patterns/noise too closely and fails to generalize — addressed with regularization, more data, or simpler models.

**8. Feature importance**
A measure of how much each input feature contributes to a model's predictions — derived from tree-based models (Gini importance) or techniques like permutation importance/SHAP values.

**9. Real-world examples**
Email spam detection, credit risk scoring, house price prediction, medical diagnosis, and demand forecasting.

**10. Limitations**
Needs large amounts of accurately labeled data (often expensive to obtain), can struggle to generalize beyond the training distribution, and can inherit biases present in the labels.

## 14. Unsupervised Learning

**1. What is unsupervised learning?**
A type of ML that finds patterns or structure in data without labeled outputs — the model explores the data on its own.

**2. Clustering vs association**
Clustering groups similar data points together (customer segments); association rule learning finds co-occurrence relationships between variables ("customers who buy X also buy Y").

**3. KMeans algorithm**
Partitions data into k clusters by iteratively assigning points to the nearest cluster centroid, then recalculating centroids as the mean of assigned points, until assignments stabilize.

**4. Hierarchical clustering**
Builds a tree (dendrogram) of nested clusters, either merging smaller clusters into bigger ones (agglomerative) or splitting large ones (divisive), without needing to pre-specify the number of clusters.

**5. PCA**
Principal Component Analysis — a dimensionality reduction technique transforming correlated features into a smaller set of uncorrelated "principal components" that capture the most variance in the data.

**6. Dimensionality reduction**
Reducing the number of input features while preserving as much meaningful information as possible — fights the curse of dimensionality, speeds up training, and aids visualization (PCA, t-SNE).

**7. Applications**
Customer segmentation, anomaly/fraud detection, recommendation systems, topic modeling in text, and image compression.

**8. Challenges**
No ground truth labels to objectively measure "correctness," results sensitive to algorithm choice/parameters (e.g., choosing k), and interpreting discovered structure can be subjective.

**9. Distance metrics**
Euclidean distance, Manhattan distance, and cosine similarity quantify how "close" two data points are, forming the basis for clustering and nearest-neighbor methods.

**10. Real-world use cases**
Grouping customers by purchasing behavior, detecting fraudulent transactions as anomalies, compressing images, and organizing large text corpora into topics.

## 15. Linear Regression

**1. What is linear regression?**
A supervised algorithm modeling the relationship between a dependent variable and one or more independent variables as a straight line (or hyperplane), minimizing the sum of squared errors.

**2. Assumptions**
Linearity between predictors and target, independence of errors, homoscedasticity (constant error variance), normally distributed residuals, and no severe multicollinearity.

**3. R² score**
The coefficient of determination — the proportion of variance in the target explained by the model, from 0 (no explanatory power) to 1 (perfect fit).

**4. MSE vs MAE**
MSE squares errors before averaging, penalizing larger errors more and being sensitive to outliers; MAE averages absolute errors, treating error sizes proportionally and being more robust to outliers.

**5. Multicollinearity**
When two or more predictors are highly correlated with each other, making it hard to isolate each variable's individual effect and destabilizing coefficient estimates — detected via correlation matrices or VIF.

**6. Gradient descent**
An iterative optimization algorithm that adjusts parameters step by step in the direction that reduces the loss function fastest, commonly used to find best-fit coefficients.

**7. Simple vs multiple regression**
Simple regression has one independent variable predicting the target; multiple regression uses two or more.

**8. Outliers effect**
Since linear regression minimizes squared error, outliers can disproportionately pull the fitted line toward them, distorting coefficient estimates.

**9. Interpretation of coefficients**
Each coefficient is the expected change in the target for a one-unit increase in that predictor, holding all other predictors constant.

**10. When to use**
When the relationship between variables is approximately linear, the target is continuous, and you want an interpretable model — often a good baseline before more complex algorithms.

## 16. Decision Tree

**1. What is decision tree?**
A tree-structured model that splits data into branches based on feature values, making decisions through if-else conditions, ending in leaf nodes holding the predicted class or value.

**2. Gini vs entropy**
Both measure impurity to decide the best split at each node — Gini measures the probability of misclassifying a randomly chosen element; entropy measures information disorder (used for information gain). They often give similar splits, but Gini is computationally slightly faster.

**3. Overfitting**
Trees are prone to overfitting because they can keep splitting until each leaf is pure, capturing noise — mitigated with pruning, depth limits, or ensemble methods.

**4. Pruning**
Removing branches with little predictive power — pre-pruning (depth/leaf-size limits during growth) or post-pruning (trimming after a fully grown tree) — to reduce overfitting.

**5. Tree depth**
The number of levels from root to deepest leaf; deeper trees capture more complex patterns but are more prone to overfitting, making depth a key hyperparameter.

**6. Feature selection**
Decision trees naturally select features by choosing the most informative one (highest information gain/lowest impurity) to split on at each node.

**7. Advantages**
Easy to interpret and visualize, needs little preprocessing (no scaling required), handles numerical and categorical data, and captures non-linear relationships.

**8. Disadvantages**
Prone to overfitting, can be unstable (small data changes can produce a very different tree), and can create biased splits with imbalanced data.

**9. Use cases**
Credit scoring, medical diagnosis support, customer churn prediction, and as base learners inside ensembles like Random Forest and Gradient Boosting.

**10. Comparison with other models**
More interpretable than ensemble methods or neural networks but typically less accurate/robust alone; ensembles trade some interpretability for significantly better predictive performance.

## 17. NLP

**1. What is NLP?**
Natural Language Processing — a field of AI focused on enabling computers to understand, interpret, and generate human language (text and speech).

**2. Tokenization**
Splitting text into smaller units (tokens) — words, subwords, or sentences — usually the first step in an NLP pipeline.

**3. Stopwords**
Common words ("the", "is", "and") that carry little unique meaning and are often removed during preprocessing to reduce noise.

**4. Stemming vs Lemmatization**
Stemming crudely chops words to a root form using rules ("running" → "run", sometimes producing non-words like "studies" → "studi"); lemmatization uses vocabulary/grammar rules to reduce words to their proper dictionary form ("better" → "good").

**5. Bag of Words**
A text representation counting word occurrences per document, ignoring grammar and order — a sparse vector of word frequencies.

**6. TF-IDF**
Term Frequency–Inverse Document Frequency — weights words by how often they appear in a document balanced against how rare they are across all documents, upweighting distinctive words and downweighting common ones.

**7. Word embeddings**
Dense vector representations of words (Word2Vec, GloVe) learned so semantically similar words sit close together in vector space, capturing meaning better than sparse representations like Bag of Words.

**8. Sentiment analysis**
An NLP task classifying text as positive, negative, or neutral (or finer-grained emotions) — commonly used for analyzing reviews or social media.

**9. Named Entity Recognition**
Identifying and classifying named entities in text into predefined categories like person, organization, location, or date.

**10. Applications**
Chatbots, machine translation, sentiment analysis, text summarization, search engines, and speech recognition.

## 18. MySQL

**1. What is SQL?**
Structured Query Language — a standard language for creating, querying, updating, and managing data in relational databases.

**2. Types of joins**
INNER JOIN (matching rows in both tables), LEFT JOIN (all left rows + matches from the right), RIGHT JOIN (all right rows + matches from the left), FULL OUTER JOIN (all rows from both) — MySQL doesn't natively support FULL OUTER JOIN, so it's usually simulated with a UNION of LEFT and RIGHT joins.

**3. GROUP BY**
Groups rows sharing the same values in specified columns so aggregate functions can be applied per group:
```sql
SELECT dept, COUNT(*) FROM employees GROUP BY dept;
```

**4. HAVING vs WHERE**
`WHERE` filters individual rows before grouping; `HAVING` filters groups after aggregation (e.g., `HAVING COUNT(*) > 5`), since aggregates can't be used in a WHERE clause.

**5. Primary key**
A column (or set of columns) that uniquely identifies each row in a table; it can't contain NULLs, and a table has only one primary key.

**6. Index**
A data structure (commonly a B-tree) that speeds up data retrieval by avoiding a full table scan, at the cost of extra storage and slightly slower writes.

**7. Normalization**
Organizing tables to reduce data redundancy and improve integrity, through a series of "normal forms" (1NF, 2NF, 3NF, etc.) that progressively remove duplicate/dependent data.

**8. Subqueries**
A query nested inside another, used to compute an intermediate result the outer query then uses: `SELECT * FROM employees WHERE salary > (SELECT AVG(salary) FROM employees)`.

**9. Aggregate functions**
`COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()` — compute a single summary value from a set of rows, typically with `GROUP BY`.

**10. Window functions**
Functions like `ROW_NUMBER()`, `RANK()`, `SUM() OVER(...)` that compute across a set of related rows (a "window") while still returning a value per row, unlike GROUP BY which collapses rows.

## 19. Advanced Excel

**1. VLOOKUP vs XLOOKUP**
`VLOOKUP` searches the leftmost column of a range and returns a value from a column to the right, but can only look rightward and breaks if columns are inserted. `XLOOKUP` is the modern replacement — searches in any direction, defaults to exact match, and handles errors more gracefully.

**2. INDEX MATCH**
`MATCH()` finds the position of a lookup value and `INDEX()` returns the value at that position — more flexible than VLOOKUP (can look leftward, doesn't break when columns are inserted).

**3. Pivot tables**
An interactive tool that summarizes, aggregates, and reorganizes large datasets by dragging fields into rows, columns, values, and filters, without writing formulas.

**4. Conditional formatting**
Automatically changes cell appearance (color, icons, data bars) based on rules or the cell's value, making patterns and outliers visually easy to spot.

**5. IF / nested IF**
`IF(condition, value_if_true, value_if_false)` returns one of two values; nesting multiple IFs handles more than two outcomes, though `IFS()` or lookup functions are usually cleaner for many conditions.

**6. Data validation**
Restricts what can be entered into a cell (dropdown lists, number ranges, date ranges), helping maintain data quality and consistency.

**7. Power Query**
Excel's data connection and transformation (ETL) tool, used to import, clean, reshape, and combine data from multiple sources before loading it into a worksheet or data model.

**8. Macros**
Recorded or VBA-scripted sequences of actions that automate repetitive tasks, triggered with a click or keyboard shortcut.

**9. Dashboard creation**
Combining pivot tables, charts, slicers, and conditional formatting on one sheet for an interactive, at-a-glance summary of key metrics, often linked to data via pivot tables or Power Query.

**10. What-if analysis**
Excel tools (Goal Seek, Data Tables, Scenario Manager) that let you explore how changing input values affects a formula's output — useful for forecasting and sensitivity analysis.

---

*Good luck with the interview. If you'd like this as a Word doc or PDF for printing, or want to go deeper on any specific topic, just say the word.*
