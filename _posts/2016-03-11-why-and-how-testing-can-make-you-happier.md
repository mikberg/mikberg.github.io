---
layout: post
title: Why and How Testing Can Make You Happier
date: 2016-03-11 17:32:00 +0000
categories: code testing dx
heroimg: images/ninjastyle.jpg
---

I was once a non-tester, because I didn't see the value. Then I tried it for a while, and was convinced. I've gathered some experience, but not enough. This article is an attempt to collect what I know and what I think I know, and hope I can finally learn some of the stuff I know but don't necessarily practice.

![hello](/images/simpsons-testing.gif)

The main inspiration source for this article is [JavaScript Air episode 004][jsair004], but there's also some original content in here. And some from [TDD: Where did it all go wrong?][tdd-where-did-it-all-go-wrong].

> I don't always test my code, but when I do, it feels better.
>
> – <cite>me</cite>

## What is it all about?

This, only for code:

![Integration testing a bike](/images/testing-bicycle-integration-test.gif) *The concept isn't that hard.*

This article is mostly about unit testing, as opposed to integration testing or end-to-end testing, but some aspects are true for all kinds. And know that there's a spectrum. In practice, tests are seldom one or the other, and that isn't the goal either.

Unit tests are cheap and should make up the largest part of your testing efforts. They're cheap to write and run. They look at only specific parts of the code. Integration tests are the opposite, they encompass larger parts of the code. Some mimic users.

## Why is it Important?

Testing helps you achieve peace of mind about your code. Writing a solution to a half-complex problem, then manually testing it only takes you so far. With some experience, you'll surely recognize the situation of shipping some code with confidence, only to find it break apart at its first opportunity.

Testing gives experience with the mindset of putting your code under the most extreme conditions it can get into. What if the number passed in was negative when we always assumed it would be positive? What if it wasn't a number at all?

>*Everybody writes bugs, we all write bugs. So, it's not a matter of "can you write code correctly or not the first time?", we all write incorrect code. **That's all that we do, we only write incorrect code**.*
>
> -- <cite>Joe Eames, JavaScript Air 004</cite>

**Coding is hard**, and we should all acknowledge that. Among the primary reasons to test your code is to gain confidence that it will behave as *expected*, whatever that is.

Not convinced? Here are some additional arguments:

### Tested Code is Better Code

Many people<sup>[who?]</sup> will tell you that code testing leads to better code quality. This is especially true when using unit tests and at least some variation on test driven development, even if it's a sloppy one. Here's why:

**If your code is hard to test, then it's probably not very well written.** What identifies good code is a large question, but the emphasized sentence here is a good rule of thumb. A quality of good code that most will agree on is that it separates concerns. Experienced programmers limit function bodies to only do one logical thing for this reason.

#### Aligning Your Goals

Code that is hard to test probably has either too many things going on, or too many dependencies (or both). Think of this as a problem of aligning interests: When writing untested code, there is a conflict of interest between speed (or laziness) and separation of concerns, and it doesn't matter as much for the short term how your code is structured. **When your code has to be tested, your goals are more aligned,** because tests are easier to write for well written code!

#### Think as a Consumer

When you're writing tests first, you're **designing your code's API first.** Testing puts you into code consumer mode, where you'll write code against what's going to be the interface to other stuff. Designing your API without so much concern for inner workings leads to a better API design, which leads to easier consumption of your module, which leads to cleaner code in the project as a whole.

#### Eureka moments

**Testing leads to a-ha moments.** Often because it forces you to think about edge cases – the value zero, `10^12`, `null` or `undefined`. You'll have the opportunity to think about, reflect on and acknowledge what should happen under strange circumstances. Merely the process of thinking about them, or other situations your code can face, often leads to realizations of how the code can be *simplified* (as well as how it has to guard itself).

These eureka moments can also come from one of the most frustrating situations: when your code and tests don't agree. You're put in the situation of having to decide which is right. If you're in this situation, something has probably gone wrong with the design, or your assumptions have changed. Think of this as a good sign! Your code will be happier.

<!-- As a side note, I like to think about this situation like an NP-complete problem, *[The travelling salesman problem][travellingsalesman]*. These problem instances basically cannot be calculated without checking all possible solutions, so it's very hard. However, if you have God-given correct answer to the instance, testing the outcome of the algorithm is easy as `t.equal(outcome, godGiven)`. Doesn't it make sense that testing is easier than writing actual code?-->

