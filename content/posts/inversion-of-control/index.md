---
title: 'Understanding Inversion of Control (IoC) Principle'
date: 2024-03-04T00:48:06+05:30
draft: false
tags: ["programming"]
categories: ["FEATURED"]
---

The biggest issue, any enterprise app can face is the complexity of wiring different elements or components together. Over a long period of time, a number of frameworks have been created to provide a facility to assemble and bind these elements from different layers. These frameworks are commonly known as **Containers** and some of the very popular examples of such frameworks are **PicoContainer** and **Spring**. There are various interesting design principles used inside these containers and Inversion of Control is one such principle. In this article, we will understand what Inversion of Control exactly is and how it is used in a modern-day enterprise application.

<!--more-->

_This article was originally written at https://medium.com/@amitkma/understanding-inversion-of-control-ioc-principle-163b1dc97454_

# What is IoC???

As the name suggests,  Inversion of Control **Principle** (it’s a principle, not a design pattern) is basically about inverting the control flow of a program.  This term was popularized by Stefano Mazzocchi in a defunct Apache Software Foundation project,  [Avalon](https://en.wikipedia.org/wiki/Apache_Avalon), then further popularized in 2004 by  [Robert C. Martin](https://en.wikipedia.org/wiki/Robert_Cecil_Martin)  and  [Martin Fowler](https://en.wikipedia.org/wiki/Martin_Fowler_(software_engineer)).

As Martin Fowler said,  “_Inversion of control is a common characteristic of frameworks, so saying that these lightweight containers (spring and pico container, etc.) are special because they use inversion of control is like saying my car is special because it has wheels._”  It is basically a defining characteristic of a framework.  Inversion of Control is used to increase the modularity of the program and makes it extensible.  But the question still is,  “**What aspect of control are they really inverting?**”

In early days of computer software, the command-line was used for general programs and thus those user interfaces were controlled by the application itself. In such programs,  you directly control the flow of the program by entering the response into the command-line but in the GUI program, you basically hand over the control to the window system (UI framework) which then decides what to do next. Here the main control of the program moved away from you to the UI framework.  Inversion of Control is a key part of what makes a framework different from a library.  A library is essentially a set of functions that you can call, these days usually organized into classes. Each call does some work and returns control to the client whereas a framework does some work for you, you just need to plug your behavior into some places by either by subclassing or by plugging into your own classes. Then framework will call your code when it is required.

Note: Do not confuse it with “[**Dependency Inversion Principle**](https://en.wikipedia.org/wiki/Dependency_inversion_principle)”. DIP is a completely different principle which also does the job of providing loose coupling between classes and inverting the flow of dependencies. (Will write an article on it soon.)

# Implementation Techniques

Let’s now discuss how Inversion of Control can be implemented in the various programs.  There are several basic design patterns which are used to implement IoC in object-oriented programming.

-   Using a  [service locator pattern](https://en.wikipedia.org/wiki/Service_locator_pattern)
-   Using  [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection)  pattern
-   Using a contextualized lookup
-   Using  [template method design pattern](https://en.wikipedia.org/wiki/Template_method_design_pattern)
-   Using  [strategy design pattern](https://en.wikipedia.org/wiki/Strategy_design_pattern)

In all of the above patterns,  Service Location (SL) and Dependency Injection (DI) are the most widely used patterns.  In this article, we will understand the difference between DI and SL. In the further article of this series, we will look into these patterns deeply and learn how to write such patterns. Don’t confuse SL and DI, they both are completely different and the flow of the code too.

# Let’s take an example

To understand Inversion of Control and it’s implementation by using dependency injection and service locator pattern, we take an example, a very simple one. In this following example, we have a component called “Library” which is used to return a list of books authored by a specific author. The code is self-explanatory and very simple for anyone to understand.

{{< gist amitkma 0a98f804c04bb9bdb144f65db7cb3dc5 >}}

It gets all the books from a `finder` object and then just checks if it is written by a particular author or not. The whole catch here is the `finder` object and how we basically connect it with the `Library` component. Reason why we are interested in this `finder` object is because we want `booksAuthoredBy()` to be completely independent of the implementation of `Finder` . This `Finder` can be remote implementation which fetches books from a server or it can also be a local database implementation which fetches books from a locally stored database. This can easily be done by creating an Interface `BookFinder` and then providing its implementation. 

{{< gist amitkma 0736be925b5ca2d4745515b6dcd6fe77 >}}

So far so good as I know the `finder` and I know it’s implementation. But we are making it as a framework and it must be extensible and usable by others too. Now think, if you are a college library manager and you want to use this framework for your college but you have your own database of books which is stored on some other server in a different way. You want to provide your own implementation of `BookFinder` rather than using my implementation.

{{< figure src="https://miro.medium.com/v2/resize:fit:722/format:webp/1*cNE3LWNAiYUQsLrAkikA3Q.png" title="Simple Dependency Diagram of Framework." align="center">}}

If you look at the above code and the diagram, you will notice that our `Library` class depends on both the `interface BookFinder` and it’s implementation `class BookFinderImpl` (`RemoteBookFinder` in the above example). As we are making it as an extensible framework, it would be better it depends only on the interface, not on the implementation details of it. But then how would get the instance to work with??? Now it’s the time to use **Inversion of Control principle** into a real system.

As mentioned above, there are various ways in which IoC can be implemented and used. Let’s take a look at both  **Dependency Injection**  and  **Service Locator**.

# Dependency Injection (aka D. I.)

The overall idea of Dependency Injection is to have a separate object, an **Assembler** which can then populate various fields by different instances or implementation by injecting those into the class.  Now our dependency diagram looks like this -

{{< figure src="https://miro.medium.com/v2/resize:fit:882/format:webp/1*wGcXvIuP9QYkVKlh17DcGA.png" title="Dependency Diagram after using DI pattern." align="center">}}

It can be seen that now our  `Library`  class only depends on  `BookFinder`  i.e. the interface. The internal implementation of the Assembler is out of the scope of this article and will be discussed in the next part of this article. There are three styles popular for doing DI in such frameworks.

-   Constructor Injection
-   Setter Injection
-   Interface Injection

## Setter and Field Injection using Dagger

{{< gist amitkma 951d6f8453eb092eaa4bfd86de524ef6 >}}

In the above code snippet, you will see that  `class Library`  doesn’t know anything about the implementation of  `interface BookFinder`. All work of assembling and providing dependencies is being done by  [Dagger](https://dagger.dev/)  which is a compile-time DI framework for Java & Android. (Internals of Dagger will be discussed in the upcoming articles)

# Service Locator (aka SL)

Dependency Injection (DI) allows us to make our Library extensible so that anyone can provide the suitable implementation of  `BookFinder`  based on their needs/use-cases. But DI is not the only way to break this dependency, SL is such another technique to implement Inversion of Control.

The idea behind a service locator is to have an object that knows how to get all of the services that an application might need. So in our implementation of the Library Framework, Service locator would have a method that returns a  `BookFinder`  when one is needed. Now our dependency diagram will look like this

{{< figure src="https://miro.medium.com/v2/resize:fit:1042/format:webp/1*EfAFtp0lApHhPuQdUfCMDw.png" title="Dependency Diagram after using the SL pattern." align="center">}}

Let’s take a look at how a simple Service Locator can be implemented. In our implementation, we are going to use a single instance of locator class.

{{< gist amitkma 1c03994127cac64680b29a0e1c58e0c2 >}}


# Difference between Dependency Injection and Service Locator

Though both the approaches solve the fundamental problem of large enterprises applications ie. decoupling the dependencies from each other and in both the cases our application code (`class Library`) is independent of the correct implementation of the  `interface BookFinder`  . However, in the Service Locator pattern, application explicitly requests the dependencies by sending a message to the locator but in Dependency Injection, the dependency just appears without asking explicitly.

Dependency injection can help make it easier to see what the component dependencies are and it also makes testing easy. With dependency injector, you can just look at the injection mechanism, such as the constructor, and see the dependencies. With the service locator, you have to search the source code for calls to the locator. While doing testing, you can easily provide the mock implementation of your actual classes in DI pattern. There is not much difference between Dependency Injection or Service Locator pattern. So it just depends on your use-case and how well you are familiar with a particular framework. But as Martin Fowler said —

> The key difference is that with a Service Locator every user of a service has a dependency to the locator. The locator can hide dependencies to other implementations, but you do need to see the locator. So the decision between locator and injector depends on whether that dependency is a problem.

He then further says that —

> So the primary issue is for people who are writing code that expects to be used in applications outside of the control of the writer. In these cases even a minimal assumption about a Service Locator is a problem.

That’s all about the Inversion of Control for this article. There are few more topics and detailed articles to come on this topic and I will write them in upcoming days.

<br/>
<br/>

I hope you have enjoyed reading this article and if you do kindly show some ❤ by hitting the reactions below and sharing it with your fellow devs. Every reaction is appreciated and motivates me to write more. Ask any doubt/suggestion/correction in the comment section below or Tag/DM me on twitter.

Follow me on here to receive further updates related to this and upcoming articles. Follow me on Twitter — https://twitter.com/iamitkma. Thank you very much for reading so far. ❤❤❤ Much love ❤❤❤