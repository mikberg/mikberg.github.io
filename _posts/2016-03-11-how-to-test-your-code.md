---
layout: post
title: How to make Testing simplify your life
date: 2016-03-11 17:32:00 +0000
categories: code testing
heroimg: images/ninjastyle.jpg
---

## Why and how to test

Let me start out by saying two things:

 1. I don't test always test my code. But when I do, I feel better after.
 2. This is heavily based on JavaScript Air 004

What to do, what not to do. Coding is hard.

 - How to write good tests
 - What kinds of tests to use

![hello](/images/simpsons-testing.gif)



![hello](/images/testing-just-as-i-expected.gif)

## Benefits of Testing Your Code


Testing helps you achieve peace of mind about your code. Writing a solution to a half-complex problem, then manually testing it only takes you so far. With some experience, you'll surely recognize the situation of shipping some code with confidence, only to find it break apart on first real try.

Testing gives experience with the mindset of putting your code under the most extreme conditions it can get into. What if the number passed in was negative when we always assumed it would be positive? What if it wasn't a number at all?

>Everybody writes bugs, we all write bugs. So, it's not a matter of *can you write code correctly or not the first time?*, we all write incorrect code. *That's all that we do, we only write incorrect code*.
>
> -- <cite>Joe Eames, JavaScript Air 004</cite>

**Coding is hard**, and we should all acknowledge that. Among the primary reasons to test your code is to gain confidence that it will behave as *expected*, whatever that is.

Not convinced? Here are some additional arguments:

### Tested Code is Better Code
<!--
- Leads to better code
  - Hard to test? Probably not well written
  - Designing for test leads to better code
  - Write API in tests: design your API first
- Reveals hidden aha moments about code
- Testing is a feature, just like any other code
- Remove dependencies between tests and code
-->

Many people<sup>[who?]</sup> will tell you that code testing leads to better code quality. This is especially true when using unit tests and at least some variation on test driven development, even if it's a sloppy one. Here's why:

**If your code is hard to test, then it's probably not very well written.** What identifies good code is a large question, but the emphasized sentence here is a good rule of thumb. A quality of good code that most will agree on is that it separates concerns. Experienced programmers limit function bodies to only do one logical thing for this reason.

#### Aligning Your Goals

Code that is hard to test probably has either too many things going on, or too many dependencies (or both). Think of this as a problem of aligning interests: When writing untested code, there is a conflict of interest between speed (or laziness) and separation of concerns, and it doesn't matter as much for the short term how your code is structured. **When your code has to be tested, your goals are more aligned,** because tests are easier to write for well written code!

#### Think as a Consumer

Another one: When you're writing tests first, you're **designing your code's API first.** Testing puts you into code consumer mode, where you'll write code against what's going to be the interface to other stuff. Designing your API without so much concern for inner workings leads to a better API design, which leads to easier consumption of your module, which leads to cleaner code in the project as a whole.

#### Eureka moments

**Testing leads to a-ha moments.** Often because it forces you to think about edge cases – the value zero, `10^12`, `null` or `undefined`. You'll have the opportunity to think about, reflect on and acknowledge what should happen under strange circumstances. Merely the process of thinking about them, or other situations your code can face, often leads to realizations of how the code can be *simplified* (as well as how it has to guard itself).

These eureka moments can also come from one of the most frustrating situations: when your code and tests don't agree. You're put in the situation of having to decide which is right. If you're in this situation, something has probably gone wrong with the design, or your assumptions have changed. Think of this as a good sign! Your code will be happier.

As a side note, I like to think about this situation like an NP-complete problem, *[The travelling salesman problem][travellingsalesman]*. These problem instances basically cannot be calculated without checking all possible solutions, so it's very hard. However, if you have God-given correct answer to the instance, testing the outcome of the algorithm is easy as `t.equal(outcome, godGiven)`. Doesn't it make sense that testing is easier than writing actual code?

