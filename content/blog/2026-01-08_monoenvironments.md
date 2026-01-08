+++
title = 'Staging is a wasteful lie: the case for the mono-environment'
date = 2026-01-08T06:00:00Z
description = "Staging doesn't catch bugs, slows delivery, and makes developers miserable. Ship straight to production instead."
[params]
	licence = "CC-BY-4.0"
+++

It is common for software engineering teams to deploy new versions of their software to staging environments before deploying it to users in production. In this post I argue that non-production environments are inherently wasteful and describe an alternative: the *mono-environment*.

I was first introduced to the concept of a mono-environment in 2018 at a SaaS startup. We had a solid engineering team, but were frustrated by slow delivery. After examining all our options, we realised that the costs of a staging environment outweighed the benefits, so we got rid of it, doubled down on our effective practices, and focussed on production.

Since then I've only worked in mono-environments, mostly at fintech startups, and even a bank. I now think it should be the default approach used by developers, especially at startups trying to achieve product-market fit.

Some former colleagues published a [one-page website on mono-environments](https://monoenv.tech/), but otherwise there is little to read about the case for a mono-environment and how to work safely in one. If anything, "testing in production" is a euphemism for sloppy and unprofessional practices. This article sets out to correct that.

# Why are staging environments wasteful?

Your users can only use and get value from production, therefore any effort that goes into delivering software to staging is inherently wasteful. For many developers staging is their primary deployment target, so staging also presents a misalignment of incentives between developers and users.

You could argue that users *indirectly* benefit from staging because it's a tool for inspection, i.e. checking functionality and looking for bugs. But this is still problematic because you can't inspect quality into the software development process.[^Deming] By the time a change reaches staging, if the quality isn't there, then it's a choice between shipping it anyway, re-working it, or binning it. All of these are wasteful. Instead, the quality needs to be there from the start.

Using staging as a quality gate also reduces the throughput of changes to production, thereby lengthening user feedback cycles and delaying delivery of value to users.

Staging environments are also unreliable quality gates because they are unrepresentative of production in two key ways. Firstly, staging is usually under lower load than production. Secondly, the data in staging is often unrepresentative of data in production, so a change might have the desired behaviour in staging but fail in production when the application processes real-world data. In my experience, this is particularly problematic with AI systems. So for staging to be a reliable indicator, it has to be very similar to production, but in practice each step towards parity gets progressively harder and more expensive.

Long-lived non-production environments have extra infrastructure costs. Configuring smaller resources for staging adds complexity and further diverges staging from production, making it an even more unreliable indicator of change quality. 

Your developers also have the cognitive overhead of working with staging. What other changes are in staging? Has someone released my change to production? Of course, there are ways to manage this, but it is additional effort that could be spent on something your users care about.

Let's not forget the misery of restarting work you thought you had finished weeks ago, because it was finally deployed to production and someone found a bug.

All the above is made much worse when you have multiple non-production environments. I once had a client where one team had *five* environments for their application. Things didn't go well for them.

Overall, staging gives a false sense of security and creates a poor developer experience. It encourages large batch sizes, lowers throughput to users, and delays feedback. In a startup, all of this presents an existential threat because it reduces the rate of learning from users and increases cash burn.

# What if we only operate production?

If we get rid of staging and only operate a production environment – a mono-environment – then how can we do so safely? Specifically, we want to maintain quality, reduce risk, and minimise waste. We need to replace *environmental* isolation with *logical* isolation, which means moving safeguards into the code.

Practically, we can do this by taking existing software development practices like testing, feature flagging and continuous deployment, and doing them well to work in fast feedback cycles. Here are five practices I think are key for a successful mono-environment.
## Practice 1: testing

*Use automated tests to ensure your change has the desired behaviour, and doesn't break existing behaviour, before deploying to production.*

We need to be confident that a change has the expected behaviour. We can get that confidence with automated tests. At risk of alienating some of you: use [test-driven development](https://martinfowler.com/bliki/TestDrivenDevelopment.html) (TDD). It gives you rapid feedback, so you know you're moving in the right direction. The test suite guards against regressions and is also a form of documentation.

TDD is a skill. A common mistake doing TDD is [testing implementation instead of behaviour](https://www.youtube.com/watch?v=EZ05e7EMOLM), which makes for brittle tests that fail en masse when you refactor code. Another mistake is writing [non-deterministic](https://martinfowler.com/articles/nonDeterminism.html) ("flaky") or slow tests, which give you unreliable, slow feedback.

It's also important to write tests of the right granularity: a [unit test](https://martinfowler.com/bliki/UnitTest.html) checks the behaviour of a single module, an [integration test](https://martinfowler.com/bliki/IntegrationTest.html) checks multiple modules function together correctly, and a smoke test checks basic functionality for a user (e.g. logging in and seeing the home page). The right mix will depend on your specific requirements and application architecture; further discussion is beyond the scope of this post.

## Practice 2: local environments  

*Use ephemeral local environments to boost confidence in a change and get feedback via demos.*  

Even with TDD, you might miss an edge case or simply assert the wrong behaviour in your tests, so it's useful to be able to do manual tests. It's also useful to be able to demo a change to a colleague or customer for feedback as early as possible during development.

Ephemeral local environments are useful for these situations. A local environment won't tell you exactly how your change will behave in production, much like staging wouldn't, but they are still a useful tool to increase your confidence in a change without all the downsides of a long-lived non-production environment. They're great for debugging too.

Set up a script to launch your local environment with one command. Check it into your repository so everyone can use it. The extent to which you can set up a complete local environment will likely depend on your architecture. If you're using containers and have a monolithic backend and React frontend, then it's trivial to bring up an entire app with a tool like Docker Compose. In a distributed system, it's likely to be harder, so you might only bring up the service of interest and use stubs for everything else.
## Practice 3: feature flagging

*Use feature flags to test new functionality in production for a subset of users.*

Feature flags allow you to turn on or off functionality in production based on some criteria, like the user making the request, without a deployment:

```python
if featureFlags.get("canViewNewFeature", user):
    newFeature()
```

This is useful in a mono-environment because you can develop a feature in private behind a feature flag and find out how it behaves in production. I typically configure a new flag so that only colleagues can see the functionality, then when I'm happy with the change, I'll either turn it on for everyone or do a gradual roll-out.

Lots of companies offering feature flag software for reasonable prices. Pick one you like. 

It's tempting to feature flag every change, but I advise against it. In startups, iteration speed is key, and in a mono-environment, it's easy and fast to revert a change or deploy another, so more often than not I tend not to bother with a flag since it gets me feedback from more users at lower cost (because no time is spent on flag management). But for incomplete features or uncertain changes, I'll use one.

In your tests, mock the feature flag provider so that you can specify the flag state for any given test.

## Practice 4: continuous deployment

*Use continuous deployment to ship changes quickly and seamlessly.*

Shipping software is a key activity in software development. Frictionless deployment means faster feedback and delivery of value. 

Continuous *integration* is the practice of developers integrating their code changes into the main branch of the repository frequently (at least daily) and building and testing the software on every commit. Continuous *delivery* is the practice of being able to deploy a new version of the software at a moment's notice. (So continuous integration is a prerequisite for continuous delivery.) In a mono-environment, we use continuous *deployment*, which is the practice of automatically deploying your software on every change.

For continuous deployment, you typically need two pipelines. The first *build and test pipeline* runs on every commit and builds the software and runs the unit and integration tests. On the main branch, after a successful build and test run, the second *deployment pipeline* deploys the software.

Both need to be fast and reliable. I find it hard to tolerate anything longer than a few minutes. It's essential to keep the build and test pipeline green, otherwise your deployments are blocked, which delays feedback on new changes and fixes for issues.

I also like to run smoke tests against production after every deployment to check I haven't broken basic functionality like logging in and seeing the expected home page. These tests should use a dedicated test user/tenant.

Overall, deployments should be business-as-usual and completely ordinary events.

## Practice 5: monitor production

*Use monitoring and observability tools to know what's happening to your users when they use your software.*

You need to know what's happening in production because it's what your users experience. It's what actually matters. If they are getting errors or performance problems, then you need to fix it fast, or at least be able to proactively get in touch before they contact you. Think about problems like uncaught exceptions and unexpected 400/500 HTTP response codes in logs. If you have a small number of users, then you might fire alerts every time, otherwise only when errors cross thresholds. Also track application events, so you can understand what your users are doing.

The technical approach to take depends on your particular situation. If you have a monolithic architecture and low number of users then you might be fine with log-based monitoring. On the other hand if you have a distributed system under high load, you might need more sophisticated observability tooling. I can't really comment on the latter, as I've not worked on a distributed system with it set up (which caused me many headaches manually tracing requests through the logs of different services), so [listen to an expert like Charity Majors instead](https://newsletter.pragmaticengineer.com/p/observability-the-present-and-future).

## Summary

All the techniques so far are about incrementally increasing the confidence in your change by repeatedly running through feedback loops. During development, each practice enables lots of frequent and fast feedback cycles, thereby minimising risk and increasing confidence in delivering value for users. 
# Common objections

## Compliance requires us to have a staging environment

Verify this for yourself. On many occasions I've been told that a piece of legislation or regulation requires something specific, then when I've looked it up myself I've found the requirements to be much more flexible. Understand the regulations, work out how you can meet them, and discuss it with the relevant experts. For example, you might be able to argue that automated testing, continuous deployments, and comprehensive monitoring are more effective controls than infrequent manual checks in a staging environment.

## What about database migrations?

Database migrations make people nervous in a mono-environment, but it's worth remembering that a successful migration in staging doesn't mean it will work in production. For example, a migration might run in tens of milliseconds in staging, but take much longer on a larger production database and cause an outage. Production databases often contain old, unusual data not present in staging, which could cause unexpected surprises.

Work in a pair and write down a plan. Consider what could go wrong and work out how you would roll back. Look at the query plan. Ensure you know what to do to recover from a disaster. Migrations should also be part of your continuous deployment pipeline.

Often, you need to use the [expand and contract pattern](https://martinfowler.com/bliki/ParallelChange.html). For example, let's say you want to rename a column:

1. Migration 1: add the new column (expand).
2. Deploy a new version of your app that writes to the new column, and reads from the new and old columns, e.g. `coalesce(new_col, old_col)`. 
3. Migration 2: backfill any null values in the new column from the old column.
4. Deploy a new version of your app that reads and writes only from the new column (contract).
5. Migration 3: drop the old column. Optionally, make the new column `not null`.

# Incremental delivery goes hand in hand with a mono-environment

>Deliver working software frequently, from a couple of weeks to a couple of months, with a preference to the shorter timescale.
>
> — [Agile Manifesto principle #3](https://agilemanifesto.org/principles.html)

Big bang releases are high risk because you find out if you're right or wrong [late in the development process](https://web.archive.org/web/20140329210405/http://alistair.cockburn.us/Trim+the+Tail). In contrast, incremental delivery (small, frequent changes) enables early feedback before it is too late to change tack.

*[Elephant carpaccio](https://web.archive.org/web/20140329203040/http://alistair.cockburn.us/Elephant+carpaccio)*, coined by Alistair Cockburn, is a useful tool for thinking about incremental delivery. Carpaccio is an Italian dish of meat sliced so thin you can almost see through it. (I'm a vegetarian, so I've never tried it.) In software carpaccio, developers take a user story – the elephant – and slice it so thin that they can deliver a slice each day or, even better, several slices each day. To get a feel for this, [watch Douglas Squirrel talk about delivery of a colour picker using elephant carpaccio](https://www.youtube.com/watch?v=EKRD-2kiC10).

Elephant carpaccio, and incremental delivery, are a natural fit for a mono-environment, because a mono-environment enables seamless delivery of each slice into the hands of users, and creates a tight feedback loop.

# Conclusion

Staging and other non-production environments give a false sense of security. Time spent on them, instead of delivering value to users, is a waste of time and money.

Instead of relying on environmental isolation, the mono-environment relies on logical isolation by moving safeguards into the code. It achieves safety through rigorous, automated software engineering practices and aligns all developer effort with the environment that generates value and revenue: production.

Counterintuitively, the speed of delivery in a mono-environment is *safer* than using a staging environment, because incremental delivery in a single environment leads to tighter feedback loops and more learning, and a higher chance of achieving product-market fit before cash runs out. A mono-environment creates a virtuous cycle of faster and more robust delivery.

Lastly, I want to note how enjoyable it is to work in a mono-environment. It's not unusual for my current team of 6 to have 30+ production deployments (including infrastructure changes and database migrations) and multiple customer conversations in a single day. Shipping so frequently can be hard to imagine if you've not done it before. I love technology, but what I love even more is shipping software people find useful, and a mono-environment helps me do that.

_Thanks to [Mike Hancock](https://www.linkedin.com/in/michael-hancock-6790a32/) for feedback on a draft of this post._

[^Deming]: I first heard the phrase "you can't inspect quality into a process" from Dave Farley on his [Modern Software Engineering YouTube channel](https://www.youtube.com/channel/UCCfqyGl3nq_V0bo64CjZh8g). I looked into its origin and apparently it is a [quote from the statistician Harold F. Dodge](https://deming.org/inspection-is-too-late-the-quality-good-or-bad-is-already-in-the-product/).