### Tests Document What the Code Does

No-one loves to write documentation, but it sure as hell is nice when the module your inheriting (from your one year younger self, or someone else) or interfacing is well documented. Tests can be this documentation, and with an added bonus: tests put their money where their mouths are and *demonstrate* what they mean. Like the best science teachers, they don't just tell you that hydrogen is flammable. They fill a balloon with hydrogen, let it rise to the ceiling and put a lighted match on a stick to it (One of my most memorable 5th grade classes).

Do you know what is common to all bugs? *They passed all the tests.* So when you find one, you know where not to look.

![hello](/images/testing-just-as-i-expected.gif) *At least you know what doesn't work.*

**Testing makes it easier to join a project**, because they reveal what the code is actually supposed to do. They tell a story about the design decisions, what went through the mind of the original developer.

### Refactor Without Worry

Ever looked at a horrible mess of code, but been to afraid to clean it up? The first thing I do in situations like that is to create tests to discover what the code is supposed to be doing. The tests lock down the functionality, in a good way, allowing us to focus on the spring cleaning, rather than worrying about breaking stuff.

I've seen pieces of code so bad they cause real concerns of whether they actually do what they're supposed to. Still, no-one dared touch it, afraid of not only breaking intended functionality, but also breaking *bugs*. I suspect a large test set based on past I/O would be well worth its investment.

Fun fact, **the feelings of worry and happiness are inversely proportional**. Or something like that.

### Create Value with Confidence and Correct Products

Correct code is more valuable than incorrect code<sup>[citation needed]</sup>. Everything helping your code to be correcter tahn what was there before is worth taking a look at, it's as simple as that. Delivering correct code builds up trust over time, trust is a valuable asset.

## A Tool to Use Less Brain RAM

