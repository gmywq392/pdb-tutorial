# `pdb` 入门教程

这篇文章将教会你怎样使用 `pdb`, 该库是 python 的 debugger 工具库，适用于 python2 和 python3。文章中也会提供一些小技巧有助于熟悉该库的使用。

--- 

#### 其他语言

这篇教程的原文是英语的，同时社区中也提供了其他语言：

- [英语](https://github.com/spiside/pdb-tutorial)
- [韩语](https://github.com/mingrammer/pdb-tutorial)

如果你想了解其他语言的教程或者有兴趣帮助建设其他语言的教程，请随意向[社区](https://github.com/spiside/pdb-tutorial/issues/)提出。

---

这篇教程适合的体验版本是 Python 2.7 或者 Python 3.4，这里也会高亮出两者的差异，可以用以下命令展示当前 python 版本：

```shell
python --version
```

确定版本合适后就开始实践吧！

## debugger 的目的是什么？

在深入代码前，我们将做一个关于 debugging 的简短概论。对我而言，以下三点是不得忽视的：

debugger 功能提供了：
* 了解程序在运行时的状态
* 正式交付前测试编写的代码
* 跟踪程序的执行链路

通过使用 debugger，你可以通过在程序任意位置设置断点来实践以上三点。相比较单纯的依赖 `print()` 来 debug 程序，通过 debugger 会更高效便捷。

对程序员老手来说，可能会有和我一样的感觉，那些最好的程序员一般都是拥有高效的 debug 能力的人。这里高效的 debug，指的是能够精确的定位问题，然后通过最低成本来解决。
通过学习和掌握 debugger 技能会使你成为更高效的程序员。当然，在你能娴熟应用前还需要点时间，这也就是这篇教程的作用！ 

## 启程

既然我们已经探讨了 debugger 的目的，现在是时候来实践一下了。首先如果还没有该项目的话需要克隆该项目到本地，如果你还没有安装 `git`，我推荐你使用（或者其他的源代码版本控制软件），可以再[这里](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)找到关于 `git` 的更多资料。
在安装好 `git` 之后，可以通过以下命令来克隆项目：
```shell
git clone https://github.com/spiside/pdb-tutorial
```

**注意**：如果这条命令无效，请查看[这里](https://help.github.com/articles/cloning-a-repository/)
成功克隆了项目之后，让我们将目录切换到项目内：
```shell
cd /path/to/pdb-tutorial
```

`file: instructions.txt`
```
你的上级交给你一个任务，让你去修复一个游戏客户端的 bug，游戏设计的很简单，就是连续投掷 6 次骰子，然后将其结果累计起来。
只有有一个程序员在解决这个 bug，但不知道如何解决，如何有效的 debug。
现在！轮到你来解决这个问题，让这个游戏再次发光吧！

通过 main.py 来启动这个游戏。
```

是不是很简单！让我们先来看看遇到了什么样的麻烦。通过下面的命令来启动：

```shell
python main.py
``` 

你看到的内容应该如下：

```
就像生活中的那样，
把这些骰子的点数加起来就行、

回合 1

---------
|*      |
|       |
|      *|
---------
---------
|*      |
|       |
|      *|
---------
---------
|*     *|
|       |
|*     *|
---------
---------
|*     *|
|       |
|*     *|
---------
---------
|*     *|
|   *   |
|*     *|
---------
那么，猜猜是多少？：
```

看上去之前的这位开发者有点幽默？既然如此，让我们输入17试试。


```
那么，猜猜是多少？：17
噢！很抱歉
答案是：5
认真的吗。这你都能算错
胜：0 负： 1
还想接着玩吗？[Y（是）/N（否）] 
```

奇怪了。居然说答案是5……好吧，可能是骰子的加法出了问题，让我们重新玩一次把他的问题找出来吧。看上去输入 `'Y'`就可以了，来试试看！

```
还想接着玩吗？[Y（是）/N（否）]: Y
Traceback (most recent call last):
  File "main.py", line 12, in <module>
    main()
  File "main.py", line 8, in main
    GameRunner.run()
  File "/Users/Development/pdb-tutorial/dicegame/runner.py", line 62, in run
    i_just_throw_an_exception()
  File "/Users/Development/pdb-tutorial/dicegame/utils.py", line 13, in i_just_throw_an_exception
    raise UnnecessaryError("You actually called this function...")
dicegame.utils.UnnecessaryError: You actually called this function...
```

诡异，我们输入的明明是一个合法的回答，它居然抛出了异常。我可以认为这个程序是有问题的了。让我们开始 debugging 吧！


## PDB 第一课： `pdb` 介绍

是时候了解一下 python 自己的 debugger `pdb`了。他内嵌在 python 标准库中，使用时可以像其他库一样。首先，我们将库引入程序中，然后可以调用他的方法来在程序中添加一个断点。
比较方便的测试方式是在 import 的同一行**加上**断点来停止程序。例如这样：
```python3
import pdb; pdb.set_trace()
```

方法[`set_trace()`](https://docs.python.org/3/library/pdb.html#pdb.set_trace) 通过硬编码的方式在调用行加入一个断点。
让我试着打开 `main.py` 在第 8 行添加一个断点，代码如下：

`file: main.py` 
```python3
from dicegame.runner import GameRunner


def main():
    print("Add the values of the dice")
    print("It's really that easy")
    print("What are you doing with your life.")
    import pdb; pdb.set_trace() # add pdb here
    GameRunner.run()


if __name__ == "__main__":
    main()
```

酷，想在让我们开始再执行一次，看看会有什么效果。

```shell
python main.py
```
```
Add the values of the dice
It's really that easy
What are you doing with your life.
> /Users/Development/pdb-tutorial/main.py(9)main()
-> GameRunner.run()
(Pdb) 
```

果然！我们现在停在了程序的某一步，并且可以前后翻找，对于刚刚的那个问题，我认为应该从累加的动作上入手。

如果你对 Python 的解释器比较熟悉，那么有很多其相关的知识都可以被活用到 `pdb` debugger 上。然而，接下来还是会有很多诡异的地方。也不用担心，让我们现在先来学一些有帮助的命令。


## 让你惊讶的 5 条 `pdb` 命令
Taken directly from the `pdb` documentation, these are the five commands that, once you learn them, you won't know how you lived
without them.
直接从 `pdb` 的文档看，有 5 条命令，一旦你掌握了，将会成为你的得力助手。 


1. `l(ist)` - 显示本行的前后 11 行代码或者持续打印之前的代码
2. `s(tep)` - 执行本行代码，并且停在合适的位置
3. `n(ext)` - 一直执行下去直到当前方法结束或者返回
4. `b(reak)` - 设置一个断点（取决于提供参数）
5. `r(eturn)` - 一直执行下去直到当前方法返回

Notice that there are brackets around the last part of every keyword. The brackets indicate that the rest of the word is _optional_ when
using the command prompt for `pdb`. This saves typing but a major gotcha is if you have a variable name such as `l` or `n`, then the
`pdb` command takes precedence. That is, say you have a variable named `c` in your program and you want to know the value of `c`. Well,
if you type `c` in `pdb`, you will actually be issuing the `c(ontinue)` keyword which executes the program and only stops if it encounters
a break point!
注意那些关键字的括号内容。括号里文字表示关键字的剩余部分，在 `pdb` 中也是具有相同效用的表达方式。当然在精简了表达方式的同时，也会和你定义的变量冲突，比如 `l` 或者 `n`，
这时候 `pdb` 命令会被视为优先。就好比说，你在程序里定义了一个变量名叫 `c`，如果想获得这个变量的值，

**NB**: I, and many other programmers, discourage the use of short variable names such as `a`, `b`, `gme`, etc. These carry no meaning
and will confuse other people reading your code. I'm only demonstrating the issues you may encounter with `pdb` in the presence of
shortened variable names.

**NNB**: Another helpful tool is the following:
`h(elp) - Without argument, print the list of available commands. With a command as an argument, print help about that command.`

For the rest of the tutorial, I will be using the shortened version of the commands and if I use a command that I have not introduced
here, I will explain what it does. So, let's begin with the first one.

### 1. l(ist) a.k.a. I'm too lazy to open the file containing the source code

```
l(ist) [first [,last]]
    List source code for the current file. Without arguments, list 11 lines around the current line
    or continue the previous listing. With one argument, list 11 lines starting at that line.
    With two arguments, list the given range; if the second argument is less than the first, it is a count.
```

**NB**: The above description was generated by calling `help` on `list`. To get the same output, in the `pdb` REPL type `help l`.

Using `list`, we can examine the source code of the current file we are in. The arguments for `list` lets you specify a given range
of lines you wish to see which can be helpful if you are in some weird 3rd party package and you are trying to figure out why they
can't get string encoding working _true story_.

**NB**: In Python 3.2 and above, you can type `ll` (long list) which shows you source code for the current function or frame. I use
this all the time instead of `l` since it's much better knowing which function you are in than an arbitrary 11 lines around your
current position.

Let's try using `l` now. In your already open `pdb` prompt, type in `l` and look at the output:

```
(Pdb) l
  4     def main():
  5         print("Add the values of the dice")
  6         print("It's really that easy")
  7         print("What are you doing with your life.")
  8         import pdb; pdb.set_trace()
  9  ->     GameRunner.run()
 10     
 11     
 12     if __name__ == "__main__":
 13         main()
[EOF]
``` 

If we want to see the whole file, we can call the list function with the range 1 to 13 like so:

```
(Pdb) l 1, 13
  1     from dicegame.runner import GameRunner
  2     
  3     
  4     def main():
  5         print("Add the values of the dice")
  6         print("It's really that easy")
  7         print("What are you doing with your life.")
  8         import pdb; pdb.set_trace()
  9  ->     GameRunner.run()
 10     
 11     
 12     if __name__ == "__main__":
 13         main()
```

Unfortunately, we don't get that much information from this file alone but we do see that it is calling the `run()` method of the `GameRunner`
class. At this point, you might be thinking, "Awesome, I'll just set a `pdb` in the run method in the `dicegame/runner.py` file !" That will
work, but there's an even easier way using the `step` command we will discuss next.

### 2. `s(tep)` a.k.a let's see what this method does...

```
s(tep)
    Execute the current line, stop at the first possible occasion
    (either in a function that is called or in the current
    function).
```

Your current line of execution should still be on `:9` and you can tell the current line by looking at the `->` outputted by the `list` command. 
Let's call the `step` command and see what happens.

```
(Pdb) s
--Call--
> /Users/Development/pdb-tutorial/dicegame/runner.py(21)run()
-> @classmethod
```

Nice! We're currently in the `runner.py` file on line 21 which we can tell from this line:
`> /Users/Development/pdb-tutorial/dicegame/runner.py(21)run()`.
The problem is, we don't have much context so run the `list` command to checkout the method.

```
(Pdb) l
 16             total = 0
 17             for die in self.dice:
 18                 total += 1
 19             return total
 20     
 21  ->     @classmethod
 22         def run(cls):
 23             # Probably counts wins or something.
 24             # Great variable name, 10/10.
 25             c = 0
 26             while True:
```

Awesome! Now we have some more context on the `run()` method but we are currently on `:21`. Let's `step` in one more time so that we enter the method itself and
then run the list command to see our current position.

```
(Pdb) s
> /Users/Development/pdb-tutorial/dicegame/runner.py(25)run()
-> c = 0
(Pdb) l
 20     
 21         @classmethod
 22         def run(cls):
 23             # Probably counts wins or something.
 24             # Great variable name, 10/10.
 25  ->         c = 0
 26             while True:
 27                 runner = cls()
 28     
 29                 print("Round {}\n".format(runner.round))
 30  
```

As we can see, we are on a terribly named `c` variable that will cause us a major issue if we try to call it (remember the comment from earlier regarding the
`c(ontinue)` command). We are just before the `while` loop so let's enter the loop and see what else we can uncover.

### 3. `n(ext)` a.k.a I hope this current line doesn't throw an exception

```
n(ext)
    Continue execution until the next line in the current function
    is reached or it returns.
```

From the current line, type the `n(ext)` command followed by `list` (notice a pattern) and let's observe what happens.

```
(Pdb) n
> /Users/Development/pdb-tutorial/dicegame/runner.py(27)run()
-> while True:
(Pdb) l
 21         @classmethod
 22         def run(cls):
 23             # Probably counts wins or something.
 24             # Great variable name, 10/10.
 25             c = 0
 26  ->         while True:
 27                 runner = cls()
 28     
 29                 print("Round {}\n".format(runner.round))
 30     
 31                 for die in runner.dice:
```

Now our current line on the `while True` statement! We can keep calling `next` indefinitely until the program throws an exception or terminates. Call `next` 3 more
times to get to the `for` loop and then follow up `next` with `list`.

```
(Pdb) n
> /Users/Development/pdb-tutorial/dicegame/runner.py(27)run()
-> runner = cls()
(Pdb) n
> /Users/Development/pdb-tutorial/dicegame/runner.py(29)run()
-> print("Round {}\n".format(runner.round))
(Pdb) n
Round 1

> /Users/Development/pdb-tutorial/dicegame/runner.py(31)run()
-> for die in runner.dice:
(Pdb) l
 26             while True:
 27                 runner = cls()
 28     
 29                 print("Round {}\n".format(runner.round))
 30     
 31  ->             for die in runner.dice:
 32                     print(die.show())
 33     
 34                 guess = input("Sigh. What is your guess?: ")
 35                 guess = int(guess)
```

At this current point, if you continue to type the `next` command you will then iterate through the `for` loop for the length of the `runner.dice`
attribute. We can take a look at the length of the `runner.dice` by calling the `len()` function around it in the `pdb` REPL which should return 5.

```
(Pdb) len(runner.dice)
5
```

Since the length is _only_ 5 items, we could iterate through the loop by calling `next` 5 times, but let's say there were 50 items to iterate over, or even 10,000!
A better option would be to set a break point and then `continue` to that break point instead.


### 4. `b(reak)` a.k.a I don't want to type `n` anymore

```
b(reak) [ ([filename:]lineno | function) [, condition] ]
    Without argument, list all breaks.

    With a line number argument, set a break at this line in the
    current file.  With a function name, set a break at the first
    executable line of that function.  If a second argument is
    present, it is a string specifying an expression which must
    evaluate to true before the breakpoint is honored.

    The line number may be prefixed with a filename and a colon,
    to specify a breakpoint in another file (probably one that
    hasn't been loaded yet).  The file is searched for on
    sys.path; the .py suffix may be omitted.
```

We're only going to pay attention to the first two paragraphs of `b(reak)`'s description in this tutorial. Like I mentioned in the previous section, we want
to set a break point past the `for` loop so we can continue to navigate through the `run()` method. Let's stop on `:34` since this has the input function
which will break and wait for a user input anyways. To do this, we can type `b 34` and then `continue` to the break point.

```
(Pdb) b 34
Breakpoint 1 at /Users/Development/pdb-tutorial/dicegame/runner.py(34)run()
(Pdb) c

[...] # prints some dice

> /Users/Development/pdb-tutorial/dicegame/runner.py(34)run()
-> guess = input("Sigh. What is your guess?: ")
```

We can also take a look at the break points that we have set by calling `break` without any arguments.

```
(Pdb) b
Num Type         Disp Enb   Where
1   breakpoint   keep yes   at /Users/Development/pdb-tutorial/dicegame/runner.py:34
    breakpoint already hit 1 time
```

To clear your break points, you can use the `cl(ear)` command followed by the breakpoint number which is found in the leftmost column of the above
output. Let's clear the breakpoint now by calling the `clear` command followed by 1.

**NB**: You can also clear all the breakpoints if you don't provide any arguments to the `clear` command.

```
(Pdb) cl 1
Deleted breakpoint 1 at /Users/Development/pdb-tutorial/dicegame/runner.py:34
```

From here we can call `next` and execute the `input()` function. Let's just type 10 for our guess and once we are back in the `pdb` REPL, call `list` so we can see
the next few lines.

```
(Pdb) n
Sigh. What is your guess?: 10
> /Users/Development/pdb-tutorial/dicegame/runner.py(35)run()
-> guess = int(guess)
(Pdb) l
 30     
 31                 for die in runner.dice:
 32                     print(die.show())
 33     
 34                 guess = input("Sigh. What is your guess?: ")
 35  ->             guess = int(guess)
 36     
 37                 if guess == runner.answer():
 38                     print("Congrats, you can add like a 5 year old...")
 39                     runner.wins += 1
 40                     c += 1


``` 

Remember that we are trying to find out why our guess wasn't correct on our first playthrough. It seemed like there was an error with the `guess == runner.answer`
equality condition. We should check to see what the `runner.answer()` method is doing in case there might be an error there. Call `next` and then let's call `step`
to _step_ into the `runner.answer()` method.

```
(Pdb) s
--Call--
> /Users/spiro/Development/mobify/engineering-meeting/pdb-tutorial/dicegame/runner.py(15)answer()
-> def answer(self):
(Pdb) l
 10         def reset(self):
 11             self.round = 1
 12             self.wins = 0
 13             self.loses = 0
 14     
 15  ->     def answer(self):
 16             total = 0
 17             for die in self.dice:
 18                 total += 1
 19             return total
 20  
```

I think I found the issue! On line 18, it doesn't look like the `total` variable is adding up the values of the dice like we want it to. Let's see if we can fix that by
checking whether a `die` has an attribute which would contain its value. To get to line 18, you can either set a break point or just call `next` until you
hit the first iteration. Once you're on `:18`, let's call the `dir()` function on the `die` instance and check what methods and attributes it has.

```
-> total += 1
(Pdb) dir(die)
['__class__', '__delattr__', [...], 'create_dice', 'roll', 'show', 'value']
``` 

There is a `value` attribute after all! Let's call that and see what returns (remember, this value will probably be different than mine). And just for fun,
let's make sure it is equal to the value that the die is showing by calling the `show()` method as well.

```
(Pdb) die.value
2
(Pdb) die.show()
'---------\n|*      |\n|       |\n|      *|\n---------'
```

**NB**: If you want the newline character `\n` to print as a newline, call `print()` with `die.show()` as its argument.

It looks like it works as expected and we're ready to fix the answer method. However, some of us may want to continue with the debugging process and catch all the
errors in one go. Unfortunately, we are once again stuck in this for loop. You might think to set a break point at `:19` and then call `continue` but there is actually
a better way in this case.

### 5. `r(eturn)` a.k.a. I want to get out of this function

```
r(eturn)
    Continue execution until the current function returns.
```

The `return` is a great _power user_ command that let's you examine the final outcome of a function. While you could set a breakpoint at the return call, the
`return` pdb command will help if there are multiple return statements in a single function since it only follows the path of execution for a single return. Let's
call the `return` command and get to the end of the function.

```
(Pdb) r
--Return--
> /Users/Development/pdb-tutorial/dicegame/runner.py(19)answer()->5
-> return total
(Pdb) l
 14     
 15         def answer(self):
 16             total = 0
 17             for die in self.dice:
 18                 total += 1
 19  ->         return total
 20     
 21         @classmethod
 22         def run(cls):
 23             # Probably counts wins or something.
 24             # Great variable name, 10/10.
(Pdb) 
```

To check the value of the returned `total` variable, you can call `total` here or look at the final value in line below the `--Return--` output. Now, to return back
to the `run()` method, call the `next` command and you'll be back in your happy place.

At this point, you can exit the `pdb` debugger by calling `exit()` **OR** `CTRL+D` (same as the Python REPL). With these five commands, you should be able to figure
out a couple other bugs and then follow along with a bit more advanced `pdb` examples.


## Advanced `pdb` topics

Here are a couple advanced `pdb` commands that you can also use.

### The `!` (bang) command

```
!
  Execute the (one-line) statement in the context of the current stack frame.
```

The bang command (`!`) lets `pdb` know that the following statement will be a Python command and not a `pdb` command. Where this is helpful is in the `run()` method
with the `c` variable. Like I mentioned in the beginning of the tutorial, calling `c` in `pdb` will issue the `continue` command. Navigating in your `pdb` REPL, stop
at `:26` in the `runner.py` file and from that point you can prefix `c` with the `!` command and see what happens. 

```
(Pdb) !c
0
```

We get the intended result, since `:25` assigned `c = 0`!


### `pdb` Post Mortem

```
pdb.post_mortem(traceback=None)
    Enter post-mortem debugging of the given traceback object. If no traceback is given, it uses the one of the exception that is currently being handled
    (an exception must be being handled if the default is to be used).

pdb.pm()
    Enter post-mortem debugging of the traceback found in sys.last_traceback. 
```

While both methods may look the same, `post_mortem() and pm()` differ by the traceback they are given. I commonly use `post_mortem()` in the `except` block.
However, we will cover the `pm()` method since I find it to be a bit more powerful. Let's try and see how this works in practice.

Open up the python REPL by typing `python` in your shell in the root of this project. From there, let's import the `main` method from the `main` module and import `pdb`
as well. Play the game until the we get the exception after trying to type `Y` to continue the game.

```
>>> import pdb
>>> from main import main
>>> main()
[...]
Would you like to play again?[Y/n]: Y
Traceback (most recent call last):
  File "main.py", line 12, in <module>
    main()
  File "main.py", line 8, in main
    GameRunner.run()
  File "/Users/Development/pdb-tutorial/dicegame/runner.py", line 62, in run
    i_just_throw_an_exception()
  File "/Users/Development/pdb-tutorial/dicegame/utils.py", line 13, in i_just_throw_an_exception
    raise UnnecessaryError("You actually called this function...")
dicegame.utils.UnnecessaryError: You actually called this function...
```

Now, let's call the `pm()` method from the `pdb` module and see what happens.

```
>>> pdb.pm()
> /Users/Development/pdb-tutorial/dicegame/utils.py(13)i_just_throw_an_exception()
-> raise UnnecessaryError("You actually called this function...")
(Pdb) 
```

Look at that! We recover from the point where the last exception was thrown and are placed in the `pdb` prompt. From here, we can examine the state the program was in
before it crashed which will help you in your investigation.

**NB**: You can also start the `main.py` script using `python -m pdb main.py` and `continue` until an exception is thrown. Python will automatically enter `post_mortem`
mode at the uncaught exception.


## The End

Congrats on making it to the end and thank you for following along in this tutorial! If you have any comments, critiques, or additional advanced examples, I'm open to pull requests.

