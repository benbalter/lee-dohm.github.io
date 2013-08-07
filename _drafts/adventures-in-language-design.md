---
layout: post
title: Adventures in Language Design
tags: Coder
---

A friend of mine at work convinced me to take a look at [Scala][scala]. It is a "multi-paradigm language" based on Java. "Multi-paradigm" is just a fancy way of describing a language that has both object-oriented and functional programming features.^(1) [Microsoft's F#][f-sharp] is another such language. The thing that impressed me about Scala is that it has a number of the same features that Ruby has but because it is statically typed, with [type inference][type-inference], it is significantly more performant in many applications than even [JRuby][jruby].

I've [written before][why-ruby-is-awesome] on the features of Ruby that I think are going to be necessary for me to be happy about switching to any new language. Here they are again:

1. Support for in-language DSLs
1. Simple package and dependency management
1. Great documentation and documentation tools
1. Great test support

In addition to the above language and ecosystem features, I wanted to get down some of the specific language and standard library features that I think are necessary or are quickly becoming so. Just as many people state that [Design Patterns are missing language features][patterns], these are the language features that I don't believe we should have to craft by hand, they are common and standardized enough that they belong in our languages by now.

## On Being a Control Freak

Before I get down to the nitty-gritty though, I want to take the time to talk about something that I've observed in the programming world. All software developers are control freaks to one extent or another. I know that I am. I want the computer to do what I tell it to and when the computer does something slightly different, I get annoyed.

Yes, I know that so far this description sounds perfectly normal. But what software developers do after they get annoyed is the difference. Normal people get frustrated and blame the computer for getting in the way or not letting them do what they want. Software developers blame themselves and resolve to write better instructions. They double down and make better code so that the computer will do the right thing. What this creates, though, is a control freak. Someone who absolutely must have things done their way. My way or the highway you might say. And these people have a hard time letting go of that control once they're used to having it.

Many if not most of the language features below are things that take over some chore for the developer, take it out of their hands and has the compiler, the interpreter or the runtime take care of it behind the scenes. And much of the resistance for some of these features seems to be simply developers having a problem letting go. They're too used to doing things their way and the system doesn't handle it *exactly* the way they would, so they balk and say that it is a bad feature.

I think the sad part is that this kind of reactionary thinking [has been going on for a long, long time ...][the-story-of-mel]

## Object-Orientation

"X isn't object oriented!" seems to be the rallying cry of the younger programming generation. As if object-oriented programming is some blight on the face of computer science.^(2) I think *purely* object-oriented languages are a mistake, much like any language design that favors academic or idealistic purity over the ability to get work done. Steve Yegge has an article about [how OOP was taken too far in Java][yegge-java]. But, on the whole, OOP has been a good thing for the world of software development and I honestly wouldn't want to do any sort of significant work in a language that does not have some method of writing object-oriented code.

## Garbage Collection

When Java appeared in 1995, garbage collection was a very controversial feature for anything that wasn't intended to be a scripting language. Now, virtually every language is garbage collected with the only commonly-used exceptions being C, C++ and [Objective-C][objective-c].^(3) I could go back to manual memory management, but why? It was always such a huge source of bugs for even the most experienced programmers. Much like manually handling the stack in Assembly language (when was the last time you worried about the order in which parameters are placed on the stack?), memory management has to be done right every single time because the *best case* scenario is that your program crashes. Seemingly the more common case is that subtle and hard to trace bugs result. It is just better to take these exacting and expensive chores out of the hands of the programmer for the common case and allow those things to be overridden in the uncommon case.

I know that some people want to keep manual control over memory management, but game programmers have figured out the workaround for this issue even in a garbage collected environment: allocate one really large chunk of memory and use it like a heap by implementing your own memory management functions.

## Exception Handling

Pervasive use of exceptions offer at least four benefits to writing code:

1. Separates error detection from error handling
1. Separates code for the much more common successful case from the code for the hopefully abnormal error case
1. Makes the default behavior of the code to not ignore errors that occur
1. Provides automatic and very detailed debugging information

## First-Class and Higher-Order Functions

All this really means is that the language has a data type for functions and that functions can create and return new functions. While lots of code can be written without these features, there are some things that are simply more elegant, understandable and maintainable when written in this way.

## Blocks and Closures

Blocks are simply anonymous functions with special syntax so that they can be easily passed to any function. In the Ruby world, blocks are used by lots of things and the standard library makes extensive use of them. For example:

{% gist 6131692 block_example.rb %}



## Pattern Matching

## Standard Algorithms As Well As Standard Data Structures

Pretty much every language since the beginning of time has had some sort of support for standard collections: array/vector, dictionary/hash, trees, graphs, etc. But what most standard libraries have been lacking in is implementations of standard algorithms, everything you write a `for` loop for. This is one thing that I believe the C++ Standard Template Library got right and so does Ruby. Most languages have some sort of generic sort method that implements [quicksort][quicksort] given a comparison function. What I'm talking about is all the other common operations:

* **find** - Return the first item in a collection that matches a predicate^4
* **include** - Return true if any item in a collection matches a predicate
* **map** - Execute a function on each item in the collection and return a new collection containing the results
* **reduce** - Execute a function on each item in the collection and return a single summary value
* **select** - Return a new collection containing only the items that match a predicate
* *and many more...*

When you have first-class functions or, even better, blocks and closures; these things become very simple to write. And things that are simple to write create less bugs. Compare these chunks of code. Each does exactly the same thing. They look through an array and return a new array containing every value that is evenly divisible by two. The first is in C and shows the way that we've been doing it since the invention of the `for` loop.

{% gist 6131692 divisibleByTwo.c %}

And here it is in Ruby ...

{% gist 6131692 divisible_by_two.rb %}

And just to prove that other languages do it too, here it is in C++ ...

{% gist 6131692 divisibleByTwo.cpp %}

The point is that there is no reason why these standard boilerplate things we do *all the time* have to be so verbose. The C version, not counting variable declarations, memory allocations or return statements is four lines. The Ruby and C++ versions are each one line. And this is an instance where the predicate is described very simply. In cases where the predicate is more complex, often they diverge even more because of the manual accounting overhead when you are simply iterating in a loop.

## Process Queues?

*A la* [channels][channels] in Go, the [Actor pattern][actor-pattern] in Scala and the object pipeline in PowerShell?

-----

1. Given that definition, Ruby and many others are also multi-paradigm languages. Honestly, more and more mainstream languages are incorporating functional programming features. I think that purely functional programming languages will soon be obsolete because the mainstream languages will have incorporated enough functional programming features that truly FP code can be written in them but will still be better at the things that FP is *not* good at, like user interface code and other imperative tasks.
1. If you think object-oriented programming is a bad thing ... you should see what we had before.
1. Although, the new [Automatic Reference Counting][arc] (ARC) feature in Objective-C makes it look to the programmer mostly like a garbage collected language so long as common idioms for allocation and transfer of ownership of pointers are used.
1. A "predicate" or "predicate function" is a function that returns true or false given its input.

[actor-pattern]: http://www.scala-lang.org/old/node/242
[arc]: http://en.wikipedia.org/wiki/Automatic_Reference_Counting
[channels]: http://golang.org/doc/effective_go.html#channels
[f-sharp]: http://research.microsoft.com/en-us/projects/fsharp/
[golang]: http://www.golang.org
[inheritance-in-go]: http://code.rkevin.com/2010/10/how-to-use-inheritance-in-google-go/
[jruby]: http://www.jruby.org
[objective-c]: http://en.wikipedia.org/wiki/Objective-c
[patterns]: http://www.codinghorror.com/blog/2007/07/rethinking-design-patterns.html
[quicksort]: http://en.wikipedia.org/wiki/Quicksort
[scala]: http://www.scala-lang.org
[the-story-of-mel]: http://www.cs.utah.edu/~elb/folklore/mel.html
[type-inference]: https://en.wikipedia.org/wiki/Type_inference
[why-ruby-is-awesome]: /2013/05/05/thoughts-on-why-ruby-is-awesome.html
[yegge-java]: http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html
