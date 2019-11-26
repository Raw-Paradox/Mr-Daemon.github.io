---
title: "__name__ in python"
---

## Motivation
This article is about `__name__` in python, today when I was reading some articles about multithread and lock in python, I mentioned that almost every article has a code snippet like that:
```python
if __name__ == "__main__":
    # code in this block
```
That quite confused me at first, though I am a newbee at python but I already have some experience in Java, so I think this maybe is related to main function or the start of this program. To ensure my guess, I went to google.

## __Name__ guard
As a stackoverflow-oriented programmer, I quickly find result in stackoverflow~~~
### variable `__name__`
When you execute your python program, the interpreter will define some special variables. `__name__` is one of them.  
* If your module is executed as main program, the interpreter will define `__name__` with the hard-coded string `"__main__"`
* In other case, your module is imported by another module, the interpreter will look at the filename of your module. For example, we import test.py in main program like that:
  ```python
  import test
  ```
  
  The interpreter will strip off `.py` of your module name, in this case, "test", and assign it to `__name__` in this module.
  ```python
  __name__ = "test"
  ```

so the snippet we mentioned at the start of this article can be used to ensure the code in block will be executed only when this module is executed as main program.
### What if import module without __name__ guard
In most cases we import a module to access the functions and classes in it, but in python, we can write executable code everywhere with no limit, so what will happen if there are some scripts in the module we want to import?  
Let's use a slightly code sample to explore how imports and scripts work:

```python
# assume this is test1.py
print('before import')
import test2
print('after import')

test2.funA()
```
```python
# assume this is test2.py
def funA():
    print('This is funA')
    
funA()
if __name__ == "__main__":
    print('after __name__ guard')
    funA()
```

When you run `python test1.py`, the result will look like this:

```shell
before import
This is funA
after import
This is funA
```

And when you run `python test2.py`, the result is:

```shell
This is funA
after __name__ guard
This is funA
```

So we can easily know how it works: when you import other modules in your main program, the interpreter will execute every executable script in the module you import, we surely don't want that occurs because we import these modules just in order to access the classes or functions in them. But in the other hands, a module which is imported in other module sometimes will also be executed as main program, so we need to put these scripts in the 'main examine' block.

### Note

`__name__` can be assigned any string value manually.

## Why we want this?

As we all know <s>sometimes</s>(always) there are so many bugs in the program that we didn't realize them, for example -- the annoying, unavoidable race condition, so naturally we need some test on our code, now as you think about, we will talk about the relationship between name guard and unit test.  

In the real project, usually we just need to write a part of this project, i.e. to write some libraries. After decoupling the huge project to several sperate parts, we just need to develop and maintain our own part. We surely need to guarantee that there are no serious bugs in our code, so usually we will add some unit test the module .

We can write some code under the name guard so that we can test on this module by running it as main program and these scripts won't be executed when we import this module in other file.

 