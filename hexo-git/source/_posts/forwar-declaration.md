---
title: A Solution to Mutual Reference in C++
date: 2018-12-28 08:06:35
tags:
    - C++
    - Program Language
    - Mutual Reference
    - Forward Declaration
---

In the recent project, I was responsible for improving program efficiency. The whole project is written in Python. To this end, I've tried to rewrite C++ code to substitute Python code in some compute-intensive part. I've met the mutual reference when rewring the C++ code. The post shows the way I practice mutual reference in C++.

## Mutual reference

There might often be such a situation that we have two classes, and they reference member variable or function of each other.  Just like Python code below:

```python
class A:
    def __init__(self):
        b = B()
    def foo(self):
        self.b.zap()

    def goo(self):
        pass
        
class B:
    def __init__(self):
        a = A()
        
    def zot(self):
        self.a.foo()
    
    def zap(self):
        pass
```
<!-- more -->

This is what we called *mutual reference*. It is not a big deal when you write mutual reference in python as python interpreter takes too much `compile' work for us, so just enjoy writing Python. But you'll fail the compile in C++ if you write code in a Python fashion that doesn't care the order of declaration and definition of class.

## Forward declaration in C++

### What is forward declaration?

The right way of mutual reference in C++ is *forward declaration* or *incomplete declaration*. The following is a definition of forward declaration:

> An incomplete declaration is the keyword class or struct followed by the name of a class or structure type. It tells the compiler that the named class or struct type exists, but doesn't say anything at all about the member functions or variables of the class or struct; this omission means that it is a (seriously) incomplete declaration of the type. Usually the compiler will be supplied with the complete declaration later in the compilation, which is why an incomplete declaration is often called a forward declaration - it is an advance forward announcement of the existence of the type. 

As you can see, forward declaration should appear in header file. When will forward declaration work in a header file?

1. If the class type X appears only as the type of a parameter or a return type in a function prototype. 
2. If the class type X is referred to only by pointer (X*) or reference (X&), even as a member variable of a class declared in A.h.
3.  If you are using an opaque type X as a member variable of a class declared in A.h. 

### A toy example

Forward declarations are essential for the following problem: Suppose we have two classes whose member functions make use of either parameters or member functions of the other class. 

```c++
class A {
public:
	void foo(B b)// Ex. 1: a parameter of the other class type
	{
		b.zap(); // Ex. 2: call a member function of the other class
	}
	void goo()
    {/* whatever */}
};
class B {
public:
	void zot(A a) // Ex. 3: a parameter of the other class type
	{
 	    a.goo(); // Ex. 4: call a member function of the other class
	}
	void zap()
	{ /* whatever */ }
};
```

The compiler will report error when it tries to compile this bit of code. The problem is that when it compiles the declaration of class A, it won't be able to understand the line labeled Ex. 1 - it hasn't seen a declaration of B yet. Obviously it would have a problem with Ex. 2, because it doesn't know about function zap either. But if the compiler could somehow understand the class A declaration, it would then have no problem with class B, because it would already know about class A when it sees Ex. 3 and Ex. 4. However, since the compiler can't compile the class A declaration, it will not be able to compile the class B declaration either. 

### How we make forward declaration to fix the problem?

Here's how we solve this problem. We take the function definitions out of the first class declarations, so that the compiler will see all of the second class before it has to compile the code for the first class functions. Here is how the example would look: This example now compiles successfully; here is the story: When the compiler sees line Ex. 1, which is now just a function prototype, it knows that B is the name of a class (from the forward declaration), and since we have only a function prototype here, we are no longer trying to call the still-unknown zap member function in B. The compiler simply records the foo prototype and continues. The compiler can then handle the class B declaration with no problem because it got all it needed from the class A declaration.

```c++
class B; // forward incomplete declaration - class B will be fully declared later
class A {
public:
	void foo(B b); // Ex. 1:
	/* rest omitted to save space */
};
class B {
    public:
	/* rest omitted to save space */
};
// the function definition later or in a separate .cpp file
void A::foo(B b) // Ex. 1B: define A's foo function after the B declaration! !
{
	b.zap(); // Ex. 2: call a member function of the other class
}
```

This example now compiles successfully; here is the story: When the compiler sees line Ex. 1, which is now just a function prototype, it knows that B is the name of a class (from the forward declaration), and since we have only a function prototype here, we are no longer trying to call the still-unknown zap member function in B. The compiler simply records the foo prototype and continues. The compiler can then handle the class B declaration with no problem because it got all it needed from the class A declaration. In line Ex. 1B, class A's function foo is defined outside of the class A declaration, after the compiler has seen the complete class B declaration. Notice the scope operator :: that tells the compiler that this foo is the one prototyped in the class A declaration. The compiler can handle the previously problematic Line Ex. 2 because it is now knows about B's zap member function.

Note: we often add  the header guard for header file. We should make the guard compatible with header file name, just like this:

```c++
//classA.h
#ifndef CLASSA_H
#define CLASSA_H

class B; // forward incomplete declaration - class B will be fully declared later
class A {
public:
	void foo(B b); // Ex. 1:
	/* rest omitted to save space */
};
 
#endif

```

Thanks to so many answers in Stackoverflow and David Kieras's note.

## Reference

http://jatinganhotra.com/blog/2012/11/25/forward-class-declaration-in-c-plus-plus/
http://umich.edu/~eecs381/handouts/IncompleteDeclarations.pdf

