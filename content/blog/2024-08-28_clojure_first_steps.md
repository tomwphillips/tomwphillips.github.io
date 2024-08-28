+++
title = 'Learning Clojure: first steps'
date = 2024-08-28
description = "A Python developer's first steps with Clojure" 
[params]
	licence = "CC-BY-4.0"
+++

## Why Clojure?

It's been longer than I'd like since I learnt a new programming language. I mostly write Python at work and I wanted to learn something substantially different. I narrowed it down to [Elixir](https://elixir-lang.org/) or [Clojure](https://clojure.org/). I chose Clojure for a few reasons:

* I've never learnt a [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)) before.
* [Clojure runs on the JVM](https://clojure.org/about/jvm_hosted), so you get Java interoperability. At work I've been thinking about Kafka, which has much better support for Java compared to Python, so this is appealing.
* The author of Clojure, Rich Hickey, is hugely influential and I enjoy his talks. [*Simple Made Easy*](https://www.youtube.com/watch?v=LKtk3HCgTa8) is a favourite of mine.
* Elixir seems more focussed on highly concurrent applications, which I generally don't write, so I thought Clojure will be more useful to me.
* My manager sung Clojure's praises.

[Inspired by Julia Evans](https://jvns.ca/blog/2021/05/24/blog-about-what-you-ve-struggled-with/) I decided to write down and share what I have learned and struggled with so far.

I like learning by reading. I dipped in and out of the [Clojure getting started guide](https://clojure.org/guides/getting_started), [Clojure for the Brave and True](https://www.braveclojure.com/) and [Programming Clojure](https://pragprog.com/titles/shcloj3/programming-clojure-third-edition/). I wasn't keen on the style of "Brave Clojure", but I did enjoy the [intro to projects and namespaces](https://www.braveclojure.com/getting-started/). It introduces the project automation tool [Leinigen](https://leiningen.org/), which seems a bit like [Pipenv](https://pipenv.pypa.io/en/latest/) or [Poetry](https://python-poetry.org/) in Python. When I get started with a new language I like to figure out early on the idiomatic way to organise projects, so with `lein new app my-project` I was sorted.

As a card-carrying member of the test-driven development (TDD) club I started trying to figure out how to write tests. I took a look at the tests in [xtdb](https://github.com/xtdb/xtdb/tree/main) and [clojure.test docs](https://clojure.github.io/clojure/clojure.test-api.html). I can't remember how, but I discovered that most Clojure developers don't do TDD like you would with `pytest` or `go test`, but they use "REPL-driven development". I enjoyed [Sean Corfield's talk on the topic](https://www.youtube.com/watch?v=gIoadGfm5T8).

Sean used VSCode. I like PyCharm with [IdeaVim](https://github.com/JetBrains/ideavim), so I downloaded [IntelliJ](https://www.jetbrains.com/idea/) and the [Cursive plugin](https://cursive-ide.com/) for Clojure. [Starting a REPL is somewhat buried](https://cursive-ide.com/userguide/first-repl.html) but once you have it running you can evaluate the *form* under your cursor with cmd+shift+p.

I thought I had gone mad when my IntelliJ wouldn't let me delete parentheses. It turned out something called [paredit](https://cursive-ide.com/userguide/paredit.html) was enabled, which enables [*structural editing*](https://clojure.org/guides/structural_editing). Structural editing takes advantage of Clojure code itself being data (an idea I'm not convinced I understand on a deep level) and all the nested forms (`(...)`, `{...}`, `[...]`) forming a tree. But being a beginner I turned it off so I could delete my unwanted bracket and be on my way. It might have been to do with [Cursive and IdeaVim not working well together](https://github.com/cursive-ide/cursive/issues/1249).

Turning off structural editing turned out to be a poor decision and something I'd switch back on, but that's another post.

