---
layout: post
title: How to make Testing simplify your life
date: 2016-03-11 17:32:00 +0000
categories: code testing
heroimg: images/ninjastyle.jpg
---

## A Best Practices guide for testing

Let me start out by saying two things:

 1. I don't test always test my code. But when I do, I feel better after.
 2. This is heavily based on JavaScript Air 004

What to do, what not to do. Coding is hard.

 - How to write good tests
 - What kinds of tests to use

![hello](/images/simpsons-testing.gif)

>Everybody writes bugs, we all write bugs. So, it's not a matter of "can you write code correctly or not the first time?", we all write incorrect code. That's all that we do, is we only write incorrect code.

Joe Eames, JavaScript Air 004

![hello](/images/testing-just-as-i-expected.gif)

### Unit tests

Unit testing will not solve all our problems.

### Integration tests

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
 - Test failures:
   - Problem with code? Problem with test?
   - Test was right, but misunderstood?
   - Tests guide code
   - Like NP-Complete problems, Travelling Salesman
 - Benefits
   - Leads to better code
     - Hard to test? Probably not well written
     - Designing for test leads to better code
     - Write API in tests: design your API first
   - Leads to peace of mind
   - Correct products
   - Documentation of what the code is supposed to do
   - Onramping, tests as specification
   - Confidence while refactoring or adding features
   - Being able to limit what you're thinking about
   - Testing is a feature, just like any other code
   - Reveals hidden aha moments about code
   - Red/green refactor
   - Write a test, make it green, then refactor
   - Go green as fast as possible, then make it better
     - First, hack stuff.
     - Hard to do two things at the same time: Solving a problem or engineering it well.
     - Just go and solve the problem! Worry later.
     - Speed trumps design
   - Remove dependencies between tests and code
   - **Test behaviours, not implementations**
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