### Documentation

- Documentation of what the code is supposed to do
- Onramping, tests as specification
- Being able to limit what you're thinking about

### Refactoring

- Confidence while refactoring or adding features
- Red/green refactor
- Write a test, make it green, then refactor

### Correct Products

  - Correct products

### Speed

- Go green as fast as possible, then make it better
  - First, hack stuff.
  - Hard to do two things at the same time: Solving a problem or engineering it well.
  - Just go and solve the problem! Worry later.
  - Speed trumps design

## Unit tests

Unit testing will not solve all our problems.

## Integration tests

![Integration testing a bike](/images/testing-bicycle-integration-test.gif)

 - What to test?
   - Be pragmatic
   - Application vs library (library easier)
   - Find a bug, write a test, fix the bug
   - TDD is the best thing we know about
     - Worst thing, except for all the others
     - Everybody agrees they should have them, just not right now
   - Know when not to test
     - Comes with experience
   - We All Write Bugs
     - That's all we do, we all write incorrect code.
   - How do I make it work, vs how to use it?
     - We flip into consumer mode
   - If we change the implementation details, none of our tests should break
     - Re*writing* is not safe
   - Create value
   - No new tests for refactoring internals
   - Don't mock internals
   - **Test behaviours, not implementations**
 - Test failures:
   - Problem with code? Problem with test?
   - Test was right, but misunderstood?
   - Tests guide code
   - Like NP-Complete problems, Travelling Salesman
 - Reading tests is a form of documentation
   - Often better than documentation itself
 - Find a test framework you're happy with (AVA, Sinon)
 - Integration tests vs unit tests
   - There's a spectrum
   - Integration
     - Expensive
     - Mimicks the user
     - Codifies manual tests
     - See how a number of closely related or collaborative components work together
   - Unit testing
     - Cheap
     - Bulk of testing effort
     - Pulls out one specific piece
     - TDD
     - Pure unit test: the thing you're testing has no dependencies, or you fake all the dependencies
     - Dependency injection more a part of the code itself, a technique to write testable code
     - Stubs, spies, mocks
     - Often a class or a function or an object
     - When you write unit tests, take it out of it's context, create an artificial context in which to probe it. Test *one* behaviour.
     - Good Unit Tests:
       - Simple!
       - A test that runs in isolation from other tests
       - Not testing a module in isolation?
         - The test shouldn't have any side effects
         - It's the *test* that is isolated, not necessarily your *class*/OUT
      - Not overspecified, knows too much about your implementation
       - AAA-system: Arrange, Act, Assert (each 3 lines of code)
         - First set things up
         - Make some change
         - Test that it has changed
       - Reads like a little story
       - Only needs basic understanding of the domain
       - Someone else
       - No branching, no looping.
       - Generally *one* expectation
       - Has a clear statement about what the test is about
       - Never assert in two places
     - Trigger for writing a test is when you've got a new piece of *behaviour*
       - Test needs to capture that behaviour
       - Test outside-in: start with the notion of the use case
       - Don't make that outside your UX or REST API
       - Test your domain models.
       - Most of us are testing much too much
       - Test the surface of the module, no things that are internal or private
       - Should be quite narrow
       - The internal API
       - Only write tests to cover implementation details when you need to better understand the refactoring of the simple implementation we start with
 - Coverage
   - 100 % coverage does not ensure it works
   - Strive for 100 %, cause then you hit 80.
   - Overrated?
   - Test what's important
   - Don't test cruff
   - Doesn't tell you what happens when bad values come along
   - **You gotta think!**
   - Relative code coverage between commits?
   - Does not tell you what combinations of paths are actually being run.
 - Mocking, stubbing, dependency injection


![hello](/images/rocket-test.gif)

Sources:

 - JavaScript Air 004
 - https://vimeo.com/68375232


[travellingsalesman]: https://en.wikipedia.org/wiki/Travelling_salesman_problem
