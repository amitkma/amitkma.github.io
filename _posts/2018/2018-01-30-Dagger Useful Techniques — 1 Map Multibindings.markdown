---
layout: post
title:  "Dagger Useful Techniques — #1 Map Multibindings"
date:   2018-01-30 23:38:00+0530
categories: main
tags: android
comments: true
---

Welcome to a series of Dagger Useful Techniques to make your dependency injection experience smoother. In this series, I would like to help the developers to make the most of Dagger who are using it for dependency injection. But before I start, there are something to clarify.

This article is all about dagger techniques, not about how one can implement dagger for dependency injection. I assume that you are already familiar with the basics of dependency injection using dagger. Although I am sharing some link which will be helpful if you are new to dependency injection and want to use dagger —

- [Dependency Injection (Wikipedia)](https://en.wikipedia.org/wiki/Dependency_injection)
- [Dependency Injection in Android with Dagger 2](https://www.raywenderlich.com/146804/dependency-injection-dagger-2)
- [Dependency Injection with Dagger 2 — the Api](http://frogermcs.github.io/dependency-injection-with-dagger-2-the-api/)
- [Dagger 2 Intro Video](https://www.youtube.com/watch?v=IKD2-MAkXyQ)
- [Codepath Dependency Injection Tutorial](https://guides.codepath.com/android/Dependency-Injection-with-Dagger-2)

This is the Part 1 of this series.

# Map Multibindings
The beauty of Dagger is the annotations. We all love annotations because annotations can be interpreted at compile time by the IDE or compiler or at run-time by the framework. Dagger uses annotation to generate lots of code for you which is used for injection.

According to the official docs of dagger —

> Dagger lets you use multibindings to contribute entries to an injectable map as long as the map keys are known at compile time.
 
> To contribute an entry to a multibound map, add a method to a module that returns the value and is annotated with @IntoMap and with another custom annotation that specifies the map key for that entry. To contribute an entry to a qualified multibound map, annotate each @IntoMap method with the qualifier.

> Then you can inject either the map itself (Map<K, V>) or a map containing value providers (Map<K, Provider<V>>). The latter is useful when you don’t want all of the values to be instantiated because you’re going to extract one value at a time, or because you want to get a potentially new instance of each value each time you query the map.

The above description looks very theoretical (but I recommend you to read it and try to understand) and you are wondering what is the need of doing all this if we can directly inject different components. So let me explain this by an example. Look at the screenshot below —

![Screenshot of Google Assistance App]({{ "/assets/post_1.png" | absolute_url }})

As you can clearly see, there are different view types like text_message type, image_message type and information_message type. These all can be implemented using different types of viewholder. Here the Map Multibinding can play an important role which let you return particular viewholder corresponding to a message type. The advantage of this approach, it provides viewholder at compile time so you can ensure whether a correct viewholder is returning or not and this is also simplifies injection of view holders (I will explain later how).

# Sample Code
(Read the above contents from official doc, follow line by line and try to relate that content with the following implementation)
{% gist 612d858d12482c97d3b055787575927d %}
{% gist 62c7039cf493b9ed0115aa6b137339f9 %}

Instead of using custom MapKey, you can use default keys provided by dagger — @StringKey, @IntKey, @LongKey, @ClassKey. More info [here](https://google.github.io/dagger/api/latest/dagger/multibindings/package-summary.html)

{% gist 238497e89b1beef12ae5b9d70e8ec131 %}

`@Binds` is a drop in replacement for Provides methods which simply return an injected parameter. Prefer `@Binds` because the generated implementation is likely to be more efficient.

Now you can inject this like below —

{% gist c4ddd8265c007d2fbadb47a3154e1787 %}
This will simplify the injection of Viewholder by just adding a single method to ViewholderModule. There are some other concepts related to multibinding in dagger about which you can read [here](https://google.github.io/dagger/multibindings).

# Final Words

That’s all about **Map Multibindings**. Although there is a lot to say about multibinding but I would like you to check the docs and try to figure out which suits you best. I have not mentioned about how injection works in dagger or how to connect module (ViewHolderModule in above case) to component because I assumed that you have a basic familiarity about dagger. In next part, I will describe techniques available in new Dagger 2.11 which simplifies the injection a lot easier.

I hope you have enjoyed reading this article. You can find source code which has above implementation here — [Click Here](https://github.com/amitkma/android-architecture-boilerplate/tree/master/mobile-app/src/main/java/com/github/amitkma/boilerplate/app). Check my github profile for other sample projects — https://github.com/amitkma
