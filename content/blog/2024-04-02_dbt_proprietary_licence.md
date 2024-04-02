+++
title = "Will dbt adopt a proprietary licence? I think so"
slug = "dbt-proprietary-licence"
description = "Analysis of dbt Labs' business model and prediction that dbt will adopt a proprietary licence."
date = "2024-04-02"
[params]
	licence = "CC-BY-4.0"
+++

[Data build tool](https://www.getdbt.com/) (dbt) changed the way many organisations transform data and do analytics. I think more change is afoot: I predict dbt Labs will adopt a proprietary licence for dbt Core and the project will fork.

dbt Labs use the [loose open core](https://sfosc.org/docs/business-models/loose-open-core/) business model, where the primary functionality in [dbt Core](https://github.com/dbt-labs/dbt-core) is covered under an open source licence ([Apache 2.0](https://github.com/dbt-labs/dbt-core/blob/main/License.md)) with extra proprietary software wrapping around it, namely [dbt Cloud](https://www.getdbt.com/product/dbt-cloud).

The open core creates a large funnel of potential customers for the proprietary product. The difficulty with the open core business model lies in deciding whether a feature goes in the open core or proprietary product. The company wants to add functionality to the proprietary product to drive conversion of users to customers, but this [often conflicts with the interests of the users of the open core](https://medium.com/@adamhjk/goodbye-open-core-good-riddance-to-bad-rubbish-ae3355316494). Despite this conflict of interest, the open core depends on the success of the the proprietary product to fund its development.

In dbt's case, I think dbt Cloud isn't a compelling product. Despite using dbt Core for years I have never bought dbt Cloud. I don't know of any organisations who use it; I know of one who stopped.

A recent [job advert](https://boards.greenhouse.io/dbtlabsinc/jobs/4343828005) says dbt Labs have over 4,100 dbt Cloud customers. At [$100/month](https://www.getdbt.com/pricing) that's ~$5M/year. Maybe they have some big enterprise deals, but that revenue is probably dwarfed by payroll. LinkedIn lists 421 members associated with dbt Labs. A conservative estimate of 300 employees at $100k/year is $30M/year, which would be a loss of ~$25M/year. Not great for [an eight year old company valued at $4.2B](https://www.prnewswire.com/news-releases/dbt-labs-raises-222m-in-series-d-funding-at-4-2b-valuation-led-by-altimeter-with-participation-from-databricks-and-snowflake-301489733.html)

What can dbt Labs do? [They already cut headcount by 15%](https://www.getdbt.com/blog/consumption-based-pricing-and-the-future-of-dbt-cloud) and 
[switched to consumption-based pricing](https://www.getdbt.com/blog/consumption-based-pricing-and-the-future-of-dbt-cloud). Can they ship some compelling features? I'm not convinced. [Glassdoor reviews](https://www.glassdoor.co.uk/Reviews/dbt-Labs-Reviews-E2969503.htm) (taken with a large pinch of salt) complain about leadership and the product roadmap. In an [interview](https://www.youtube.com/watch?v=cuLXwUypAC8) last December, their CEO Tristan Handy said:

>\[W\]hat I want to do is increasingly make living in the dbt ecosystem feel like living in the Apple ecosystem, which is to say that the hard stuff \[like CI, observability, data cataloging\] just kind of vanishes into the background. And you forget that that was ever a problem and you spend all your time thinking about business value.

This is a nice idea but it will be hard to execute. He acknowledged that comparing dbt Labs with Apple is a stretch, but putting that aside, the revenue models are completely different: people pay Apple $1000 for an iPhone and then pay even more for services (to the EU's chagrin). In contrast, entry into the dbt ecosystem is free and users spend money elsewhere (e.g. on a cloud data warehouse or managed data transfer services).

By Tristan's own admission, and unlike Apple, dbt Labs' products aren't even best-in-class:

>So we just launched this thing called dbt Explorer. And you look at dbt Explorer and you compare it to a fully featured data catalogue ... and I mean, gosh, it's like David and Goliath, like, our product is kind of a toy. But it does a lot of things, the basic things that you want, and it is automatically in the hands of everybody who is in the dbt ecosystem. And that's really powerful. And so we want to continue to do that, we're not going to try to build the most complex best-in-class orchestration tool or best-in-class observer. But when you have a huge user group, and you can say, hey, here's a really useful thing, and it works with all these other things, then you get to kind of raise the bar on the set of tooling that everyone has access to.

I can’t see how this will lead to a sustainable open source business. Even if they do convert a fraction of their users to customers, these customers are then at risk of churning to a more sophisticated product in the future.

What might happen next? dbt Labs coined "analytics engineering" and if you're analytics engineer practicing what dbt Labs preach, you probably have a lot of models. What surprises me is that [5% of the dbt install base has over 5000 models](https://www.getdbt.com/blog/analytics-engineering-next-step-forwards)! These customers are sitting ducks, locked-in to dbt.

How can dbt Labs get money from those users? By pulling the [same move as HashiCorp](https://www.hashicorp.com/blog/hashicorp-adopts-business-source-license) and switching new releases of dbt Core to the [Business Source License](https://mariadb.com/bsl11/) (BuSL), which would block competitors offering products based on it.

dbt Labs then release new features to tame huge dbt projects now efficiency and cost optimisation are in vogue and perhaps they will start to see more customers.  Meanwhile, they can claim to be committed to open source, despite the BuSL not being an [OSI-approved licence](https://opensource.org/licenses).

Coincidentally, [HashiCorp's former chief revenue officer Brandon Sweeney is now dbt Labs’ chief operating officer](https://www.getdbt.com/blog/dbt-labs-appoints-tech-veteran-brandon-sweeney-as-president-and-chief-operating-officer).

If this does happen, a fork of dbt core is in the interests of all the vendors selling "dbt compatible" products, much like businesses who got together to create the [OpenTofu](https://opentofu.org/) fork of Terraform. I think it would be good for users too. I am surprised by how long dbt has dominated the transformation part of the data stack. More competition and diversity of thought in the data industry is good for everyone.

I think dbt Labs should have stuck with their original consultancy business Fishtown Anayltics and made dbt a [free software product](https://sfosc.org/docs/business-models/free-software-product/), charging their clients for a proprietary distribution. But that business model probably wouldn't go down well with their investors. Unfortunately I see dbt ending up being yet another example of the consequences of choosing the wrong open source business model.