---
categories:
  - Python
  - 教程
comments: true
date: 2018-12-26T09:07:56+08:00
image: /images/thumbs/h_41.jpg
tags: []
title: "如何用PEP 8编写优雅的Python代码"
toc: true
---

> 原文地址：[How to Write Beautiful Python Code With PEP 8](https://realpython.com/python-pep8/)
>
> 作者：[Jasmine Finer ](https://realpython.com/team/jfiner/)
>
> 翻译：[howie6879](https://github.com/howie6879)

`PEP 8`有时候读作`PEP8 `或者`PEP-8`，是一份提供如何编写Python代码指南和最佳实践的文档，由`Guido van Rossum, Barry Warsaw, Nick Coghlan`在2001年完成。`PEP 8`主要注重于提高 Python 代码的可读性和一致性。

`PEP`全称为：`Python Enhancement Proposal`，一个PEP是一份文档，它描述了为Python提出的新特性以及为社区编写的Python方面的文档，比如设计和风格。

本教程概述了`PEP 8`中列出的关键指南，它的目标读者是初级到中级程序员，因此我没有涉及一些最高级的主题。不过你可以通过阅读完整的[PEP 8 — Style Guide for Python Code | Python.org](https://www.python.org/dev/peps/pep-0008/)文档来学习高级主题。

在本教程结束时，你将能够：

- 编写遵从`PEP 8`规范的代码
- 理解`PEP 8`中列出的指导原则背后的原因
- 设置你的开发环境，以便开始编写符合`PEP 8`标准的Python代码

## Why We Need PEP 8
> 可读性很重要  

`PEP 8`规范存在的目的在于提高Python代码的可读性，但为什么可读性如此重要？为什么编写具有可读性的代码是Python语言的指导原则之一？

正如`Guido van Rossum`所说：”代码阅读的频率远远高于编写的频率“。你可能花费几分钟或一整天时间编写一段代码来处理用户身份验证，一旦你写完它，你就再也不会重写一次，但是你肯定会再读一次，这段代码可能仍然是你正在进行的项目的一部分。每次你回到那个代码文件，你必须要记住那部分代码是做什么的以及你为什么要写它，所以可读性很重要。

如果你是Python新手，在你编写代码之后的几天或几周内还要你记住代码片段的含义可能有点困难。如果你遵循`PEP 8`标准，你可以很确定你对你的变量命名得很好，你会知道你添加了足够的空格，因此更容易遵循代码中的逻辑步骤，你还可以对你的代码进行很好的注释，所有的这些都意味着你的代码具有更高的可读性，也更容易再次阅读。作为初学者，遵循`PEP 8`的规则可以使学习Python成为更愉快的任务。

如果你正在找工作，那么遵循`PEP 8`标准尤为重要，编写清晰可读性高的代码更会突出你的专业性，这将会侧面告诉你的老板，你了解怎么很好地构建你的代码。

如果你拥有很多编写Python代码的经验，然后你可能需要和其他人协作开发，编写可读性高的代码在这里至关重要，其他人可能此前从未看过你这样风格的代码，他必须重新阅读且理解你的代码风格，如果拥有你遵循和认可的指南将使其他人更容易阅读你的代码。

## Naming Conventions
> 明了胜于晦涩  

当你编写Python代码的时候，你必须对很多东西命名：变量、函数、类、包等等，选择合理的名字将为你节省时间和精力。你将能够从名称中找出某个变量，函数或类所代表的含义。你还要避免使用不恰当的名称，因为这样可能会导致难以调试的错误。

::Note: 永远不要使用 i，o，或 I 单字母名称，因为这些名称可能会被误认为1和0，具体取决于字体:::

```python
# 这可能看起来像你试图将2重新分配给零
0 = 2 
```

### Naming Styles
下表概述了Python代码中的一些常见命名风格以及何时应该使用它们：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/EA017D35-813C-4339-BC62-0DF6D2B7E5E6.png)

这些是一些常见的命名约定以及如何使用它们的示例，但是为了编写高可读性代码，你仍然需要谨慎选择字母和单词。除了在代码中选择正确的命名风格外，还必须仔细选择名称，以下是有关如何尽可能有效地执行此操作的一些指示。

### How to Choose Names
为变量、函数、类等选择名称是一项挑战，在编写代码时，你应该在命名选择中加入相当多的思考，因为这样可以使代码更具可读性。在Python中为对象命名的最佳方法是使用描述性名称来清楚表明对象所代表的内容。

对变量进行命名时，你可能会选择简单的单字母小写名称，例如x。但是，除非你使用x作为数学函数的参数，否则并不清楚x代表什么。想象下你正在将一个人的名字保存为字符串，并且你希望使用字符串切片来格式化其名称。你最终会得到这样的东西：

```python
# Not recommended
x = 'John Smith'
y, z = x.split()
print(z, y, sep=', ')
# 'Smith, John'
```

上面代码可以很好的运行，但是你必须追踪`x,y,z`表示的含义，这样也会让你的代码协作者产生困扰，更明确的名称选择将是这样的：

```python
# Recommended
name = 'John Smith'
first_name, last_name = name.split()
print(last_name, first_name, sep=', ')
# 'Smith, John'
```

同样，为了减少输入的数量，在选择名称时使用单词缩写也是很有诱惑力的，在下面的例子中，我定义了一个函数`db()`，它接受一个参数x并将其加倍返回：

```python
# Not recommended
def db(x):
    return x * 2
```

乍一看，这似乎是一个明智的选择。 `db()`很容易看成是`double`的缩写，但想象一下，几天后再看这段代码，你可能已经忘记了你试图通过这个函数实现的目标是什么，这将使猜测它原本的含义变得困难。

下面的例子就比较清晰，如果你几天后再看这段代码，你仍然可以阅读并理解此函数的目标：

```python
# Recommended
def multiply_by_two(x):
    return x * 2
```

同样的理念适用于Python中的所有其他数据类型和对象，尽量一直使用最简洁且最具描述性的名称。

## Code Layout
> 优美胜于丑陋  

如何对代码布局对于提升它的可读性有很大的作用。在本节中，你将学习如何添加垂直空格以提高代码的可读性。你还将学习如何处理`PEP 8`中建议的79字符行限制。

### Blank Lines
垂直空格或空行可以极大地提高代码的可读性，聚集在一起的代码可能是压倒性的且难以阅读，同样，代码中的空行太多会使其看起来非常稀疏，读者可能需要进行没必要的滚动，以下是关于如何使用垂直空白的三个关键指南。

**用两个空行包围顶级函数和类**，顶级函数和类应该是完全自包含的，并处理单独的功能，在它们周围放置额外的垂直空间是有意义的，因此很明显它们是分开的：

```python
class MyFirstClass:
    pass


class MySecondClass:
    pass


def top_level_function():
    return None
```

**用一个空行包围类中的方法定义**，在一个类中，函数都彼此有联系，最好只在它们之间留一行：

```python
class MyClass:
    def first_method(self):
        return None

    def second_method(self):
        return None
```

**在函数内部谨慎地使用空行，以显示清晰的步骤**，有时候一个复杂的函数必须在return语句之前完成好几个步骤，为了帮助阅读者理解函数里面的逻辑，在每个步骤之间留一个空行会很有帮助。

在下面的示例中，有一个计算列表方差的函数，这个问题可以分为两个步骤，所以我在每个步骤之间留下了一个空行，在return语句之前还有一个空行。这有助于读者清楚地看到返回的内容：

```python
def calculate_variance(number_list):
    sum_list = 0
    for number in number_list:
        sum_list = sum_list + number
    mean = sum_list / len(number_list)

    sum_squares = 0
    for number in number_list:
        sum_squares = sum_squares + number**2
    mean_squares = sum_squares / len(number_list)

    return mean_squares - mean**2
```

如果仔细地使用垂直空格，可以大大提高代码的可读性，它有助于读者直观地理解你的代码如何分成几个部分，以及这些部分如何相互关联。

### Maximum Line Length and Line Breaking
`PEP 8`建议每行代码限制在79个字符以内，这是因为它允许多个文件并排打开，同时也避免了换行，当然，将语句保持在79个字符以内并不总是可行的。`PEP 8`概述了允许语句在多行上运行的方法。

如果代码包含在括号、方括号或大括号中，Python将会认为代码是一行的：

```python
def function(arg_one, arg_two,
             arg_three, arg_four):
    return arg_one
```

如果不能用上述的方式进行每行代码延续，那么可以使用反斜杠代替换行：

```python
from mypkg import example1, \
    example2, example3
```

但是，如果可以使用第一个方案，那么就应该尽量这样做。

如果需要在二元运算符周围做换行操作，例如`+`和`*`，那么需要将换行操作放在前面，这条规则源于数学，数学家同意在二元运算符之前换行以可提高可读性，比较以下两个例子。

下面是在二元运算符之前换行的示例：

```python
# Recommended
total = (first_variable
         + second_variable
         - third_variable)
```

你可以马上反应过来哪些变量正在相加后者相减，因为操作符紧邻着变量。

现在，让我们看一个在二元运算符之后换行的示例：

```python
# Not Recommended
total = (first_variable +
         second_variable -
         third_variable)
```

在这里，很难看出哪个变量被加，哪个变量被减。

在二元运算符之前换行可以让代码更加具有可读性，所`PEP 8`鼓励这种方式。在二元运算符之后换行的代码仍然符合`PEP 8`，但是，我们鼓励你在二元运算符之前换行。

## Indentation
> 当存在多种可能，不要尝试去猜测  
> 而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法）  

缩进，即前导空格，在Python中非常重要。Python中代码行的缩进级别决定了语句如何组合在一起。

思考下面的例子：

```python
x = 3
if x > 5:
    print('x is larger than 5')
```

缩进的print语句使得Python明白只有if语句返回True才能继续执行它，同样的缩进适用于告诉 Python 在调用函数时要执行哪些代码，或者哪些代码属于给定的类。

`PEP 8`制定的关键缩进规则如下：

- 使用4个连续的空格来表示缩进。
- 选择空格而不是制表符(Tab)。

### Tabs vs. Spaces
如同上述所说，当你进行缩进的时候，你最好用空格代替制表符，你可以直接对文本编辑器的设置进行调整，当你按`Tab`的时候转换为4个空格的输出

如果你使用的是Python 2并且混合使用了制表符和空格来缩进代码，当你运行的时候可能不会看见错误。为了帮助你检查一致性，可以在从命令行运行Python 2代码时添加`-t`标志。当你不一致地使用制表符和空格时，解释器将会发出警告：

```shell
python2 -t code.py
code.py: inconsistent use of tabs and spaces in indentation
```

相反，如果你使用`-tt`标志，解释器将发出错误而不是警告，使用此方法的好处是解释器会告诉你缩进不一致的位置在哪里：

```shell
python2 -tt code.py
File "code.py", line 3
    print(i, j)
             ^
TabError: inconsistent use of tabs and spaces in indentation
```

Python 3不允许制表符和空格混合使用，因此，如果你使用的是Python3，解释器将会自动报错：

```python
python3 code.py
File "code.py", line 3
    print(i, j)
              ^
TabError: inconsistent use of tabs and spaces in indentation
```

你可以在编写Python代码中使用制表符或空格来表示缩进，但是，如果你使用的是Python 3，则必须与你的选择保持一致，否则你的代码将不能运行。`PEP 8`推荐你总是使用4个连续的空格来表示缩进。

### Indentation Following Line Breaks

当你为了保证每行在79个字符以下而进行换行时，使用缩进来提高代码可读性是很有用的，它允许读者区分两行代码和跨越两行的单行代码，这里有两种缩进风格供你选择。

第一个是将缩进块与开始的分隔符对齐:

```python
def function(arg_one, arg_two,
             arg_three, arg_four):
    return arg_one
```

有时你会发现只需要4个空格就可以与开口分隔符对齐，这种情况经常发生在 if 语句中，如果跨多行的`if+ +(`构成4个字符，那么就会发生这种情况，在这种情况下，很难确定 if 语句中嵌套的代码块从哪里开始:

```python
x = 5
if (x > 3 and
    x < 10):
    print(x)
```

在这种情况下，`PEP 8`提供了两种替代方案来帮助你提高代码可读性：

- 在最后的条件后添加注释，由于大多数编辑器中有语法高亮显示，这可以将条件与嵌套代码分开：

```python
x = 5
if (x > 3 and
    x < 10):
    # Both conditions satisfied
    print(x)
```

- 在换行中添加额外的缩进：

```python
x = 5
if (x > 3 and
        x < 10):
    print(x)
```

换行符后的另一种缩进方式是**悬挂缩进(hanging indent)**，这是一个印刷术语，意思是段落或语句中除第一行以外的每一行都缩进，可以使用悬挂缩进直观地表示一行代码的延续。下面是一个例子:

```python
var = function(
    arg_one, arg_two,
    arg_three, arg_four)
```

::Note：当你正在使用hanging indent时，第一行不得有任何参数。以下示例不符合PEP 8::

```python
# Not Recommended
var = function(arg_one, arg_two,
    arg_three, arg_four)
```

使用**hanging indent**时，可以添加额外的缩进以区分连续行与函数内包含的代码。下面是一个难以阅读的例子，因为函数里面的代码和连续行的缩进等级是相同的：

```python
# Not Recommended
def function(
    arg_one, arg_two,
    arg_three, arg_four):
    return arg_one
```

相反，最好在一行代码换行后使用双缩进，这有助于你区分函数参数和函数内部构造，从而提高可读性：

```python
def function(
        arg_one, arg_two,
        arg_three, arg_four):
    return arg_one
```

当你编写遵循`PEP 8`规范代码的时候，每行字符在79之内的规则使你必须强制换行，为了提高可读性，你应该缩进一行，以表明它是一个连续的行。做到这一点有两种方法，第一种是将缩进的块与开始的分隔符对齐，第二种是使用悬挂缩进，在换行之后，你可以自由选择使用哪种方法进行缩进。

### Where to Put the Closing Brace
一行代码的换行操作允许你在括号、方括号或大括号后面断开一行，这会造成一个问题，就是很容易忘记关闭括号，但重要的是要把它放在合理的地方，否则，它会使读者感到困惑，`Pep 8`为隐含的行延续中的结束括号的位置提供了两个选项:

- 使用前一行的第一个非空白字符排列右括号：

```python
list_of_numbers = [
    1, 2, 3,
    4, 5, 6,
    7, 8, 9
    ]
```

- 将结束括号与声明行的第一个字符对齐:

```python
list_of_numbers = [
    1, 2, 3,
    4, 5, 6,
    7, 8, 9
]
```

你可以自由地选择任何一种方式，但是，和之前说的一样，一致性是关键，所以之后的代码就要坚持使用你选择的某一个方案以保持代码一致性。

## Comments
> 如果你无法向人描述你的方案，那肯定不是一个好方案；反之亦然  

你应该使用注释来说明编写的代码，这对记录你的代码非常重要，这样你的任何协作者都可以理解它。当你或者其他人阅读代码注释，他们应该能够很容易地理解注释所应用的代码，以及它如何与代码的其余部分相匹配。

下面是一些你为你代码作注释时候需要注意的关键点：

- 将注释和文档字符串的行长限制为72个字符
- 使用完整的句子，以大写字母开头
- 注释随着代码的更新而更新

### Block Comments
使用块注释来记录一小部分代码，当你必须编写几行代码来执行单个操作时，它们非常有用，例如从文件导入数据或更新数据库记录。其重要性在于它们可以帮助其他人理解给定代码块的用途和功能。

`PEP 8`提供以下规则来编写注释：

- 将块注释缩进到其描述代码的相同级别
- 用#加上一个空格开始每一行的注释
- 用包含单个＃的行分隔段落

这是一个解释for循环功能的块注释，请注意，句子为了每行保留79个字符行限制进行了换行：

```python
for i in range(0, 10):
    # Loop over i ten times and print out the value of i, followed by a
    # new line character
    print(i, '\n')
```

有时，如果代码技术性较强，那么有必要在块注释中使用多个段落：

```python
def quadratic(a, b, c, x):
    # Calculate the solution to a quadratic equation using the quadratic
    # formula.
    #
    # There are always two solutions to a quadratic equation, x_1 and x_2.
    x_1 = (- b+(b**2-4*a*c)**(1/2)) / (2*a)
    x_2 = (- b-(b**2-4*a*c)**(1/2)) / (2*a)
    return x_1, x_2
```

如果你不确定什么样的注释类型是合适的，那么块注释通常是正确的选择。请尽量在你的代码中添加注释，一旦你对代码进行了更新也请务必对注释进行更新！

### Inline Comments
行内注释对单个语句中的一段代码进行了解释，这有助于提醒你或者其他人为什么某行代码是必须的，以下是 `PEP 8`对他们的评价:

- 谨慎使用行内注释
- 在与它们引用的语句相同的行上写入行内注释
- 从语句中用两个或多个空格分隔行内注释
- 像块注释那样，用#加上一个空格开始每一行的注释
- 不要用它们来解释明显的问题

下面是一个行内注释的例子：

```python
x = 5  # This is an inline comment
```

有时，行内注释似乎是必要的，但是你可以使用更好的命名约定，下面是一个例子:

```python
x = 'John Smith'  # Student Name
```

这里，行内注释为变量提供了额外的说明信息，然而用`x`作为一个人名的变量名不是很好的做法，如果你对变量重命名，是可以不用使用行内注释的：

```python
student_name = 'John Smith'
```

最后，像这样的行内注释是不好的做法，因为它们陈述了显而易见且杂乱的代码：

```python
empty_list = []  # Initialize empty list

x = 5
x = x * 5  # Multiply x by 5
```

行内注释比块注释更具体，并且容易在非必要时候添加它们，这会导致代码混乱。除非你确定需要行内注释，不然使用块注释避免代码混乱的问题比较好，坚持使用块注释，可以让你的代码更加符合`PEP8`的标准。

### Documentation Strings
`Documentation strings` 或者说`docstrings`， 是用双引号（”””）或单引号（’’’）括起来的字符串，它们出现在任何函数，类，方法或模块的第一行，你可以使用它们来解释和记录特定的代码块。这里有一个对应的`PEP`，见[PEP 257](https://www.python.org/dev/peps/pep-0257/)，但你可以从本节阅读总结的经验：

使用文档字符串的重要规则如下：

- 文档字符串两边都有三个双引号环绕，比如：`"""This is a docstring"""`
- 为所有公共模块，函数，类和方法编写`docstrings`
- 使用单行`"""`结束多行`docstring`

```python
def quadratic(a, b, c, x):
    """Solve quadratic equation via the quadratic formula.

    A quadratic equation has the following form:
    ax**2 + bx + c = 0

    There always two solutions to a quadratic equation: x_1 & x_2.
    """
    x_1 = (- b+(b**2-4*a*c)**(1/2)) / (2*a)
    x_2 = (- b-(b**2-4*a*c)**(1/2)) / (2*a)

    return x_1, x_2
```

- 对于单行`docstrings`，保持`"""`在同一行：

```python
def quadratic(a, b, c, x):
    """Use the quadratic formula"""
    x_1 = (- b+(b**2-4*a*c)**(1/2)) / (2*a)
    x_2 = (- b-(b**2-4*a*c)**(1/2)) / (2*a)

    return x_1, x_2
```

想要了解更多，请阅读[Documenting Python Code: A Complete Guide – Real Python](https://realpython.com/documenting-python-code/#docstrings-background)

## Whitespace in Expressions and Statements
> 间隔胜于紧凑  

正确使用空格在表达式和语句中非常有用，如果代码中没有足够的空格，那么代码应该很难阅读，因为他们都凑在一起，如果空格太多，又会难以在视觉上将完整的语句联系出来。

### Whitespace Around Binary Operators
在进行以下二元操作的时候，应该在其两边加上空格：

- 分配操作（=, +=, -=, 等等）
- 比较（==, !=, >, <. >=, <=）和 （is, is not, in, not in）
- 布尔（and, not, or）

::Note：当`=`是为一个函数的参数赋值时候就不用在两边加空格::

```python
# Recommended
def function(default_parameter=5):
    # ...


# Not recommended
def function(default_parameter = 5):
    # ...
```

如果语句中有多个运算符，则在每个运算符前后添加单个空格可能会让人感到困惑，相反，最好只在优先级最低的运算符周围添加空格，尤其是在执行数学运算时。以下是几个例子：

```python
# Recommended
y = x**2 + 5
z = (x+y) * (x-y)

# Not Recommended
y = x ** 2 + 5
z = (x + y) * (x - y)
```

还可以将此应用于有多个条件的if语句：

```python
# Not recommended
if x > 5 and x % 2 == 0:
    print('x is larger than 5 and divisible by 2!')
```

在上面的示例中，`and`操作具有最低优先级，因此，可以下面这样更清楚地表达if语句：

```python
# Recommended
if x>5 and x%2==0:
    print('x is larger than 5 and divisible by 2!')
```

你可以自由选择哪个方式更清晰，但需要注意的是你必须在运算符的任何一侧使用相同数量的空格。

下面的例子是不被允许的：

```python
# Definitely do not do this!
if x >5 and x% 2== 0:
    print('x is larger than 5 and divisible by 2!')
```

在切片中，冒号作为二元运算符。因此，上一节中概述的规则也适用于此，并且其任何一方都应该有相同数量的空白，以下列表切片示例是合法的：

```python
list[3:4]

# Treat the colon as the operator with lowest priority
list[x+1 : x+2]

# In an extended slice, both colons must be
# surrounded by the same amount of whitespace
list[3:4:5]
list[x+1 : x+2 : x+3]

# The space is omitted if a slice parameter is omitted
list[x+1 : x+2 :]
```

总之，你应该使用空格包围大多数运算符，但是，此规则有一些注意事项，例如在函数参数中或在一个语句中组合多个运算符时。

### When to Avoid Adding Whitespace

在某些情况下，添加空格可能会使代码难以阅读，太多的空格会使代码过于稀疏而难以理解。 `PEP 8`概述了一些非常明显的例子应当不使用空格

在一行的末尾避免添加空格是最重要的地方，这称为`trailing whitespace`，它是不可见的，可能产生难以追踪的错误。

以下列表概述了一些应避免添加空格的情况：

- 直接放在括号、方括号或大括号内:

```python
# Recommended
my_list = [1, 2, 3]

# Not recommended
my_list = [ 1, 2, 3, ]
```

- 在逗号，分号或冒号之前：

```python
x = 5
y = 6

# Recommended
print(x, y)

# Not recommended
print(x , y)
```

- 在打开函数调用的参数列表的开括号之前：

```python
def double(x):
    return x * 2

# Recommended
double(3)

# Not recommended
double (3)
```

- 在开始索引或切片的开括号之前:

```python
# Recommended
list[3]

# Not recommended
list [3]
```

- 在尾随逗号和右括号之间：

```python
# Recommended
tuple = (1,)

# Not recommended
tuple = (1, )
```

- 对齐赋值运算符：

```python
# Recommended
var1 = 5
var2 = 6
some_long_var = 7

# Not recommended
var1          = 5
var2          = 6
some_long_var = 7
```

确保你的代码中没有尾随空格。 在其他情况下，`PEP 8`不鼓励添加额外的空格，例如立直接放在括号、方括号或大括号内，以及逗号和冒号之前。 为了对齐操作符，也不应该添加额外的空格。

## Programming Recommendations

> 简洁胜于复杂  

你经常会发现有几种方法可以在 Python (以及任何其他编程语言)中执行类似的操作。在本节中，你将看到`PEP 8`提供的一些建议用来消除这种歧义并且保持一致性。

**不要使用等价运算符将布尔值与True或False进行比较**，你经常需要检查布尔值是True还是False，当这样操作时，请使用如下所示的语句直观地执行此操作：

```python
# Not recommended
my_bool = 6 > 5
if my_bool == True:
    return '6 is bigger than 5'
```

这里不需要使用等价运算符`==`， bool只能取值True或False，按照下面这样写就行了：

```python
# Recommended
if my_bool:
    return '6 is bigger than 5'
```

这种在if语句中使用bool的方式可以写更少的代码并且更加简单，因此`PEP 8`鼓励使用这种方式。

**在if语句中判断列表是否为空**，当你希望确定列表是否为空时，你通常会判断列表的长度，如果列表为空，那么长度为0，在if语句中使用时等于False，这里有一个例子：

```python
# Not recommended
my_list = []
if not len(my_list):
    print('List is empty!')
```

但是，在Python中，任何空列表，字符串或元组都可以当为False的。因此，我们可以提出一个更简单的替代方案：

```python
# Recommended
my_list = []
if not my_list:
    print('List is empty!')
```

两种方法都可以判断列表是否为空，但是第二种方式更加简洁，因此`PEP 8`鼓励使用这种方式。

**在if语句中，使用 is not 比 no ... is in 好**，当你需要确定一个变量是否被赋值，这里有两种方法，第一种是评估if语句中的x是不是为非`None`，如下所示：

```python
# Recommended
if x is not None:
    return 'x exists!'
```

第二种方案是现在if语句中判断x是不是`None`，再进行`not`操作：

```python
# Not recommended
if not x is None:
    return 'x exists!'
```

两种方法都可以判断x是否为`None`，但是第一种方式更加简单，因此`PEP 8`鼓励使用这种方式。

**当你想表达if x is not None时，不要使用if x**，有时候，你可能拥有一个函数带有默认值为`None`的参数，当对参数`arg`检查是否带有不同的值的时候经常会犯下面这样的错误：

```python
# Not Recommended
if arg:
    # Do something with arg...
```

此代码检查的是`arg`是否为真，但相对的你要的是检查是否为`None`，所以下面这样写比较好：

```python
# Recommended
if arg is not None:
    # Do something with arg...
```

上面的错误会使得判断是否为真和`not None`相等，你可以设置`arg = []`，如上所述，空列表在Python中被认为是`False`，因此，尽管参数被声明为`[]`，但条件并没有满足，因此函数里面的代码`if`声明并不能被执行。

**用.startswith() 和.endswith()代替切片**，如果你想检查一段字符串的前缀或者后缀是否带有字符串`cat`，这看起来使用列表切片似乎比较明智，但是，列表切片容易出错，您必须对前缀或后缀中的字符数进行硬编码，对于那些不太熟悉Python列表切片的人来说很难看清楚你想要实现的目的：

```python
# Not recommended
if word[:3] == 'cat':
    print('The word starts with "cat"')
```

然而，还是使用`.startswith():`可读性比较高：

```python
# Recommended
if word.startswith('cat'):
    print('The word starts with "cat"')
```

同样的，当你想检查字符串后缀也是同样的原理，下面的示例概述了如何检查字符串是否以jpg结尾：

```python
# Not recommended
if file_name[-3:] == 'jpg':
    print('The file is a JPEG')
```

虽然结果是正确的，但是符号有点复杂且难以阅读，与之相对的，你可以用`.endswith()`实现，看下面例子：

```python
# Recommended
if file_name.endswith('jpg'):
    print('The file is a JPEG')
```

就像上面的列出的编程建议，我们目标是编写清晰以及可读性较高的代码，在Python中，有许多不同的方法可以实现相同的结果，因此有关选择哪种方法的指南很有帮助。

## When to Ignore PEP 8

> 什么时候忽略PEP 8？对于这个问题有个简短的答案，那就是永远不会  

如果你严格遵循 PEP 8，就可以保证您拥有干净、专业和可读的代码。 这对你以及合作者和潜在雇主都有好处。

但是，PEP 8中的一些指导方针在以下实例中不适用：

- 如果遵守PEP 8将破坏与现有软件的兼容性
- 如果你正在从事的工作的代码与 PEP 8不一致
- 如果代码需要与旧版本的Python版本保持兼容

## Tips and Tricks to Help Ensure Your Code Follows PEP 8

想要你的代码兼容`PEP 8`，那你得记住不少规范，在开发代码时，记住所有这些规则可能是一项艰巨的任务。特别是对老项目进行更新以兼容`PEP 8`时，幸运的是，有些工具可以帮助你加快这一过程，这里有两个工具可以帮你强制兼容`PEP 8`规范：`linters`和`autoformatters`。

### Linters

Linters是分析代码和标记错误的程序，他们可以为你如何修复错误提供建议，在作为文本编辑器的扩展安装时，Linters特别有用，因为它们标记了你编程时的错误和格式问题。在本节中，您将看到Linters程序是如何工作的大纲，最后还有文本编辑器扩展的链接。

对于Python代码，最好的linters如下：

- [pycodestyle]([pycodestyle · PyPI](https://pypi.org/project/pycodestyle/))是一个根据`PEP 8`中的某些风格约定来检查Python代码的工具

```shell
# 使用pip安装pycodestyle
pip install pycodestyle
```

使用以下命令在终端中使用`pycodestyle`：

```shell
pycodestyle code.py
code.py:1:17: E231 missing whitespace after ','
code.py:2:21: E231 missing whitespace after ','
code.py:6:19: E711 comparison to None should be 'if cond is None:'
```

- [flake8](flake8)是一个结合了调试器，`pyflakes`和`pycodestyle`的工具

```shell
# 使用pip安装flake8
pip install flake8
```

使用以下命令在终端中使用`pycodestyle`：

```shell
flake8 code.py
code.py:1:17: E231 missing whitespace after ','
code.py:2:21: E231 missing whitespace after ','
code.py:3:17: E999 SyntaxError: invalid syntax
code.py:6:19: E711 comparison to None should be 'if cond is None:'
```

还显示了输出的示例。

::Note：额外输出的一行表示语法错误。::

这些也可用作Atom，Sublime Text，Visual Studio Code和VIM的扩展。您还可以找到有关为Python开发设置Sublime Text和VIM的指南，以及Real Python中一些流行的文本编辑器的概述。

### Autoformatters

`Autoformatters`可以重构你的代码以使其符合`PEP 8`规范，像[black](black)程序就可以根据`PEP 8`规范重新格式化你的代码，一个很大的区别是它将行长度限制为88个字符，而不是79个字符。但是，您可以通过添加命令行标志来覆盖它，如下面的示例所示。

可以使用pip安装black，它需要`Python 3.6+`：

```python
pip install black
```

它可以通过命令行运行，就像Linters一样。假设你从名为code.py里面不符合PEP 8规范的代码开始：

```python
for i in range(0,3):
    for j in range(0,3):
        if (i==2):
            print(i,j)
```

你可以在命令行中运行如下代码：

```shell
black code.py
reformatted code.py
All done!
```

`code.py`将会被重新格式化为如下所示：

```python
for i in range(0, 3):
    for j in range(0, 3):
        if i == 2:
            print(i, j)
```

如果你想要更改行长度限制，则可以使用`—line-length`标志：

```shell
black --line-length=79 code.py
reformatted code.py
All done!
```

另外两个`autoformatters `分别为`autopep8`和`yapf`，它们和`black`操作类似。

另一个`Real Python`教程，由` Alexander van Tol`编写的[Python Code Quality: Tools & Best Practices](https://realpython.com/python-code-quality/)详细解释了如何使用这些工具。

## Conclusion

你现在已经知道如何使用`PEP 8`中的指南编写高质量，可读高的Python代码，虽然指南看起来很迂腐，但遵循它们可以真正改善你的代码，特别是在与潜在雇主或合作者共享代码时。

在本教程中你了解到：

- 什么是` PEP 8`以及其存在的原因
- 为什么你应该编写符合`PEP 8`标准的代码
- 如何编写符合`PEP 8`的代码

除此之外，您还了解了如何使用linters和autoformatters根据`PEP 8`指南检查代码。

如果你想了解更多，可以阅读[full documentation]([PEP 8 — Style Guide for Python Code | Python.org](https://www.python.org/dev/peps/pep-0008/))或者浏览[pep8.org](https://pep8.org/)，这些教程不仅提供了类似的信息同时也提供了很好的格式让你阅读，在这些文档中，您将找到本教程未涉及的其余`PEP 8`指南。

![wechat_howie](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg)