Here's a tip: **Don't try to solve a problem and engineer a nice solution at the same time.** Testing gives us that opportunity, using this recipe from [Ian Cooper's talk on TDD][tdd-where-did-it-all-go-wrong]:

  1. Write a red test.
  2. Solve the problem, make it green *as fast as possible.*
  3. Engineer a nice solution, refactor it to something you're proud of.

The important thing to take away here is the temporal separation of concerns in your head. Do not try to do steps 2 and 3 at the same time. One of the main limitations of programming is how much you can keep in your head at once, and you'll write better code the less you have to keep there when you're typing.

When solving the problem, do not think about how the code really should be. Copy-paste code, write inefficient loops, duplicate stuff, do whatever you have to do to get the test green as fast as possible. *Then* think about how to change it into something you could show your mother.

Separating your own concerns is one of the reasons to test in the first place, and this method helps doing it in practice. When you quickly and dirtily race to a solution, you don't have to think about how it looks or how fast it runs. When you carve out your nicely engineered and cool solution, you don't have to worry about screwing something up.

## Knowing What to Test is Key

Knowing what to test isn't as easy as it sounds, and is definitively something that comes with experience. Many test too much. Knowing what to test is related to understanding what is important, and what isn't, and knowing that is not trivial. Here's a tip, though:

> *Test at the highest level possible to get coverage and flexibility on implementation.*
>
> – Brian Lonsdorf, JavaScript Air 004

So, basically:

  1. **Do not test internal stuff**, it will only get in your way. If you honestly feel like you should test the internal stuff, then you might be better off separating some of the internals out to a new module, where they are external.
  2. Do not allow tests to be **over specified** and deal with stuff they don't have to or shouldn't know about.
  3. Do not write tests just to get 100 % coverage. If someone tells you to always have 100 % coverage, no matter what, then **punch them in the face**.

Remember that tests should be written *outside-in*, from a perspective outside the module. Note that full test coverage should still be possible, i.e. all branches of your code should still be reachable. If they're not, they're basically dead code, right? **Test the internals only if you need to understand better how they work.**

Think about what happens when the code is refactored, some time in the future. The implementation should be allowed to change without tests failing. Why? Well, because **if the future coder has to change the tests, then it's basically a rewrite, not a refactor. And rewrites aren't safe.** There should be no new tests for refactoring internals.

Be pragmatic about what to test. Testing is part of the project, part of the value creation, and it doesn't make sense to test just anything, just like it doesn't make sense to implement every button. Remember the documentation aspect. If tests are mostly involved with implementation details, we loose the focus on what's *important in the module*. We loose the documentation value.

About documentation, **test your domain assumptions.** These are the code interpretations of the problem domain you're working in, often something where the programmer is not the expert. Documenting these assumptions in code form solves two problems: documenting the assumptions themselves, and proving that they work as interpreted.

**Write a test when you find a bug.** Don't just fix it, but prove for yourself and others that you've actually done so. Write a test, and make sure it's both red and aligned what the expectation would be not aware of the bug. Fix the bug, make it green. Save the day.

Code coverage is overrated as a concrete number, but useful as a tool. Do not strive to cover for coverage's sake. **Remember that coverage only tells you what lines your tests run, but not in what combination they will run.** However, it can be a good indication of whether things are moving in the right direction. If a refactor leads to poorer code coverage, then some bells should be ringing, especially if it's a refactor. Do not allow yourself to write tests just to increase the number. Well tested and written code will have a higher number.

**The trigger for writing a test is when you've got a new piece of behavior.** The tests should capture that behavior, but no more.

Testing libraries might be both easier and more important than testing end applications. After all, libraries will be used by multiple applications.

## How To Write Awesome Tests

Knowing how to write good tests is key, because it's very easy to write them badly. The truth is, as with everything, that it takes practice. Still, here are some tips.

**A good test is a simple test.** It doesn't try to cover everything in one go. It's name should reflect what it does, and the name should fit comfortably in one line. The name shouldn't be *"it works"*, but rather, *"it returns 0 for negative values"*

![hello](/images/rocket-test.gif) *A good test is a simple test.*

**Make sure not to overspecify your test.** Overspecified tests know too much about internals for their own good, and don't allow refactoring.

A unit test runs the code in isolation from other tests, not necessarily other code. It takes the code out of it's context, and creates an artificial context from which an aspect of it can be investigated. However, that does not mean a unit test has to run in isolation from all other *code*, even though this is often cited as "pure unit tests". **Everything does not have to be mocked, stubbed or spied on,** leading to more complex setups, lower coverage and more fragile tests.

Use [mocks][mocks] or [stubs][stubs] where that makes sense. You don't want to test against a real HTTP API, but stub them out. Use a mock or a stub if what you're testing is your own call to that object, or if you want to lead your own code down a certain path.

**A test should read like a small story, following the AAA system: Arrange, Act, Assert.** You set things up, you make some statement, and you assert that the statement did what the statement should. The "small story" aspect has emphasis on *small*. None of the "A"-s should be more than about 3 lines. Making some space between the phases can be nice. It should contain no branching or looping, and you should assert only one logical thing. (It's nice if that can be expressed as one assert statement, but sometimes you'll need more, and that's OK.) Never assert in two different places in the test, as this causes confusion as to what you're actually testing.

**The test should be readable with only some domain knowledge.** If it does things that are hard to explain without going into the inner workings of the module, then it's probably better to either spend some more time on the test, or just leave it out completely.

Generally, don't test your dependencies. For certain projects, it may make sense to do some simple testing of the assumptions your own code is making, but do so sparingly and with caution. Testing a library is the job of the library author. Instead, rely on changelogs for upgrades, and on testing your integration against the library (this is a reason not to mock everything).

Write cheap tests that don't take forever to run, because **run them as often as possible**. If you can pass a `--watch` argument to your test runner and have it run every time a file is changed, then that's a good thing.

Last but not least, **use a test framework you like working with.** If JavaScript is your thing, I'd like to recommend [AVA 🚀][ava] because of it's clarity, simplicity and lack of complex configuration. Whatever you choose, make sure it's something that works with you and helps you write efficiently and fast. As with everything code, if it's not fun, something is probably wrong.


[jsair004]: http://javascriptair.com/episodes/2016-01-06/
[travellingsalesman]: https://en.wikipedia.org/wiki/Travelling_salesman_problem
[tdd-where-did-it-all-go-wrong]: https://vimeo.com/68375232
[mocks]: http://sinonjs.org/docs/#mocks
[stubs]: http://sinonjs.org/docs/#stubs
[ava]: https://github.com/sindresorhus/ava
