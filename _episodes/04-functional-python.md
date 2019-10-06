---
title: "Functional Programming in Python"
teaching: 0
exercises: 0
questions:
- "Key question (FIXME)"
objectives:
- "First learning objective. (FIXME)"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

## The Functional Paradigm


## Comprehensions

Comprehensions are a more **Pythonic** way to structure map and filter operations.
They serve exactly the same purpose, but are more concise and can be easier to structure in more complex cases, such as mapping over a 2d data structure.
Using comprehensions also gives us control over which data structures we end up with, rather than always getting back a `map` or `filter` iterable.

### List Comprehensions

The **list comprehension** is probably the most commonly used comprehension type.
As you might expect from the name, list comprehensions produce a list from some other iterable type.
In effect they are the same as using `map` and/or `filter` and using `list()` to cast the result to a list, as we did previously.

All comprehension types are structured in a similar way, using the syntax for a literal of that type (in this case a list literal) containing what looks like the top of a for loop.
To the left of the `for` we put the equivalent of the map operation we want to use:

~~~
print([i for i in range(5)])
print([2 * i for i in range(5)])
~~~
{: .language-python}

~~~
[0, 1, 2, 3, 4]
[0, 2, 4, 6, 8]
~~~
{: .output}

We can also use list comprehensions to perform the equivalent of a filter operation, by putting the filter condition at the end:

~~~
print([2 * i for i in range(5) if i % 2 == 0])
~~~
{: .language-python}

~~~
[0, 4, 8]
~~~
{: .output}

### Dictionary and Set Comprehensions

Dictionary and set comprehensions are fundamentally the same as list comprehensions but use the dictionary or set literal syntax:

~~~
print({2 * i for i in range(5)})
~~~
{: .language-python}

~~~
{0, 2, 4, 6, 8}
~~~
{: .output}

~~~
print({i: 2 * i for i in range(5)})
~~~
{: .language-python}

~~~
{0: 0, 1: 2, 2: 4, 3: 6, 4: 8}
~~~
{: .output}

## Generators

### Generator Comprehensions

Generator comprehensions look very similar to list comprehensions, but behave slightly differently.
What happens if we try to use them in the same was as we did list comprehensions?

~~~
print((2 * i for i in range(5)))
~~~
{: .language-python}

~~~
<generator object <genexpr> at 0x7efc21efcdd0>
~~~
{: .output}

Like the `map` and `filter` functions, generator expressions are not evaluated until you iterate over them.

~~~
for i in (2 * i for i in range(5)):
    print(i)
~~~
{: .language-python}

~~~
0
2
4
6
8
~~~
{: .output}

Performance characteristics.

If we initialise and fully iterate over each comprehension, the time taken is very similar.

~~~
%%timeit
l = [2*x for x in range(1000000)]
for x in l:
    pass
~~~
{: .language-python}

~~~
66 ms ± 492 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
for x in l:
    pass
~~~
{: .language-python}

~~~
65.4 ms ± 2.3 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

If we initialise both a list and a generator comprehension and store them, but do not iterate over them, the generator is around 100,000x faster.

~~~
%%timeit
l = [2*x for x in range(1000000)]
~~~
{: .language-python}

~~~
59.7 ms ± 372 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
~~~
{: .language-python}

~~~
494 ns ± 11.5 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
~~~
{: .output}

If we initialise both versions, but only partially iterate over each, then we can see the benefit of using a generator comprehension over a list comprehension.

~~~
%%timeit
l = [2*x for x in range(1000000)]
for x in l:
    if x > 10:
        break
~~~
{: .language-python}

~~~
63.5 ms ± 1.77 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
for x in l:
    if x > 10:
        break
~~~
{: .language-python}

~~~
988 ns ± 5.76 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
~~~
{: .output}

Since the list comprehension produces an actual list, whereas the generator comprehension doesn't produce any values until they are required, we also see a large difference in memory use between the two:

~~~
%%memit
# Uses about 4GB RAM
for x in [2*x for x in range(100000000)]:
    pass
~~~
{: .language-python}

~~~
peak memory: 4786.71 MiB, increment: 3862.02 MiB
~~~
{: .output}

~~~
%%memit
for x in (2*x for x in range(100000000)):
    pass
~~~
{: .language-python}

~~~
peak memory: 924.94 MiB, increment: 0.00 MiB
~~~
{: .output}

## Generator Functions

There's one final common place where we see lazy evaluation - a **generator function**.
Generator functions are similar to generator comprehensions, but in using a function.

Instead of using `return` to return a single value from a function, a generator function **yields** multiple values:

~~~
def count_to_n(n):
    for i in range(n):
        yield i + 1

for i in count_to_n(5):
    print(i)
~~~
{: .language-python}

~~~
1
2
3
4
5
~~~
{: .output}

Counting papers from academics

Sorting papers by title, day

Needs to end day with 2d array of papers published per person per day - run a simulation of people publishing on random days

~~~
class Person:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return self.name

class Academic(Person):
    def __init__(self, name):
        super().__init__(name)
        self.papers = []
        self.staff = []

    def write_paper(title, day):
        new_paper = Paper(title, day)

        self.papers.append(new_paper)
        return new_paper

    def add_staff(academic):
        if academic not in self.staff:
            self.staff.append(academic)

academics = [Academic(name) for name in ['Alice', 'Bob', 'Carol', 'David']]
alice = academics[0]
bob = academics[1]
alice.add_staff(bob)


~~~
{: .language-python}


## Decorators
Function that accepts a function and returns a (different) function

~~~
def decorate_hello(fun):
    def inner(*args, **kwargs):
        print('Hello!')
        return fun(*args, **kwargs)
    
    return inner

@decorate_hello
def say_world():
    print('World!')

say_world()
~~~
{: .language-python}

~~~
Hello!
World!
~~~
{: .output}

{% include links.md %}

