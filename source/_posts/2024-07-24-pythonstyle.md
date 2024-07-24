---

layout: post

title: "python编程规范"

date: 2024-07-24 10:12:10
updated: 2024-07-24 16:23:22

excerpt: "谷歌Google Python Style Guide翻译"

categories: 
	- 教程
    - 计算机

tag: 
	- 教程
    - 计算机

math: true

category_bar: true
typora-root-url: ..
---



# Google Python Style Guide

{% note warring%}

翻译自[Google Python Style Guide](https://google.github.io/styleguide/pyguide.html#381-docstrings)，使用GPT-4o翻译和人工校对。

{% endnote %}

## 1 Background 

Python是谷歌使用的主要动态语言。此风格指南是针对Python程序的一系列*应做和不应做*的事项。

为了帮助您正确格式化代码，我们创建了一个 [Vim 的设置文件](google_python_style.vim)。对于 Emacs，默认设置应该是可以的。

许多团队使用 [Black](https://github.com/psf/black) 或 [Pyink](https://github.com/google/pyink) 自动格式化工具，以避免在格式问题上争论。

## 2 Python Language Rules 

### 2.1 Lint 

对您的代码运行 `pylint`，使用这个 [pylintrc](https://google.github.io/styleguide/pylintrc)。

#### 2.1.1 Definition 

`pylint`是一个用于查找Python源代码中的错误和风格问题的工具。它能够发现通常由编译器在像C和C++这样较少动态的语言中捕获的问题。由于Python的动态特性，一些警告可能是不正确的；然而，虚假的警告应该相对不常见。

#### 2.1.2 Pros 

捕捉那些容易被忽视的错误，如拼写错误、使用变量前未赋值等。

#### 2.1.3 Cons 

`pylint` 并不完美。为了充分利用它，有时我们需要绕过它，抑制它的警告或修复它。

#### 2.1.4 Decision 

确保在您的代码上运行 `pylint`。


如果警告不合适，则应抑制警告，以免隐藏其他问题。要抑制警告，可以设置行级注释：

```python
def do_PUT(self):  # WSGI name, so pylint: disable=invalid-name
  ...
```

`pylint` 警告通过符号名称（`empty-docstring`）进行识别。特定于 Google 的警告以 `g-` 开头。

如果从符号名称中无法清楚地了解抑制的原因，请添加解释。

以这种方式进行抑制的优点在于，我们可以轻松地搜索抑制并重新审视它们。

您可以通过以下方式获取`pylint`警告的列表：

```shell
pylint --list-msgs
```

要获取有关特定消息的更多信息，请使用：

```shell
pylint --help-msg=invalid-name
```

更倾向于使用 `pylint: disable` 而不是已弃用的旧形式 `pylint: disable-msg`。

未使用的参数警告可以通过在函数开始时删除变量来抑制。始终包含一条注释，解释您为什么要删除它。“Unused.”是足够的。例如：

```python
def viking_cafe_order(spam: str, beans: str, eggs: str | None = None) -> str:
    del beans, eggs  # Unused by vikings.
    return spam + spam + spam
```

其他常见的抑制此警告的方式包括使用 '`_`' 作为未使用参数的标识符，或在参数名称前加上 '`unused_`'，或者将其赋值为 '`_`'。这些形式是被允许的，但不再被鼓励。这些做法会破坏通过名称传递参数的调用者，并且并不强制要求这些参数实际上未被使用。

### 2.2 Imports 

仅对包和模块使用`import`语句，而不是对单个类型、类或函数使用。

#### 2.2.1 Definition 

模块间共享代码的可重用机制。

#### 2.2.2 Pros 

命名空间管理约定是简单的。每个标识符的来源以一致的方式指示；`x.Obj` 表示对象 `Obj` 是在模块 `x` 中定义的。


#### 2.2.3 Cons 

模块名称仍然可能发生冲突。一些模块名称不便于过长。


#### 2.2.4 Decision 

-   使用 `import x` 来导入包和模块
-   使用 `from x import y`，其中 `x` 是包的前缀，而 `y` 是没有前缀的模块名称。
-   在以下任何情况下使用 `from x import y as z`：
    -   要导入两个名为`y`的模块
    -   `y` 与当前模块中定义的顶级名称冲突
    -   `y` 与公共 API 中的一个常见参数名称（例如，`features`）发生冲突。
    -   `y` 是一个不太方便的长名称
    -   在您的代码上下文中，`y` 过于通用（例如，`from storage.file_system import options as fs_options`）
-   仅在`z`是标准缩写时使用`import y as z`（例如，`import numpy as np`）

例如，模块`sound.effects.echo`可以如下导入：

```python
from sound.effects import echo
...
echo.EchoFilter(input, output, delay=0.7, atten=4)
```

在导入时不要使用相对名称。即使模块在同一个包中，也应使用完整的包名。这有助于防止无意中重复导入一个包。


##### 2.2.4.1 Exemptions 

此规则的例外情况：

-   以下模块中的符号用于支持静态分析和类型检查：
    -   [`typing` module](#typing-imports)
    -   [`collections.abc` module](#typing-imports)
    -   [`typing_extensions` module](https://github.com/python/typing_extensions/blob/main/README.md)
-   来自[six.moves module](https://six.readthedocs.io/#module-six.moves)的重定向。


### 2.3 Packages 

使用模块的完整路径名导入每个模块。

#### 2.3.1 Pros 

避免了模块名称冲突或由于模块搜索路径与作者预期不符而导致的不正确导入。使得查找模块变得更加容易。

#### 2.3.2 Cons 

这使得代码的部署变得更加困难，因为你必须复制包的层次结构。不过，这在现代部署机制中并不是一个真正的问题。

#### 2.3.3 Decision 

所有新代码应通过其完整的包名导入每个模块。

导入应如下所示：

```python
Yes:
  # 在代码中引用 absl.flags 时，请使用完整名称（详细）
  import absl.flags
  from doctor.who import jodie

  _FOO = absl.flags.DEFINE_string(...)
```

```python
Yes:
  # 代码中的引用标志仅使用模块名称（常见）
  from absl import flags
  from doctor.who import jodie

  _FOO = flags.DEFINE_string(...)
```

*（假设该文件位于 `doctor/who/` 目录下，`jodie.py` 也存在于该目录中）*

```python
No:
  # 不清楚作者想要哪个模块以及将会导入什么
  # 实际的导入行为取决于控制 sys.path 的外部因素
  # 作者打算导入哪个可能的 jodie 模块？
  import jodie
```

主二进制文件所在的目录不应被假定在 `sys.path` 中，尽管在某些环境中确实如此。在这种情况下，代码应假定 `import jodie` 指的是一个名为 `jodie` 的第三方或顶级包，而不是一个本地的 `jodie.py`。


### 2.4 Exceptions 

允许使用例外，但必须谨慎使用。

#### 2.4.1 Definition 

异常是一种打破正常控制流以处理错误或其他异常情况的手段。

#### 2.4.2 Pros 

正常操作代码的控制流并未被错误处理代码所混杂。它还允许在发生某些条件时跳过多个帧，例如，从$N$个嵌套函数中一步返回，而不必通过错误代码进行传递。

#### 2.4.3 Cons 

可能导致控制流变得混乱。在进行库调用时，容易遗漏错误情况。


#### 2.4.4 Decision 

例外必须遵循某些条件：

-   在合适的情况下，使用内置的异常类。例如，抛出一个 `ValueError` 来指示编程错误，比如违反了前置条件，这种情况可能在验证函数参数时发生

-   不要将 `assert` 语句用作条件语句或验证前置条件。它们不应对应用程序逻辑至关重要。一个检验标准是，如果移除 `assert` 语句，代码仍然能够正常运行。`assert` 条件语句是 [不保证](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement) 被评估的。对于基于 [pytest](https://pytest.org) 的测试，使用 `assert` 是可以的，并且被期望用于验证预期。例如：

    
    ```python
    Yes:
      def connect_to_next_port(self, minimum: int) -> int:
        """Connects to the next available port.
    
        Args:
          minimum: A port value greater or equal to 1024.
    
        Returns:
          The new minimum port.
    
        Raises:
          ConnectionError: If no available port is found.
        """
        if minimum < 1024:
          # 请注意，这个引发ValueError的情况在文档中并未提及
          # 由于在字符串的“Raises:”部分中，保证这种特定的行为反应对API误用并不合适
          raise ValueError(f'Min. port must be at least 1024, not {minimum}.')
        port = self._find_next_open_port(minimum)
        if port is None:
          raise ConnectionError(
              f'Could not connect to service on port {minimum} or higher.')
        # 该代码不依赖于此断言的结果
        assert port >= minimum, (
            f'Unexpected port {port} when minimum was {minimum}.')
        return port
    ```

    ```python
    No:
      def connect_to_next_port(self, minimum: int) -> int:
        """Connects to the next available port.
    
        Args:
          minimum: A port value greater or equal to 1024.
    
        Returns:
          The new minimum port.
        """
        assert minimum >= 1024, 'Minimum port must be at least 1024.'
        # The following code depends on the previous assert.
        port = self._find_next_open_port(minimum)
        assert port is not None
        # The type checking of the return statement relies on the assert.
        return port
    ```


-   库或包可以定义它们自己的异常。在这样做时，它们必须继承自现有的异常类。异常名称应以`Error`结尾，并且不应引入重复（例如`foo.FooError`）

-   绝不要使用笼统的 `except:` 语句，或者捕获 `Exception` 或 `StandardError`，除非你正在

    -   重新抛出异常，或者
    -   在程序中创建一个隔离点，在该点上异常不会被传播，而是被记录并抑制，例如通过保护线程的最外层代码块来防止其崩溃

    在这方面，Python非常宽容，`except:`确实会捕获所有内容，包括拼写错误的名称、sys.exit()调用、Ctrl+C中断、unittest失败以及所有其他你根本不想捕获的异常

-   最小化`try`/`except`块中的代码量。`try`块的主体越大，越有可能出现您未预料到的代码行引发异常。在这些情况下，`try`/`except`块掩盖了一个真实的错误

-   使用 `finally` 子句来执行代码，无论在 `try` 块中是否引发异常。这通常对于清理操作很有用，例如，关闭文件


### 2.5 Mutable Global State 

避免可变的全局状态。

#### 2.5.1 Definition 

模块级值或类属性在程序执行过程中可能会发生变更。

#### 2.5.2 Pros 

偶尔有用。

#### 2.5.3 Cons 

-   打破封装性：这样的设计可能使得实现有效目标变得困难。例如，如果全局状态用于管理数据库连接，那么同时连接到两个不同的数据库（例如在迁移过程中计算差异）就变得困难。类似的问题在全局注册表中也很容易出现。

-   在导入过程中有可能改变模块的行为，因为对全局变量的赋值是在模块首次导入时进行的。

#### 2.5.4 Decision 

避免可变的全局状态。

在那些使用全局状态被认为合理的罕见情况下，可变的全局实体应在模块级别或作为类属性声明，并通过在名称前加上`_`来使其内部化。如果有必要，对可变全局状态的外部访问必须通过公共函数或类方法进行。请参见下面的[Naming](#s3.16-naming)。请在注释中或通过注释链接的文档中解释使用可变全局状态的设计原因。

模块级常量是被允许并鼓励使用的。例如：`_MAX_HOLY_HANDGRENADE_COUNT = 3` 用于内部使用的常量，或 `SIR_LANCELOTS_FAVORITE_COLOR = "blue"` 用于公共 API 常量。常量的命名必须使用全大写字母并用下划线分隔。请参见下面的 [Naming](#s3.16-naming)。

### 2.6 Nested/Local/Inner Classes and Functions 

嵌套的局部函数或类在用于封闭局部变量时是可以的。内部类也是可以的。

#### 2.6.1 Definition 

一个类可以在方法、函数或类内部定义。一个函数可以在方法或函数内部定义。嵌套函数对在封闭作用域中定义的变量具有只读访问权限。

#### 2.6.2 Pros 

允许定义仅在非常有限的范围内使用的实用类和函数。非常[ADT](https://en.wikipedia.org/wiki/Abstract_data_type)风格。通常用于实现装饰器。

#### 2.6.3 Cons 

嵌套的函数和类无法直接进行测试。嵌套可能会使外部函数变得更长且可读性降低。

#### 2.6.4 Decision 

它们是可以的，但有一些注意事项。避免嵌套函数或类，除非是为了闭合一个局部值，而这个值不是`self`或`cls`。不要仅仅为了隐藏一个函数而嵌套它，以免让模块的用户无法访问。相反，在模块级别用\_作为前缀来命名它，以便测试仍然可以访问。

### 2.7 Comprehensions & Generator Expressions 

可以用于简单的情况。

#### 2.7.1 Definition 

列表、字典和集合推导式以及生成器表达式提供了一种简洁而高效的方式来创建容器类型和迭代器，而无需使用传统的循环、`map()`、`filter()`或`lambda`。

#### 2.7.2 Pros 

简单的推导式可以比其他字典、列表或集合的创建技术更清晰和简单。生成器表达式可以非常高效，因为它们完全避免了列表的创建。

#### 2.7.3 Cons 

复杂的理解或生成器表达式可能难以阅读。

#### 2.7.4 Decision 

允许的使用推导式，然而不允许使用多个 `for` 子句或过滤表达式。优化应以可读性为主，而非简洁性。

```python
Yes:
  result = [mapping_expr for value in iterable if filter_expr]

  result = [
      is_valid(metric={'key': value})
      for value in interesting_iterable
      if a_longer_filter_expression(value)
  ]

  descriptive_name = [
      transform({'key': key, 'value': value}, color='black')
      for key, value in generate_iterable(some_input)
      if complicated_condition_is_met(key, value)
  ]

  result = []
  for x in range(10):
    for y in range(5):
      if x * y > 10:
        result.append((x, y))

  return {
      x: complicated_transform(x)
      for x in long_generator_function(parameter)
      if x is not None
  }

  return (x**2 for x in range(10))

  unique_names = {user.name for user in users if user is not None}
```

```python
No:
  result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]

  return (
      (x, y, z)
      for x in range(5)
      for y in range(5)
      if x != y
      for z in range(5)
      if y != z
  )
```

### 2.8 Default Iterators and Operators 

对于支持默认迭代器和操作符的类型，如列表、字典和文件，使用默认的迭代器和操作符。

#### 2.8.1 Definition 

容器类型，如字典和列表，定义了默认的迭代器和成员测试操作符（“in”和“not in”）。

#### 2.8.2 Pros 

默认的迭代器和运算符简单且高效。它们直接表达操作，而无需额外的方法调用。使用默认运算符的函数是通用的。它可以与任何支持该操作的类型一起使用。

#### 2.8.3 Cons 

通过阅读方法名称，无法判断对象的类型（除非变量有类型注释）。这也是一个优势。

#### 2.8.4 Decision 

对于支持默认迭代器和操作符的类型，如列表、字典和文件，使用默认的迭代器和操作符。内置类型也定义了迭代器方法。优先使用这些方法，而不是返回列表的方法，除了在迭代时不应修改容器。

```python
Yes:  for key in adict: ...
      if obj in alist: ...
      for line in afile: ...
      for k, v in adict.items(): ...
```

```python
No:   for key in adict.keys(): ...
      for line in afile.readlines(): ...
```

### 2.9 Generators 

根据需要使用生成器。

#### 2.9.1 Definition 

生成器函数返回一个迭代器，每次执行 yield 语句时都会产生一个值。在产生一个值之后，生成器函数的运行状态会被挂起，直到下一个值被需要。

#### 2.9.2 Pros 

更简单的代码，因为每次调用时局部变量的状态和控制流都得以保留。生成器使用的内存比一次性创建整个值列表的函数要少。

#### 2.9.3 Cons 

生成器中的局部变量在生成器被完全消耗或自身被垃圾回收之前，不会被垃圾回收。

#### 2.9.4 Decision 

在生成器函数的文档字符串中使用“Yields:”而不是“Returns:”。

如果生成器管理一个昂贵的资源，请确保强制进行清理。

进行清理的一个好方法是通过使用上下文管理器来包装生成器 [PEP-0533](https://peps.python.org/pep-0533/)。

### 2.10 Lambda Functions 

适合单行本。优先选择生成器表达式，而不是带有 “lambda ”的 “map() ”或 “filter()”。


#### 2.10.1 Definition 

Lambda 表达式定义了在表达式中使用的匿名函数，而不是在语句中使用的函数。

#### 2.10.2 Pros 

方便

#### 2.10.3 Cons 

比本地函数更难阅读和调试。缺乏名称意味着堆栈跟踪更难以理解。表达能力受到限制，因为该函数可能仅包含一个表达式。

#### 2.10.4 Decision 

允许使用lambda。如果lambda函数内部的代码跨越多行或长度超过60-80个字符，最好将其定义为常规的[嵌套函数](#lexical-scoping)。

对于像乘法这样的常见操作，建议使用来自`operator`模块的函数，而不是使用lambda函数。例如，优先选择`operator.mul`而不是`lambda x, y: x * y`。

### 2.11 Conditional Expressions 

对于简单的情况还可以。

#### 2.11.1 Definition 

条件表达式（有时称为“三元运算符”）是一种提供更简洁语法的机制，用于 if 语句。例如：`x = 1 if cond else 2`。

#### 2.11.2 Pros 

比if语句更简洁和方便。


#### 2.11.3 Cons 

可能比if语句更难以阅读。如果表达式较长，条件可能难以定位。

#### 2.11.4 Decision 

可以用于简单情况。每个部分必须适合在一行内：真表达式、如果表达式、否则表达式。当情况变得更复杂时，请使用完整的 if 语句。

```python
Yes:
    one_line = 'yes' if predicate(value) else 'no'
    slightly_split = ('yes' if predicate(value)
                      else 'no, nein, nyet')
    the_longest_ternary_style_that_can_be_done = (
        'yes, true, affirmative, confirmed, correct'
        if predicate(value)
        else 'no, false, negative, nay')
```

```python
No:
    bad_line_breaking = ('yes' if predicate(value) else
                         'no')
    portion_too_long = ('yes'
                        if some_long_module.some_long_predicate_function(
                            really_long_variable_name)
                        else 'no, false, negative, nay')
```

### 2.12 Default Argument Values 

适用于大部分情况。

#### 2.12.1 Definition 

您可以在函数参数列表的末尾为变量指定值，例如，`def foo(a, b=0):`。如果仅用一个参数调用`foo`，则`b`的值被设定为0。如果用两个参数调用，则`b`的值为第二个参数的值。

#### 2.12.2 Pros 

通常情况下，您会有一个使用大量默认值的函数，但在少数情况下，您希望覆盖这些默认值。默认参数值提供了一种简单的方法来实现这一点，而无需为少数例外情况定义大量函数。由于Python不支持方法/函数的重载，默认参数是一种“伪装”重载行为的简单方式。

#### 2.12.3 Cons 

默认参数在模块加载时只会被评估一次。如果参数是可变对象，例如列表或字典，这可能会导致问题。如果函数修改了该对象（例如，通过向列表中添加一个项目），则默认值会被修改。

#### 2.12.4 Decision 

可以使用，但需注意以下警告：

在函数或方法定义中，不要使用可变对象作为默认值。

```python
Yes: def foo(a, b=None):
         if b is None:
             b = []
Yes: def foo(a, b: Sequence | None = None):
         if b is None:
             b = []
Yes: def foo(a, b: Sequence = ()):  # 空元组是可以的，因为元组是不可变的
         ...
```

```python
from absl import flags
_FOO = flags.DEFINE_string(...)

No:  def foo(a, b=[]):
         ...
No:  def foo(a, b=time.time()):  # `b` 是不是用来表示这个模块被加载的时间？
         ...
No:  def foo(a, b=_FOO.value):  # sys.argv尚未被解析。...
         ...
No:  def foo(a, b: Mapping = {}):  # 仍然可能被传递给未检查的代码
         ...
```

### 2.13 Properties 

属性可以用于控制获取或设置需要简单计算或逻辑的属性。属性的实现必须符合常规属性访问的一般期望：即它们应该是廉价的、直接的，并且不令人意外。

#### 2.13.1 Definition 

一种将方法调用封装为获取和设置属性的标准属性访问的方式。

#### 2.13.2 Pros 

-   允许使用属性访问和赋值的API，而不是调用[getter和setter](#getters-and-setters)方法
-   可以用于将属性设置为只读
-   允许进行惰性计算
-   提供了一种在类的内部独立于类用户演变时，维护类的公共接口的方法

#### 2.13.3 Cons 

-   可以像运算符重载一样隐藏副作用
-   对于子类来说，这可能会造成混淆

#### 2.13.4 Decision 

属性是允许的，但与运算符重载一样，只有在必要时才应使用，并且应符合典型属性访问的期望；否则应遵循[getters and setters](#getters-and-setters)规则。

例如，使用属性来简单地获取和设置内部属性是不允许的：没有发生任何计算，因此该属性是多余的（[不如将属性设为公共](#getters-and-setters)）。相比之下，使用属性来控制属性访问或计算一个*微不足道*的派生值是允许的：逻辑简单且不令人意外。

属性应该使用 `@property` [装饰器](#s2.17-function-and-method-decorators) 创建。手动实现属性描述符被视为一种 [强大特性](#power-features)。

具有属性的继承可能并不明显。不要使用属性来实现子类可能希望重写和扩展的计算。

### 2.14 True/False Evaluations 

尽可能使用“隐式”假设（有一些注意事项）。

#### 2.14.1 Definition 

Python在布尔上下文中将某些值评估为`False`。一个简单的“经验法则”是，所有“空”的值都被视为假，因此`0, None, [], {}, ''`在布尔上下文中都评估为假。

#### 2.14.2 Pros 

使用Python布尔值的条件更易于阅读且不易出错。在大多数情况下，它们的执行速度也更快。

#### 2.14.3 Cons 

这对C/C++开发者来说可能显得有些奇怪。

#### 2.14.4 Decision 

如果可能的话，请使用“隐式”假值，例如，`if foo:` 而不是 `if foo != []:`。不过，有几个注意事项需要牢记：

-   始终使用 `if foo is None:`（或 `is not None`）来检查 `None` 值。例如，当测试一个默认值为 `None` 的变量或参数是否被设置为其他值时。其他值可能是在布尔上下文中为假的值！

-   永远不要使用 `==` 将布尔变量与 `False` 进行比较。请改用 `if not x:`。如果需要区分 `False` 和 `None`，可以将表达式串联起来，例如 `if not x and x is not None:`

-   对于序列（字符串、列表、元组），可以利用空序列为假这一事实，因此`if seq:`和`if not seq:`比`if len(seq):`和`if not len(seq):`更为可取

-   在处理整数时，隐式的假值可能带来的风险大于收益（即，意外地将`None`视为0）。您可以将一个已知为整数的值（并且不是`len()`的结果）与整数0进行比较

    ```python
    Yes: if not users:
             print('no users')
    
         if i % 10 == 0:
             self.handle_multiple_of_ten()
    
         def f(x=None):
             if x is None:
                 x = []
    ```

    ```python
    No:  if len(users) == 0:
             print('no users')
    
         if not i % 10:
             self.handle_multiple_of_ten()
    
         def f(x=None):
             x = x or []
    ```

-   请注意，`'0'`（即，作为字符串的`0`）的值为真

-   请注意，Numpy 数组在隐式布尔上下文中可能会引发异常。在测试 `np.array` 的空性时，建议使用 `.size` 属性（例如，`if not users.size`）


### 2.16 Lexical Scoping 

可以使用。

#### 2.16.1 Definition 

嵌套的 Python 函数可以引用在外部函数中定义的变量，但不能对其进行赋值。变量绑定是通过词法作用域来解析的，即基于静态程序文本。在一个代码块中对某个名称的任何赋值都会导致 Python 将对该名称的所有引用视为局部变量，即使这些引用出现在赋值之前。如果出现全局声明，则该名称被视为全局变量。

该功能使用的一个示例是：

```python
def get_adder(summand1: float) -> Callable[[float], float]:
    """Returns a function that adds numbers to a given number."""
    def adder(summand2: float) -> float:
        return summand1 + summand2

    return adder
```

#### 2.16.2 Pros 

通常会导致更清晰、更优雅的代码。这对经验丰富的Lisp和Scheme（以及Haskell和ML等）程序员尤其令人感到安慰。


#### 2.16.3 Cons 

可能会导致令人困惑的错误，例如基于 [PEP-0227](https://peps.python.org/pep-0227/) 的这个例子：

```python
i = 4
def foo(x: Iterable[int]):
    def bar():
        print(i, end='')
    # ...
    # A bunch of code here
    # ...
    for i in x:  # Ah, i *is* local to foo, so this is what bar sees
        print(i, end='')
    bar()
```

因此，`foo([1, 2, 3])` 将打印 `1 2 3 3`，而不是 `1 2 3 4`。

#### 2.16.4 Decision 

可以使用。

### 2.17 Function and Method Decorators 

在有明显优势的情况下，谨慎使用装饰器。避免使用 `staticmethod`，并限制 `classmethod` 的使用。

#### 2.17.1 Definition 

[函数和方法的装饰器](https://docs.python.org/3/glossary.html#term-decorator)（也称为“`@` 符号”）。一个常见的装饰器是 `@property`，用于将普通方法转换为动态计算的属性。然而，装饰器语法也允许用户定义装饰器。具体来说，对于某个函数 `my_decorator`，如下所示：

```python
class C:
    @my_decorator
    def method(self):
        # method body ...
```

等价于：

```python
class C:
    def method(self):
        # method body ...
    method = my_decorator(method)
```

#### 2.17.2 Pros 

优雅地指定了一种方法上的某种变换；该变换可能消除一些重复的代码，强制执行不变式等。

#### 2.17.3 Cons 

装饰器可以对函数的参数或返回值执行任意操作，从而导致意想不到的隐式行为。此外，装饰器在对象定义时执行。对于模块级对象（类、模块函数等），这发生在导入时。装饰器代码中的错误几乎不可能恢复。

#### 2.17.4 Decision 

在有明显优势的情况下，谨慎使用装饰器。装饰器应遵循与函数相同的导入和命名指南。装饰器的pydoc应清楚地说明该函数是一个装饰器。为装饰器编写单元测试。

在装饰器本身避免外部依赖（例如，不要依赖文件、套接字、数据库连接等），因为在装饰器运行时（可能是在导入时，例如通过`pydoc`或其他工具），这些依赖可能不可用。一个使用有效参数调用的装饰器应该（尽可能地）保证在所有情况下都能成功。

装饰器是“顶层代码”的一个特殊情况——有关更多讨论，请参见 [main](#s3.17-main)。

除非为了与现有库中定义的API集成而被迫使用`staticmethod`，否则不要使用它。请改为编写模块级函数。

仅在编写命名构造函数或修改必要全局状态（例如进程范围内的缓存）的类特定例程时使用 `classmethod`。

### 2.18 Threading 

不要依赖内置类型的原子性。

虽然Python的内置数据类型如字典似乎具有原子操作，但在某些边缘情况下，它们并不是原子的（例如，如果`__hash__`或`__eq__`被实现为Python方法），因此不应依赖它们的原子性。同样，你也不应依赖原子变量赋值（因为这又依赖于字典）。

使用 `queue` 模块的 `Queue` 数据类型作为线程之间传递数据的首选方式。否则，使用 `threading` 模块及其锁原语。优先使用条件变量和 `threading.Condition`，而不是使用更低级别的锁。


### 2.19 Power Features 

避免这些特征。

#### 2.19.1 Definition 

Python是一种极其灵活的语言，提供了许多华丽的特性，例如自定义元类、对字节码的访问、即时编译、动态继承、对象重新父级、导入黑客、反射（例如`getattr()`的一些用法）、系统内部的修改、实现自定义清理的`__del__`方法等。

#### 2.19.2 Pros 

这些是强大的语言特性。它们可以使你的代码更加简洁。

#### 2.19.3 Cons 

在不绝对必要的情况下，使用这些“酷炫”的特性是非常诱人的。然而，使用不寻常特性编写的代码往往更难以阅读、理解和调试。起初（对于原作者来说）似乎并非如此，但在重新审视代码时，通常会发现这比那些虽然更长但更直接的代码要困难得多。

#### 2.19.4 Decision 

在您的代码中避免使用这些特性。

标准库模块和类内部使用这些特性的可以使用（例如，`abc.ABCMeta`、`dataclasses` 和 `enum`）。


### 2.20 Modern Python: from \_\_future\_\_ imports 

新的语言版本的语义变化可能需要通过一个特殊的未来导入来控制，以便在早期运行时的每个文件基础上启用它们。

#### 2.20.1 Definition 

能够通过 `from __future__ import` 语句启用一些更现代的特性，使得可以提前使用预期未来 Python 版本中的特性。

#### 2.20.2 Pros 

这已被证明使得运行时版本升级更加顺畅，因为可以在每个文件的基础上进行更改，同时声明兼容性并防止这些文件中的回归。现代代码更易于维护，因为它不太可能积累在未来运行时升级中会造成问题的技术债务。


#### 2.20.3 Cons 

这样的代码可能在非常旧的解释器版本上无法运行，特别是在引入所需的未来语句之前。这种需求在支持极其多样化环境的项目中更为常见。


#### 2.20.4 Decision 

##### from \_\_future\_\_ imports

鼓励使用 `from __future__ import` 语句。这允许给定的源文件今天开始使用更现代的 Python 语法特性。一旦您不再需要在一个特性被隐藏在 `__future__` 导入后的版本上运行，您可以随意删除这些行。

在可能在版本低至 3.5 而非 >= 3.7 上执行的代码中，导入：

```python
from __future__ import generator_stop
```

有关更多信息，请阅读[Python未来声明定义](https://docs.python.org/3/library/__future__.html)文档。

请在您确信代码仅在足够现代的环境中使用之前，不要移除这些导入。即使您今天在代码中并未使用特定未来导入所启用的功能，保留它在文件中可以防止后续对代码的修改无意中依赖于旧的行为。

根据需要使用其他 `from __future__` 导入语句。


### 2.21 Type Annotated Code 

您可以根据 [PEP-484](https://peps.python.org/pep-0484/) 为 Python 代码添加类型提示，并使用像 [pytype](https://github.com/google/pytype) 这样的类型检查工具在构建时对代码进行类型检查。

类型注解可以在源代码中或在[stub pyi 文件](https://peps.python.org/pep-0484/#stub-files)中。尽可能地，注解应当在源代码中。对于第三方或扩展模块，使用 pyi 文件。


#### 2.21.1 Definition 

类型注解（或称“类型提示”）用于函数或方法的参数和返回值：

```python
def func(a: int) -> list[int]:
```

您还可以使用类似于 [PEP-526](https://peps.python.org/pep-0526/) 的语法来声明变量的类型：

```python
a: SomeType = some_func()
```

#### 2.21.2 Pros 

类型注解提高了代码的可读性和可维护性。类型检查器将许多运行时错误转换为构建时错误，并减少您使用[Power Features](#power-features)的能力。

#### 2.21.3 Cons 

您需要保持类型声明的最新状态。您可能会看到一些您认为是有效代码的类型错误。使用[type checker](https://github.com/google/pytype)可能会降低您使用[Power Features](#power-features)的能力。

#### 2.21.4 Decision 

在更新代码时，强烈建议启用Python类型分析。在添加或修改公共API时，请包含类型注释，并在构建系统中通过pytype启用检查。由于静态分析在Python中相对较新，我们承认不希望出现的副作用（例如错误推断的类型）可能会阻碍某些项目的采用。在这些情况下，建议作者在BUILD文件或代码本身中适当地添加带有TODO的注释或链接到描述当前阻碍类型注释采用的问题的错误。


## 3 Python Style Rules 

### 3.1 Semicolons 

请勿用分号结束您的行，也不要使用分号将两个语句放在同一行。

### 3.2 Line length 

最大行长度为*80个字符*。

80个字符限制的明确例外：

-   长导入语句
-   URLs、路径名或注释中的长标志
-   长字符串模块级常量不包含空格，这使得在诸如URL或路径名等情况下，跨行拆分变得不方便
    -   Pylint 禁用注释。（例如：`# pylint: disable=invalid-name`）

请勿使用反斜杠进行[显式行续接](https://docs.python.org/3/reference/lexical_analysis.html#explicit-line-joining)。

相反，可以利用Python的[在括号、方括号和大括号内的隐式行连接](http://docs.python.org/reference/lexical_analysis.html#implicit-line-joining)。如果有必要，可以在一个表达式周围添加一对额外的括号。

请注意，这条规则并不禁止在字符串中使用反斜杠转义的换行符（见[下文](#strings)）。

```python
Yes: foo_bar(self, width, height, color='black', design=None, x='foo',
             emphasis=None, highlight=0)
```

```python

Yes: if (width == 0 and height == 0 and
         color == 'red' and emphasis == 'strong'):

     (bridge_questions.clarification_on
      .average_airspeed_of.unladen_swallow) = 'African or European?'

     with (
         very_long_first_expression_function() as spam,
         very_long_second_expression_function() as beans,
         third_thing() as eggs,
     ):
       place_order(eggs, beans, spam, beans)
```

```python

No:  if width == 0 and height == 0 and \
         color == 'red' and emphasis == 'strong':

     bridge_questions.clarification_on \
         .average_airspeed_of.unladen_swallow = 'African or European?'

     with very_long_first_expression_function() as spam, \
           very_long_second_expression_function() as beans, \
           third_thing() as eggs:
       place_order(eggs, beans, spam, beans)
```

当一个字面字符串无法在单行中显示时，请使用括号进行隐式行连接。

```python
x = ('This will build a very long long '
     'long long long long long long string')
```

优先在尽可能高的句法层次上换行。如果必须换行两次，则两次都应在相同的句法层次上换行。

```python
Yes: bridgekeeper.answer(
         name="Arthur", quest=questlib.find(owner="Arthur", perilous=True))

     answer = (a_long_line().of_chained_methods()
               .that_eventually_provides().an_answer())

     if (
         config is None
         or 'editor.language' not in config
         or config['editor.language'].use_spaces is False
     ):
       use_tabs()
```

```python
No: bridgekeeper.answer(name="Arthur", quest=questlib.find(
        owner="Arthur", perilous=True))

    answer = a_long_line().of_chained_methods().that_eventually_provides(
        ).an_answer()

    if (config is None or 'editor.language' not in config or config[
        'editor.language'].use_spaces is False):
      use_tabs()

```

在评论中，如果有必要，将较长的URL单独放在一行。

```python
Yes:  # See details at
      # http://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html
```

```python
No:  # See details at
     # http://www.example.com/us/developer/documentation/api/content/\
     # v2.0/csv_file_name_extension_full_specification.html
```

请注意上述行续例中元素的缩进；有关解释，请参见[indentation](#s3.4-indentation)部分。

在所有其他情况下，当一行超过80个字符，并且[Black](https://github.com/psf/black)或[Pyink](https://github.com/google/pyink)自动格式化工具无法将该行缩短到限制之内时，该行可以超过此最大限制。鼓励作者在合理的情况下根据上述说明手动断开该行。


### 3.3 Parentheses 

谨慎使用括号。

在元组周围使用括号是可以的，但并不是必须的。在返回语句或条件语句中，除非使用括号表示隐含的行继续或指示一个元组，否则不应使用括号。

```python
Yes: if foo:
         bar()
     while x:
         x = bar()
     if x and y:
         bar()
     if not x:
         bar()
     # 对于一个包含1个项目的元组，括号比逗号在视觉上更为明显
     onesie = (foo,)
     return foo
     return spam, beans
     return (spam, beans)
     for (x, y) in dict.items(): ...
```

```python
No:  if (x):
         bar()
     if not(x):
         bar()
     return (foo)
```

### 3.4 Indentation 

将代码块缩进 *4 个空格*。

切勿使用制表符。隐含的行续应将换行的元素垂直对齐（见[line length examples](#s3.2-line-length)），或者使用悬挂的4个空格缩进。闭合的（圆形、方形或花括号）括号可以放在表达式的末尾，或单独放在新行上，但此时应与对应的开括号所在行保持相同的缩进。

```python
Yes:   # 与开口分隔符对齐
       foo = long_function_name(var_one, var_two,
                                var_three, var_four)
       meal = (spam,
               beans)

       # 与字典中的开口分隔符对齐
       foo = {
           'long_dictionary_key': value1 +
                                  value2,
           ...
       }

       # 4-space 悬挂缩进；第一行无内容
       foo = long_function_name(
           var_one, var_two, var_three,
           var_four)
       meal = (
           spam,
           beans)

       # 4-space 悬挂缩进；第一行无内容，
       # 在新的一行上关闭括号
       foo = long_function_name(
           var_one, var_two, var_three,
           var_four
       )
       meal = (
           spam,
           beans,
       )

       # 4-space hanging indent in a dictionary.
       foo = {
           'long_dictionary_key':
               long_dictionary_value,
           ...
       }
```

```python
No:    # 第一行的内容是禁止的
       foo = long_function_name(var_one, var_two,
           var_three, var_four)
       meal = (spam,
           beans)

       # 2-space hanging indent forbidden.
       foo = long_function_name(
         var_one, var_two, var_three,
         var_four)

       # No hanging indent in a dictionary.
       foo = {
           'long_dictionary_key':
           long_dictionary_value,
           ...
       }
```

#### 3.4.1 Trailing commas in sequences of items? 

在项目序列中，只有当闭合容器标记`]`、`)`或`}`不与最后一个元素位于同一行时，才建议使用尾随逗号，同时对于只有一个元素的元组也适用。尾随逗号的存在还被用作提示我们的Python代码自动格式化工具 [Black](https://github.com/psf/black) 或 [Pyink](https://github.com/google/pyink)，以指示它在最后一个元素后存在`,`时，将项目容器自动格式化为每行一个项目。

```python
Yes:   golomb3 = [0, 1, 3]
       golomb4 = [
           0,
           1,
           4,
           6,
       ]
```

```python
No:    golomb4 = [
           0,
           1,
           4,
           6,]
```

### 3.5 Blank Lines 

在顶层定义之间留出两个空行，无论是函数定义还是类定义。在方法定义之间以及`class`的文档字符串与第一个方法之间留出一个空行。在`def`行后不要留空行。在函数或方法内部，根据判断适当使用单个空行。

空行不必与定义相连。例如，紧接在函数、类和方法定义之前的相关注释是有意义的。考虑一下，您的注释是否可以作为文档字符串的一部分更有用。

### 3.6 Whitespace 

遵循标准排版规则，在标点符号周围使用空格。

括号、方括号或大括号内不得有空格。

```python
Yes: spam(ham[1], {'eggs': 2}, [])
```

```python
No:  spam( ham[ 1 ], { 'eggs': 2 }, [ ] )
```

在逗号、分号或冒号之前不要留空格。在逗号、分号或冒号之后应留空格，但在行末除外。

```python
Yes: if x == 4:
         print(x, y)
     x, y = y, x
```

```python
No:  if x == 4 :
         print(x , y)
     x , y = y , x
```

在开始参数列表、索引或切片的开放括号/方括号之前，不应有空格。

```python
Yes: spam(1)
```

```python
No:  spam (1)
```

```python
Yes: dict['key'] = list[index]
```

```python
No:  dict ['key'] = list [index]
```

没有多余的空格。

在赋值操作符（`=`）、比较操作符（`==, <, >, !=, <>, <=, >=, in, not in, is, is not`）和布尔运算符（`and, or, not`）的两侧各留一个空格。对于算术运算符（`+`, `-`, `*`, `/`, `//`, `%`, `**`, `@`）周围空格的插入，请根据你的判断进行处理。

```python
Yes: x == 1
```

```python
No:  x<1
```

在传递关键字参数或定义默认参数值时，切勿在`=`周围使用空格，唯一的例外是：[当存在类型注解时](#typing-default-values)，*应*在默认参数值的`=`周围使用空格。

```python
Yes: def complex(real, imag=0.0): return Magic(r=real, i=imag)
Yes: def complex(real, imag: float = 0.0): return Magic(r=real, i=imag)
```

```python
No:  def complex(real, imag = 0.0): return Magic(r = real, i = imag)
No:  def complex(real, imag: float=0.0): return Magic(r = real, i = imag)
```

不要使用空格来垂直对齐连续行上的标记，因为这会增加维护负担（适用于 `:`, `#`, `=`, 等等）：

```python
Yes:
  foo = 1000  # comment
  long_name = 2  # comment that should not be aligned

  dictionary = {
      'foo': 1,
      'long_name': 2,
  }
```

```python
No:
  foo       = 1000  # comment
  long_name = 2     # comment that should not be aligned

  dictionary = {
      'foo'      : 1,
      'long_name': 2,
  }
```


### 3.7 Shebang Line 

大多数 `.py` 文件不需要以 `#!` 行开头。程序的主文件应以 `#!/usr/bin/env python3` 开始（以支持虚拟环境），或者根据 [PEP-394](https://peps.python.org/pep-0394/) 使用 `#!/usr/bin/python3`。

这一行由内核用于查找Python解释器，但在导入模块时被Python忽略。它仅在文件旨在直接执行时是必要的。


### 3.8 Comments and Docstrings 

确保为模块、函数、方法的文档字符串和内联注释使用正确的风格。

#### 3.8.1 Docstrings 

Python使用*docstrings*来对代码进行文档化。文档字符串是一个字符串，它是包、模块、类或函数中的第一条语句。这些字符串可以通过对象的`__doc__`成员自动提取，并被`pydoc`使用。（尝试在您的模块上运行 `pydoc` 以查看其外观。）始终使用三重引号 `"""` 格式来编写文档字符串（根据 [PEP 257](https://peps.python.org/pep-0257/)）。文档字符串应组织为一个摘要行（一个物理行，不超过 80 个字符），以句号、问号或感叹号结束。当编写更多内容时（鼓励这样做），必须在摘要行后留出一个空行，然后在与第一行第一个引号相同的光标位置开始其余的文档字符串。下面还有更多关于文档字符串的格式指南。


#### 3.8.2 Modules 

每个文件应包含许可证模板。选择与项目所使用的许可证相对应的模板（例如，Apache 2.0、BSD、LGPL、GPL）。

文件应以文档字符串开头，描述模块的内容和使用方法。
```python
"""A one-line summary of the module or program, terminated by a period.

Leave one blank line.  The rest of this docstring should contain an
overall description of the module or program.  Optionally, it may also
contain a brief description of exported classes and functions and/or usage
examples.

Typical usage example:

  foo = ClassFoo()
  bar = foo.FunctionBar()
"""
```

##### 3.8.2.1 Test modules 

测试文件的模块级文档字符串并不是必需的。只有在可以提供额外信息时，才应包含它们。

示例包括有关测试应如何进行的一些具体细节、对不寻常的设置模式的解释、对外部环境的依赖等。

```python
"""This blaze test uses golden files.

You can update those files by running
`blaze run //foo/bar:foo_test -- --update_golden_files` from the `google3`
directory.
"""
```

不应使用不提供任何新信息的文档字符串。

```python
"""Tests for foo.bar."""
```

#### 3.8.3 Functions and Methods 

在本节中，“函数”指的是一种方法、函数、生成器或属性。

每个具有以下一个或多个特性的函数都必须包含文档字符串：

-   作为公共API的一部分
-   非平凡大小
-   非显而易见的逻辑

文档字符串应提供足够的信息，以便在不阅读函数代码的情况下编写对该函数的调用。文档字符串应描述函数的调用语法及其语义，但通常不应涉及其实现细节，除非这些细节与函数的使用方式相关。例如，一个作为副作用改变其参数之一的函数应在其文档字符串中指出这一点。否则，与调用者无关的函数实现的微妙但重要的细节，最好以注释的形式与代码一起表达，而不是放在函数的文档字符串中。

文档字符串可以是描述性风格（`"""从 Bigtable 获取行。"""`）或命令式风格（`"""获取来自 Bigtable 的行。"""`），但在一个文件内风格应保持一致。`@property` 数据描述符的文档字符串应与属性或<a href="#doc-function-args">函数参数</a>的文档字符串使用相同的风格（`"""Bigtable 路径。"""`，而不是 `"""返回 Bigtable 路径。"""`）。

某些函数的特定方面应在特别的部分中进行记录，具体如下所列。每个部分以一个标题行开始，标题行以冒号结束。除了标题之外的所有部分应保持两个或四个空格的悬挂缩进（在一个文件中保持一致）。在函数的名称和签名足够信息丰富，以至于可以用一行文档字符串恰当地描述的情况下，这些部分可以省略。

<a id="doc-function-args"></a>
[*Args:*](#doc-function-args)
:   逐一列出每个参数的名称。描述应紧随名称之后，并用冒号分隔，后面跟一个空格或换行。如果描述过长，无法在单行80个字符内显示，则应使用比参数名称多2或4个空格的悬挂缩进（在文件中保持一致）。描述应包括所需的类型，如果代码中没有相应的类型注释。如果一个函数接受`*foo`（可变长度参数列表）和/或`**bar`（任意关键字参数），则应将其列为`*foo`和`**bar`。

<a id="doc-function-returns"></a>
[*Returns:* (or *Yields:* for generators)](#doc-function-returns)
:   描述返回值的语义，包括类型注释未提供的任何类型信息。如果函数仅返回 None，则此部分不是必需的。如果文档字符串以“Return”、“Returns”、“Yield”或“Yields”开头（例如 `"""Returns row from Bigtable as a tuple of strings."""`）*并且*开头的句子足以描述返回值，则可以省略此部分。不要模仿较旧的“NumPy 风格”（[示例](https://numpy.org/doc/1.24/reference/generated/numpy.linalg.qr.html)），该风格常常将元组返回值记录为多个具有各自名称的返回值（从不提及元组）。相反，应将此类返回值描述为：“返回：一个元组 (mat_a, mat_b)，其中 mat_a 是...，而...”。文档字符串中的辅助名称不必与函数体中使用的任何内部名称相对应（因为这些不是 API 的一部分）。如果函数使用 `yield`（是一个生成器），则 `Yields:` 部分应记录 `next()` 返回的对象，而不是调用评估的生成器对象本身。

<a id="doc-function-raises"></a>
[*Raises:*](#doc-function-raises)
:   列出所有与接口相关的异常，并附上描述。使用类似的异常名称 + 冒号 + 空格或换行以及悬挂缩进的样式，如*Args:*中所述。您不应记录在文档字符串中指定的API被违反时引发的异常（因为这将悖论性地使违反API时的行为成为API的一部分）。

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Fetches rows from a Smalltable.

    Retrieves rows pertaining to the given keys from the Table instance
    represented by table_handle.  String keys will be UTF-8 encoded.

    Args:
        table_handle: An open smalltable.Table instance.
        keys: A sequence of strings representing the key of each table
          row to fetch.  String keys will be UTF-8 encoded.
        require_all_keys: If True only rows with values set for all keys will be
          returned.

    Returns:
        A dict mapping keys to the corresponding table row data
        fetched. Each row is represented as a tuple of strings. For
        example:

        {b'Serak': ('Rigel VII', 'Preparer'),
         b'Zim': ('Irk', 'Invader'),
         b'Lrrr': ('Omicron Persei 8', 'Emperor')}

        Returned keys are always bytes.  If a key from the keys argument is
        missing from the dictionary, then that row was not found in the
        table (and require_all_keys must have been False).

    Raises:
        IOError: An error occurred accessing the smalltable.
    """
```

同样，这种带有换行的`Args:`变体也是允许的：

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Fetches rows from a Smalltable.

    Retrieves rows pertaining to the given keys from the Table instance
    represented by table_handle.  String keys will be UTF-8 encoded.

    Args:
      table_handle:
        An open smalltable.Table instance.
      keys:
        A sequence of strings representing the key of each table row to
        fetch.  String keys will be UTF-8 encoded.
      require_all_keys:
        If True only rows with values set for all keys will be returned.

    Returns:
      A dict mapping keys to the corresponding table row data
      fetched. Each row is represented as a tuple of strings. For
      example:

      {b'Serak': ('Rigel VII', 'Preparer'),
       b'Zim': ('Irk', 'Invader'),
       b'Lrrr': ('Omicron Persei 8', 'Emperor')}

      Returned keys are always bytes.  If a key from the keys argument is
      missing from the dictionary, then that row was not found in the
      table (and require_all_keys must have been False).

    Raises:
      IOError: An error occurred accessing the smalltable.
    """
```

##### 3.8.3.1 Overridden Methods 

一种重写基类方法的方法，如果明确使用[`@override`](https://typing-extensions.readthedocs.io/en/latest/#override)（来自`typing_extensions`或`typing`模块）进行装饰，则不需要文档字符串，除非重写的方法的行为实质性地细化了基方法的契约，或者需要提供细节（例如，记录额外的副作用），在这种情况下，重写的方法必须包含至少这些差异的文档字符串。

```python
from typing_extensions import override

class Parent:
  def do_something(self):
    """Parent method, includes docstring."""

# Child class, method annotated with override.
class Child(Parent):
  @override
  def do_something(self):
    pass
```

```python
# Child class, but without @override decorator, a docstring is required.
class Child(Parent):
  def do_something(self):
    pass

# Docstring is trivial, @override is sufficient to indicate that docs can be
# found in the base class.
class Child(Parent):
  @override
  def do_something(self):
    """See base class."""
```

#### 3.8.4 Classes 

类下面应该有一个文档字符串，描述该类。公共属性（不包括[属性](#properties)）应在此处的`Attributes`部分进行文档说明，并遵循与[函数的`Args`](#doc-function-args)部分相同的格式。

```python
class SampleClass:
    """Summary of class here.

    Longer class information...
    Longer class information...

    Attributes:
        likes_spam: A boolean indicating if we like SPAM or not.
        eggs: An integer count of the eggs we have laid.
    """

    def __init__(self, likes_spam: bool = False):
        """Initializes the instance based on spam preference.

        Args:
          likes_spam: Defines if instance exhibits this preference.
        """
        self.likes_spam = likes_spam
        self.eggs = 0

    @property
    def butter_sticks(self) -> int:
        """The number of butter sticks we have."""
```

所有类的文档字符串应以一行摘要开始，该摘要描述类实例所代表的内容。这意味着`Exception`的子类也应描述该异常所代表的内容，而不是它可能发生的上下文。类的文档字符串不应重复不必要的信息，例如该类是一个类。

```python
# Yes:
class CheeseShopAddress:
  """The address of a cheese shop.

  ...
  """

class OutOfCheeseError(Exception):
  """No more cheese is available."""
```

```python
# No:
class CheeseShopAddress:
  """Class that describes the address of a cheese shop.

  ...
  """

class OutOfCheeseError(Exception):
  """Raised when no more cheese is available."""
```

#### 3.8.5 Block and Inline Comments 

最终需要添加注释的地方是在代码的复杂部分。如果你在下一个[代码审查](http://en.wikipedia.org/wiki/Code_review)中需要解释它，那么你应该现在就进行注释。在复杂操作开始之前，应该添加几行注释。对于不明显的操作，则在行末添加注释。

```python
# We use a weighted dictionary search to find out where i is in
# the array.  We extrapolate position based on the largest num
# in the array and the array size and then do binary search to
# get the exact number.

if i & (i-1) == 0:  # True if i is 0 or a power of 2.
```

为了提高可读性，这些注释应至少距离代码有两个空格，并以注释字符`#`开头，随后在注释文本之前至少留一个空格。

另一方面，切勿对代码进行描述。假设阅读代码的人比你更了解Python（尽管他们可能不清楚你想要实现的目标）。

```python
# BAD COMMENT: Now go through the b array and make sure whenever i occurs
# the next element is i+1
```

<!-- The next section is copied from the C++ style guide. -->

#### 3.8.6 Punctuation, Spelling, and Grammar 

请注意标点、拼写和语法；阅读写得好的评论比阅读写得差的评论要容易得多。

评论应当像叙述文本一样易于阅读，使用恰当的大小写和标点。在许多情况下，完整的句子比句子片段更易于阅读。较短的评论，例如代码行末尾的评论，有时可以不那么正式，但你应保持风格的一致性。

尽管代码审查者指出你在应该使用分号的地方使用了逗号可能会让人感到沮丧，但源代码保持高水平的清晰度和可读性是非常重要的。恰当的标点、拼写和语法有助于实现这一目标。

### 3.10 Strings 

使用[f-string](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)、`%`运算符或`format`方法来格式化字符串，即使所有参数都是字符串。请根据自己的判断在字符串格式化选项之间做出选择。使用`+`进行单次连接是可以的，但不要使用`+`进行格式化。

```python
Yes: x = f'name: {name}; score: {n}'
     x = '%s, %s!' % (imperative, expletive)
     x = '{}, {}'.format(first, second)
     x = 'name: %s; score: %d' % (name, n)
     x = 'name: %(name)s; score: %(score)d' % {'name':name, 'score':n}
     x = 'name: {}; score: {}'.format(name, n)
     x = a + b
```

```python
No: x = first + ', ' + second
    x = 'name: ' + name + '; score: ' + str(n)
```

避免在循环中使用 `+` 和 `+=` 运算符来累积字符串。在某些情况下，使用加法累积字符串可能导致二次而非线性的运行时间。尽管这种常见的累积方式在 CPython 中可能会被优化，但这属于实现细节。优化适用的条件并不容易预测，并且可能会发生变化。相反，应该将每个子字符串添加到一个列表中，并在循环结束后使用 `''.join` 将列表连接起来，或者将每个子字符串写入 `io.StringIO` 缓冲区。这些技术在时间复杂度上始终具有摊销线性运行时间复杂度。

```python
Yes: items = ['<table>']
     for last_name, first_name in employee_list:
         items.append('<tr><td>%s, %s</td></tr>' % (last_name, first_name))
     items.append('</table>')
     employee_table = ''.join(items)
```

```python
No: employee_table = '<table>'
    for last_name, first_name in employee_list:
        employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
    employee_table += '</table>'
```

在一个文件中，要保持字符串引号字符的一致性。选择使用`'`或`"`，并坚持使用它。在字符串中使用另一种引号字符是可以的，这样可以避免在字符串内需要使用反斜杠转义引号字符。

```python
Yes:
  Python('Why are you hiding your eyes?')
  Gollum("I'm scared of lint errors.")
  Narrator('"Good!" thought a happy Python reviewer.')
```

```python
No:
  Python("Why are you hiding your eyes?")
  Gollum('The lint. It burns. It burns us.')
  Gollum("Always the great lint. Watching. Watching.")
```

建议使用 `"""` 来表示多行字符串，而不是 `'''`。项目可以选择仅在同时使用 `'` 表示常规字符串的情况下，使用 `'''` 来表示所有非文档字符串的多行字符串。无论如何，文档字符串必须使用 `"""`。

多行字符串不会随着程序其余部分的缩进而变化。如果您需要避免在字符串中嵌入额外的空格，可以使用连接的单行字符串或使用 [`textwrap.dedent()`](https://docs.python.org/3/library/textwrap.html#textwrap.dedent) 的多行字符串，以去除每行开头的空格：

```python
  No:
  long_string = """This is pretty ugly.
Don't do this.
"""
```

```python
  Yes:
  long_string = """This is fine if your use case can accept
      extraneous leading spaces."""
```

```python
  Yes:
  long_string = ("And this is fine if you cannot accept\n" +
                 "extraneous leading spaces.")
```

```python
  Yes:
  long_string = ("And this too is fine if you cannot accept\n"
                 "extraneous leading spaces.")
```

```python
  Yes:
  import textwrap

  long_string = textwrap.dedent("""\
      This is also fine, because textwrap.dedent()
      will collapse common leading spaces in each line.""")
```

请注意，在这里使用反斜杠并不违反对[显式行续](#line-length)的禁止；在这种情况下，反斜杠是[转义字符串字面量中的换行符](https://docs.python.org/3/reference/lexical_analysis.html#string-and-bytes-literals)。


#### 3.10.1 Logging 

对于那些将模式字符串（带有%-占位符）作为第一个参数的日志记录函数：始终使用字符串字面量（而不是f-string！）作为第一个参数，并将模式参数作为后续参数传递。一些日志记录实现会将未展开的模式字符串收集为可查询的字段。这也可以防止花费时间渲染一个没有配置输出的日志记录器的消息。

```python
  Yes:
  import tensorflow as tf
  logger = tf.get_logger()
  logger.info('TensorFlow Version is: %s', tf.__version__)
```

```python
  Yes:
  import os
  from absl import logging

  logging.info('Current $PAGER is: %s', os.getenv('PAGER', default=''))

  homedir = os.getenv('HOME')
  if homedir is None or not os.access(homedir, os.W_OK):
    logging.error('Cannot write to home directory, $HOME=%r', homedir)
```

```python
  No:
  import os
  from absl import logging

  logging.info('Current $PAGER is:')
  logging.info(os.getenv('PAGER', default=''))

  homedir = os.getenv('HOME')
  if homedir is None or not os.access(homedir, os.W_OK):
    logging.error(f'Cannot write to home directory, $HOME={homedir!r}')
```

#### 3.10.2 Error Messages 

错误信息（例如：关于异常的消息字符串，如 `ValueError`，或显示给用户的消息）应遵循三个准则：

1.  信息需要与实际的错误状态精确匹配

2.  插值片段必须始终清晰可辨

3.  它们应该允许简单的自动化处理（例如，使用grep命令）

```python
  Yes:
  if not 0 <= p <= 1:
    raise ValueError(f'Not a probability: {p=}')

  try:
    os.rmdir(workdir)
  except OSError as error:
    logging.warning('Could not remove directory (reason: %r): %r',
                    error, workdir)
```

```python
  No:
  if p < 0 or p > 1:  # PROBLEM: also false for float('nan')!
    raise ValueError(f'Not a probability: {p=}')

  try:
    os.rmdir(workdir)
  except OSError:
    # PROBLEM: Message makes an assumption that might not be true:
    # Deletion might have failed for some other reason, misleading
    # whoever has to debug this.
    logging.warning('Directory already was deleted: %s', workdir)

  try:
    os.rmdir(workdir)
  except OSError:
    # PROBLEM: The message is harder to grep for than necessary, and
    # not universally non-confusing for all possible values of `workdir`.
    # Imagine someone calling a library function with such code
    # using a name such as workdir = 'deleted'. The warning would read:
    # "The deleted directory could not be deleted."
    logging.warning('The %s directory could not be deleted.', workdir)
```


### 3.11 Files, Sockets, and similar Stateful Resources 

在完成对文件和套接字的操作后，应显式关闭它们。这个规则自然扩展到内部使用套接字的可关闭资源，例如数据库连接，以及其他需要以类似方式关闭的资源。仅举几个例子，这还包括[mmap](https://docs.python.org/3/library/mmap.html)映射、[h5py文件对象](https://docs.h5py.org/en/stable/high/file.html)和[matplotlib.pyplot图形窗口](https://matplotlib.org/2.1.0/api/_as_gen/matplotlib.pyplot.close.html)。

不必要地保持文件、套接字或其他此类有状态对象处于打开状态有许多缺点：

-   它们可能会消耗有限的系统资源，例如文件描述符。处理许多此类对象的代码如果在使用后未能及时将其返回给系统，可能会不必要地耗尽这些资源
-   保持文件打开可能会阻止其他操作，例如移动或删除它们，或卸载文件系统
-   在程序中共享的文件和套接字可能在逻辑上被关闭后，无意中被读取或写入。如果它们实际上已经关闭，尝试从中读取或写入将引发异常，从而更早地使问题显现

此外，虽然文件和套接字（以及一些类似行为的资源）在对象被析构时会自动关闭，但将对象的生命周期与资源的状态耦合在一起是一种不良实践：

-   没有保证运行时何时会实际调用`__del__`方法。不同的Python实现使用不同的内存管理技术，例如延迟垃圾回收，这可能会任意且无限期地延长对象的生命周期。
-   意外的对文件的引用，例如在全局变量或异常回溯中，可能会使其存在的时间比预期更长。

依赖终结器进行自动清理并产生可观察的副作用，这一做法在多个十年和多种语言中被反复重新发现，导致了重大问题（例如，参见[这篇文章](https://wiki.sei.cmu.edu/confluence/display/java/MET12-J.+Do+not+use+finalizers)关于Java的讨论）。

管理文件和类似资源的首选方式是使用 [`with` 语句](http://docs.python.org/reference/compound_stmts.html#the-with-statement)：

```python
with open("hello.txt") as hello_file:
    for line in hello_file:
        print(line)
```

对于不支持`with`语句的类文件对象，请使用`contextlib.closing()`：

```python
import contextlib

with contextlib.closing(urllib.urlopen("http://www.python.org/")) as front_page:
    for line in front_page:
        print(line)
```

在上下文基础的资源管理不可行的少数情况下，代码文档必须清楚地解释资源生命周期是如何管理的。

### 3.12 TODO Comments 

对于临时代码、短期解决方案或虽然足够好但并不完美的代码，请使用 `TODO` 注释。

一个 `TODO` 注释以全大写的单词 `TODO` 开头，后接冒号，以及指向包含上下文的资源的链接，理想情况下是一个错误引用。错误引用更为理想，因为错误会被跟踪并有后续评论。接着用一个以连字符 `-` 引入的解释性字符串来补充这一上下文。

其目的是建立一个一致的 `TODO` 格式，以便能够进行搜索，从而获取更多细节。

```python
# TODO: crbug.com/192795 - Investigate cpufreq optimizations.
```

旧风格，曾被推荐，但在新代码中不鼓励使用：


```python
# TODO(crbug.com/192795): Investigate cpufreq optimizations.
# TODO(yourusername): Use a "\*" here for concatenation operator.
```

避免添加将个人或团队作为上下文的TODO。

```python
# TODO: @yourusername - File an issue and use a '*' for repetition.
```

如果你的 `TODO` 形式为“在未来的某个日期做某事”，请确保你要么包含一个非常具体的日期（例如“在2009年11月之前修复”），要么包含一个非常具体的事件（例如“当所有客户端都能处理XML响应时移除此代码”），以便未来的代码维护者能够理解。问题跟踪非常适合用于此。


### 3.13 Imports formatting 

导入语句应分行书写；对于`typing`和`collections.abc`的导入语句有[例外情况](#typing-imports)。

E.g.:

```python
Yes: from collections.abc import Mapping, Sequence
     import os
     import sys
     from typing import Any, NewType
```

```python
No:  import os, sys
```


导入语句总是放在文件的顶部，紧接在任何模块注释和文档字符串之后，并在模块全局变量和常量之前。导入语句应按从最通用到最不通用的顺序进行分组：

1.  Python未来的导入语句。例如：

    ```python
    from __future__ import annotations
    ```

    请参见[上文](#from-future-imports)以获取有关这些内容的更多信息。

2.  Python标准库导入。例如：

    ```python
    import sys
    ```

3.  第三方模块或包的导入。例如：

    
    ```python
    import tensorflow as tf
    ```

4.  代码库子包导入。例如：

    
    ```python
    from otherproject.ai import mind
    ```

5.  **已弃用：** 与此文件属于同一顶级子包的特定应用程序导入。例如：

    
    ```python
    from myproject.backend.hgwells import time_machine
    ```

    您可能会发现较旧的 Google Python 风格代码是这样做的，但这已不再是必需的。**鼓励新代码不必对此烦恼。** 只需将特定于应用程序的子包导入视为与其他子包导入相同即可。

    

在每个分组内，导入应根据每个模块的完整包路径（即`from path import ...`中的`path`）按字典顺序排序，忽略大小写。代码可以选择在导入部分之间放置一个空行。

```python
import collections
import queue
import sys

from absl import app
from absl import flags
import bs4
import cryptography
import tensorflow as tf

from book.genres import scifi
from myproject.backend import huxley
from myproject.backend.hgwells import time_machine
from myproject.backend.state_machine import main_loop
from otherproject.ai import body
from otherproject.ai import mind
from otherproject.ai import soul

# Older style code may have these imports down here instead:
#from myproject.backend.hgwells import time_machine
#from myproject.backend.state_machine import main_loop
```


### 3.14 Statements 

通常每行只包含一个陈述。

然而，只有当整个语句适合在一行上时，您才能将测试的结果放在与测试相同的行上。特别是，您绝对不能在`try`/`except`中这样做，因为`try`和`except`无法同时放在同一行上，而只有在没有`else`的情况下，您才能在`if`中这样做。

```python
Yes:

  if foo: bar(foo)
```

```python
No:

  if foo: bar(foo)
  else:   baz(foo)

  try:               bar(foo)
  except ValueError: baz(foo)

  try:
      bar(foo)
  except ValueError: baz(foo)
```

### 3.15 Getters and Setters 

获取器和设置器函数（也称为访问器和变更器）应在它们为获取或设置变量的值提供有意义的角色或行为时使用。

特别是在获取或设置变量的过程复杂或成本显著时，无论是当前还是在合理的未来，都应使用它们。

例如，如果一对获取器/设置器仅仅是读取和写入一个内部属性，那么这个内部属性应该被公开。相比之下，如果设置一个变量意味着某个状态被失效或重建，那么它应该是一个设置函数。函数调用暗示着可能正在发生一个非平凡的操作。或者，当需要简单逻辑时，[属性](#properties) 可能是一个选项，或者重构以不再需要获取器和设置器。

获取器和设置器应遵循[命名](#s3.16-naming)指南，例如`get_foo()`和`set_foo()`。

如果过去的行为允许通过某个属性进行访问，则不要将新的getter/setter函数绑定到该属性上。任何仍试图通过旧方法访问变量的代码都应明显中断，以便让它们意识到复杂性的变化。


### 3.16 Naming 

`module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`,
`function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`,
`function_parameter_name`, `local_var_name`, `query_proper_noun_for_thing`,
`send_acronym_via_https`.


函数名称、变量名称和文件名应具有描述性；避免使用缩写。特别是，不要使用对项目外的读者模糊或不熟悉的缩写，也不要通过删除单词中的字母来进行缩写。

始终使用 `.py` 文件名扩展名。切勿使用破折号。


#### 3.16.1 Names to Avoid 

-   单字符名称，除非在特定允许的情况下：

    -   计数器或迭代器（例如 `i`、`j`、`k`、`v` 等）。
    -   在`try/except`语句中，`e`作为异常标识符。
    -   在`with`语句中将`f`作为文件句柄
    -   私有[type variables](#typing-type-var)没有约束（例如，`_T = TypeVar("_T")`，`_P = ParamSpec("_P")`）

    请注意不要滥用单字符命名。一般来说，描述性应与名称的可见范围成正比。例如，`i` 可能是一个适合于 5 行代码块的良好名称，但在多个嵌套作用域中，它可能显得过于模糊。

-   在任何包/模块名称中使用破折号（`-`）

-   `__double_leading_and_trailing_underscore__` 名称（由 Python 保留）

-   冒犯性术语

-   不必要地包含变量类型的名称（例如：`id_to_name_dict`）

#### 3.16.2 Naming Conventions 

-   “内部”是指模块内部，或在类中受保护或私有的部分。

-   在模块变量和函数前加一个下划线（`_`）在一定程度上可以保护它们（代码检查工具会标记受保护成员的访问）。请注意，单元测试可以访问被测试模块中的受保护常量。

-   在实例变量或方法前加上双下划线（`__`，也称为“dunder”）实际上使得该变量或方法对其类私有（通过名称重整）；我们不鼓励使用这种方式，因为它会影响可读性和可测试性，并且并不是真正的私有。更推荐使用单下划线。

-   将相关的类和顶层函数放在同一个模块中。
    与Java不同，模块中不需要限制自己只能有一个类。

-   对于类名使用大写字母开头的单词（CapWords），而对于模块名则使用小写字母加下划线（lower\_with\_under.py）。尽管有一些旧的模块名为 CapWords.py，但现在不再推荐使用这种命名方式，因为当模块恰好以类的名称命名时，这会造成混淆。（“等一下——我写的是 `import StringIO` 还是 `from StringIO import StringIO`？”）

-   新的 *单元测试* 文件遵循符合 PEP 8 的 lower\_with\_under 方法命名规则，例如，`test_<method_under_test>_<state>`。为了与遵循 CapWords 函数命名规则的遗留模块保持一致（\*），在以 `test` 开头的方法名称中，可能会出现下划线，以分隔名称的逻辑组成部分。一种可能的模式是 `test<MethodUnderTest>_<state>`。


#### 3.16.3 File Naming 

Python 文件名必须具有 `.py` 扩展名，并且不得包含破折号（`-`）。这使得它们可以被导入和单元测试。如果您希望可执行文件在没有扩展名的情况下可访问，请使用符号链接或包含 `exec "\$0.py" "\$@"` 的简单 bash 包装器。


#### 3.16.4 Guidelines derived from [Guido](https://en.wikipedia.org/wiki/Guido_van_Rossum)'s Recommendations 

| Type                       | Public               | Internal                          |
| :------------------------- | :------------------- | :-------------------------------- |
| Packages                   | `lower_with_under`   |                                   |
| Modules                    | `lower_with_under`   | `_lower_with_under`               |
| Classes                    | `CapWords`           | `_CapWords`                       |
| Exceptions                 | `CapWords`           |                                   |
| Functions                  | `lower_with_under()` | `_lower_with_under()`             |
| Global/Class Constants     | `CAPS_WITH_UNDER`    | `_CAPS_WITH_UNDER`                |
| Global/Class Variables     | `lower_with_under`   | `_lower_with_under`               |
| Instance Variables         | `lower_with_under`   | `_lower_with_under` (protected)   |
| Method Names               | `lower_with_under()` | `_lower_with_under()` (protected) |
| Function/Method Parameters | `lower_with_under`   |                                   |
| Local Variables            | `lower_with_under`   |                                   |



#### 3.16.5 Mathematical Notation 

对于数学计算密集的代码，当短变量名与参考文献或算法中的既定符号相匹配时，尽管可能违反风格指南，仍然优先使用这些短变量名。在这样做时，应在注释或文档字符串中引用所有命名约定的来源，或者如果来源不可访问，则清晰地记录命名约定。对于公共API，优先使用符合PEP8标准的`descriptive_names`，因为它们在上下文之外被遇到的可能性更大。


### 3.17 Main 

在Python中，`pydoc`以及单元测试都要求模块能够被导入。如果一个文件旨在作为可执行文件使用，其主要功能应放在`main()`函数中，并且在执行主程序之前，您的代码应始终检查`if __name__ == '__main__'`，以确保在模块被导入时不会执行该程序。

在使用 [absl](https://github.com/abseil/abseil-py) 时，请使用 `app.run`：

```python
from absl import app
...

def main(argv: Sequence[str]):
    # process non-flag arguments
    ...

if __name__ == '__main__':
    app.run(main)
```

否则，请使用：

```python
def main():
    ...

if __name__ == '__main__':
    main()
```

所有顶层的代码将在模块被导入时执行。请注意，不要调用函数、创建对象或执行其他在文件被 `pydoc` 处理时不应执行的操作。


### 3.18 Function length 

偏好小而专注的函数。

我们认识到长函数有时是合适的，因此对函数长度没有严格限制。如果一个函数超过大约40行，请考虑是否可以在不损害程序结构的情况下将其拆分。

即使你的长函数现在运行得非常完美，几个月后有人对其进行修改时，可能会添加新的行为。这可能导致难以发现的错误。保持函数简短和简单使其他人更容易阅读和修改你的代码。

在处理某些代码时，您可能会遇到冗长而复杂的函数。不要因修改现有代码而感到畏惧：如果处理这样的函数变得困难，您发现错误难以调试，或者您希望在多个不同的上下文中使用其中的一部分，考虑将该函数拆分为更小且更易于管理的部分。


### 3.19 Type Annotations 

#### 3.19.1 General Rules 

-   请熟悉[PEP-484](https://peps.python.org/pep-0484/)。

-   对`self`或`cls`进行注解通常不是必要的。 [`Self`](https://docs.python.org/3/library/typing.html#typing.Self) 可以在需要提供正确的类型信息时使用，例如。

    ```python
    from typing import Self
    
    class BaseClass:
      @classmethod
      def create(cls) -> Self:
        ...
    
      def difference(self, other: Self) -> float:
        ...
    ```

-   同样，不必强迫自己对`__init__`的返回值进行注释（其中`None`是唯一有效的选项）。

-   如果其他任何变量或返回类型不应被表达，则使用`Any`。

-   您并不需要对模块中的所有函数进行注释。

    -   至少对你的公共API进行注释
    -   运用判断力在安全性与清晰性之间，以及灵活性之间取得良好的平衡
    -   注释容易出现类型相关错误的代码（之前的错误或复杂性）
    -   注释难以理解的代码
    -   从类型的角度对代码进行注释，随着代码的稳定性提高。在许多情况下，您可以对成熟代码中的所有函数进行注释，而不会失去太多灵活性


#### 3.19.2 Line Breaking 

请尽量遵循现有的[indentation](#indentation)规则。

在注释之后，许多函数签名将变为“每行一个参数”。为了确保返回类型也能单独占据一行，可以在最后一个参数后面加上一个逗号。

```python
def my_method(
    self,
    first_var: int,
    second_var: Foo,
    third_var: Bar | None,
) -> int:
  ...
```

始终优先在变量之间进行换行，而不是，例如，在变量名称和类型注释之间进行换行。然而，如果所有内容都能适应在同一行中，则可以这样做。

```python
def my_method(self, first_var: int) -> int:
  ...
```

如果函数名、最后一个参数和返回类型的组合过长，请在新的一行缩进4个空格。当使用换行时，建议将每个参数和返回类型放在各自的行上，并将闭合括号与`def`对齐：

```python
Yes:
def my_method(
    self,
    other_arg: MyLongType | None,
) -> tuple[MyLongType1, MyLongType1]:
  ...
```

可选地，返回类型可以与最后一个参数放在同一行：

```python
Okay:
def my_method(
    self,
    first_var: int,
    second_var: int) -> dict[OtherLongType, MyLongType]:
  ...
```

`pylint` 允许您将闭合括号移动到新的一行，并与开括号对齐，但这样会降低可读性。

```python
No:
def my_method(self,
              other_arg: MyLongType | None,
             ) -> dict[OtherLongType, MyLongType]:
  ...
```

如上面的例子所示，尽量避免断开类型。然而，有时它们的长度过长，无法在一行内显示（尽量保持子类型不被断开）。

```python
def my_method(
    self,
    first_var: tuple[list[MyLongType1],
                     list[MyLongType2]],
    second_var: list[dict[
        MyLongType3, MyLongType4]],
) -> None:
  ...
```

如果单个名称和类型过长，可以考虑为该类型使用一个[别名](#typing-aliases)。最后的手段是在冒号后换行并缩进4个空格。

```python
Yes:
def my_function(
    long_variable_name:
        long_module_name.LongTypeName,
) -> None:
  ...
```

```python
No:
def my_function(
    long_variable_name: long_module_name.
        LongTypeName,
) -> None:
  ...
```

#### 3.19.3 Forward Declarations 

如果您需要使用一个尚未定义的类名（来自同一模块）——例如，如果您需要在该类的声明中使用类名，或者如果您使用的是在代码中稍后定义的类——可以选择使用 `from __future__ import annotations`，或者使用字符串来表示类名。

```python
Yes:
from __future__ import annotations

class MyClass:
  def __init__(self, stack: Sequence[MyClass], item: OtherClass) -> None:

class OtherClass:
  ...
```

```python
Yes:
class MyClass:
  def __init__(self, stack: Sequence['MyClass'], item: 'OtherClass') -> None:

class OtherClass:
  ...
```


#### 3.19.4 Default Values 

根据[PEP-008](https://peps.python.org/pep-0008/#other-recommendations)，仅对同时具有类型注解和默认值的参数在`=`周围使用空格。

```python
Yes:
def func(a: int = 0) -> int:
  ...
```

```python
No:
def func(a:int=0) -> int:
  ...
```


#### 3.19.5 NoneType 

在Python类型系统中，`NoneType`是一个“第一类”类型，并且在类型声明中，`None`是`NoneType`的别名。如果一个参数可以是`None`，则必须进行声明！您可以使用`|`联合类型表达式（在新的Python 3.10+代码中推荐使用），或者使用较旧的`Optional`和`Union`语法。

使用显式的 `X | None` 而不是隐式的。早期版本的 PEP 484 允许将 `a: str = None` 解释为 `a: str | None = None`，但这不再是首选的行为。

```python
Yes:
def modern_or_union(a: str | int | None, b: str | None = None) -> str:
  ...
def union_optional(a: Union[str, int, None], b: Optional[str] = None) -> str:
  ...
```

```python
No:
def nullable_union(a: Union[None, str]) -> str:
  ...
def implicit_optional(a: str = None) -> str:
  ...
```


#### 3.19.6 Type Aliases 

您可以声明复杂类型的别名。别名的名称应采用大写字母开头的格式。如果该别名仅在此模块中使用，则应为\_Private。

请注意，`: TypeAlias` 注释仅在版本 3.10 及以上中受支持。

```python
from typing import TypeAlias

_LossAndGradient: TypeAlias = tuple[tf.Tensor, tf.Tensor]
ComplexTFMap: TypeAlias = Mapping[str, _LossAndGradient]
```

#### 3.19.7 Ignoring Types 

您可以通过特殊注释 `# type: ignore` 在某一行禁用类型检查。

`pytype` 提供了一个选项，可以针对特定错误进行禁用（类似于 lint）：

```python
# pytype: disable=attribute-error
```


#### 3.19.8 Typing Variables 

<a id="annotated-assignments"></a>
[*Annotated Assignments*](#annotated-assignments)
:  如果一个内部变量的类型难以或不可能推断，则通过带注释的赋值来指定其类型——在变量名和数值之间使用冒号和类型（与具有默认值的函数参数的处理方式相同）：

    ```python
    a: Foo = SomeUndecoratedFunction()
    ```

<a id="type-comments"></a>
[*Type Comments*](#type-comments)
:   尽管你可能会看到它们仍然存在于代码库中（在 Python 3.6 之前是必要的），但请不要在行末添加更多的 `# type: <type name>` 注释：

    ```python
    a = SomeUndecoratedFunction()  # type: Foo
    ```

#### 3.19.9 Tuples vs Lists 

类型列表只能包含单一类型的对象。类型元组可以具有单一重复的类型，或者具有一定数量的不同类型元素。后者通常用作函数的返回类型。

```python
a: list[int] = [1, 2, 3]
b: tuple[int, ...] = (1, 2, 3)
c: tuple[int, str, float] = (1, "2", 3.5)
```

#### 3.19.10 Type variables 

Python 的类型系统具有 [generics](https://peps.python.org/pep-0484/#generics)类型变量，例如 `TypeVar` 和 `ParamSpec`，是使用它们的常见方式。

例子：

```python
from collections.abc import Callable
from typing import ParamSpec, TypeVar
_P = ParamSpec("_P")
_T = TypeVar("_T")
...
def next(l: list[_T]) -> _T:
  return l.pop()

def print_when_called(f: Callable[_P, _T]) -> Callable[_P, _T]:
  def inner(*args: _P.args, **kwargs: _P.kwargs) -> _T:
    print("Function was called")
    return f(*args, **kwargs)
  return inner
```

一个 `TypeVar` 可以被约束：

```python
AddableType = TypeVar("AddableType", int, float, str)
def add(a: AddableType, b: AddableType) -> AddableType:
  return a + b
```

`typing` 模块中一个常见的预定义类型变量是 `AnyStr`。在多个注解中使用它，这些注解可以是 `bytes` 或 `str`，并且必须都是相同的类型。

```python
from typing import AnyStr
def check_length(x: AnyStr) -> AnyStr:
  if len(x) <= 42:
    return x
  raise ValueError()
```

类型变量必须具有描述性名称，除非它满足以下所有标准：

-   不对外可见
-   未受限制

```python
Yes:
  _T = TypeVar("_T")
  _P = ParamSpec("_P")
  AddableType = TypeVar("AddableType", int, float, str)
  AnyFunction = TypeVar("AnyFunction", bound=Callable)
```

```python
No:
  T = TypeVar("T")
  P = ParamSpec("P")
  _T = TypeVar("_T", int, float, str)
  _F = TypeVar("_F", bound=Callable)
```

#### 3.19.11 String types 

> 在新代码中请勿使用 `typing.Text`。它仅用于 Python 2/3 的兼容性。

对于字符串/文本数据，请使用 `str`。对于处理二进制数据的代码，请使用 `bytes`。

```python
def deals_with_text_data(x: str) -> str:
  ...
def deals_with_binary_data(x: bytes) -> bytes:
  ...
```

如果一个函数的所有字符串类型始终相同，例如如果返回类型与上述代码中的参数类型相同，则使用 [AnyStr](#typing-type-var)。


#### 3.19.12 Imports For Typing 

对于来自 `typing` 或 `collections.abc` 模块的符号（包括类型、函数和常量），用于支持静态分析和类型检查时，始终直接导入符号本身。这使得常见的注解更加简洁，并与全球范围内使用的类型注释实践相匹配。您被明确允许在一行中从 `typing` 和 `collections.abc` 模块导入多个特定符号。例如：

```python
from collections.abc import Mapping, Sequence
from typing import Any, Generic, cast, TYPE_CHECKING
```

鉴于这种导入方式会将项目添加到本地命名空间，因此`typing`或`collections.abc`中的名称应被视为类似于关键字，并且不应在您的Python代码中定义，无论是否有类型。如果类型与模块中现有名称发生冲突，请使用`import x as y`进行导入。

```python
from typing import Any as AnyType
```

在可用的情况下，优先使用内置类型作为注解。Python 通过 [PEP-585](https://peps.python.org/pep-0585/) 支持使用参数化容器类型的类型注解，该提案在 Python 3.9 中引入。

```python
def generate_foo_scores(foo: set[str]) -> list[float]:
  ...
```

#### 3.19.13 Conditional Imports 

仅在特殊情况下使用条件导入，即在运行时必须避免进行类型检查所需的额外导入时。这个模式是不被鼓励的；应优先考虑重构代码以允许顶层导入等替代方案。

仅在类型注解中需要的导入可以放置在`if TYPE_CHECKING:`块内。

-   条件导入的类型需要作为字符串进行引用，以便与 Python 3.6 向前兼容，因为在该版本中，注解表达式实际上会被评估
-   仅应在此处定义仅用于类型的实体；这包括别名。否则，将会出现运行时错误，因为该模块在运行时不会被导入
-   该代码块应紧接在所有正常导入之后
-   在输入的导入列表中不应有空行
-   将此列表按常规导入列表的方式进行排序
```python
import typing
if typing.TYPE_CHECKING:
  import sketch
def f(x: "sketch.Sketch"): ...
```

#### 3.19.14 Circular Dependencies 

由类型引起的循环依赖是代码异味。这类代码是重构的良好候选者。尽管从技术上讲，保持循环依赖是可能的，但各种构建系统将不允许这样做，因为每个模块必须依赖于另一个模块。

将创建循环依赖导入的模块替换为`Any`。设置一个具有意义的名称的[别名](#typing-aliases)，并使用该模块的真实类型名称（`Any`的任何属性都是`Any`）。别名定义应与最后一个导入之间隔开一行。

```python
from typing import Any

some_mod = Any  # some_mod.py imports this module.
...

def my_method(self, var: "some_mod.SomeType") -> None:
  ...
```

#### 3.19.15 Generics 

在注释时，建议为泛型类型指定类型参数；否则，[泛型的参数将被假定为 `Any`](https://peps.python.org/pep-0484/#the-any-type)。

```python
# Yes:
def get_names(employee_ids: Sequence[int]) -> Mapping[int, str]:
  ...
```

```python
# No:
# This is interpreted as get_names(employee_ids: Sequence[Any]) -> Mapping[Any, Any]
def get_names(employee_ids: Sequence) -> Mapping:
  ...
```

如果一个泛型的最佳类型参数是 `Any`，请明确指出，但请记住，在许多情况下 [`TypeVar`](#typing-type-var) 可能更为合适：

```python
# No:
def get_names(employee_ids: Sequence[Any]) -> Mapping[Any, str]:
  """Returns a mapping from employee ID to employee name for given IDs."""
```

```python
# Yes:
_T = TypeVar('_T')
def get_names(employee_ids: Sequence[_T]) -> Mapping[_T, str]:
  """Returns a mapping from employee ID to employee name for given IDs."""
```


## 4 Parting Words 

*保持一致性*。

如果你正在编辑代码，花几分钟时间查看你周围的代码并确定其风格。如果他们在索引变量名中使用 `_idx` 后缀，你也应该这样做。如果他们的注释周围有小方框的哈希标记，你的注释也应该有小方框的哈希标记。

制定风格指南的目的是为了拥有一个共同的编码词汇，使人们能够专注于你所表达的内容，而不是你表达的方式。我们在这里提出全球风格规则，以便人们了解这一词汇，但地方风格同样重要。如果你添加到文件中的代码与周围现有代码的风格差异过大，当读者去阅读时，会打乱他们的阅读节奏。

然而，一致性是有其局限性的。它在局部的应用更为显著，并且对于全球风格未指定的选择也更为适用。一般来说，不应将一致性作为以旧风格进行操作的理由，而不考虑新风格的优势，或代码库随着时间的推移趋向于更新风格的倾向。