# Third Party Baseline

**Document status**: Draft

**Authors**: Erik Taubeneck

**Original Issue**: [meetings/141](https://github.com/patcg/meetings/issues/141)

---

## Context

The private measurement APIs discussed by the PATCG all require some type of browser interaction (e.g., calling an API) by the website (either the _source site_ or _trigger site_.) It is often the case that the website operator will decide to use a _third party service provider_ to perform these interactions on their behalf.

The proposed private measurement APIs all include some form of budgeting or rate limiting to achieve their privacy goals. This presents challenges with respect to _third parties_.


## Goals

We aim to achieve the following in our API designs, but also recognize there are trade offs to consider.



1. Top-level site owners _must_ be able to delegate usage of the measurement API to _third party service providers._
2. Top-level site owners _should_ be able to utilize multiple _third party service providers._
    a. It _may_ be reasonable to standardize an upper limit on the number of _third party service providers._
3. The utilization of multiple _third party service providers_ _must not_ result in a reduction in the privacy afforded by the API.
    a. The utilization (or general support for) multiple _third party service providers_ should not result in a reduction in the utility afforded by the API.
4. The measurement APIs _must not_ require top-level context to operate, e.g., they should be accessible within an iFrame or similarly isolated context.
    a. A site _may_ restrict access to APIs for _third party service providers_ so that only their preferred providers can use the APIs.
5. It _must not_ be possible for one _third party service provider_ to actively degrade or prevent the operation of another _third party service provider_.
    a. It _may_ be possible for the top-level site to configure a state that degrades or prevents the operation of some _third party service providers_, e.g., by embedding too many providers.
6. By default, it _must_ be possible for an embedded _third party service provider_ to operate the measurement APIs without requiring the top-level site to take action (beyond embedding the _third party service provider_.)
    a. It _must_ be possible for sites to override these defaults (see 4a.)
7. Whenever possible, the measurement APIs _should_ utilize reasonable defaults but _may_ allow for more precise configuration.
    a. Sites that rely on defaults _should not_ receive significantly less utility from the API.


## Key Issue: Privacy Budget Management

Currently, all the proposed measurement APIs utilize some form of a _privacy budget_, which determines how differentially private noise is applied to outputs of the API. Utilization of the API deducts from that budget over the course of an epoch, and if fully consumed, the API cannot be used until the next epoch. In order to achieve goal #&#x2060;3, this budget needs to constrain the usage across all _third party service providers_, which implies that the budget must somehow be allocated between them. This allocation presents a particularly complex set of trade offs between these goals.

For example, if the API allows the budget to be consumed arbitrarily at each invocation, the first invocation may consumer the entire budget, conflicting with goal #&#x2060;5. Conversely, we could require top-level site owners to assign portions of their budget to each _third party service provider_, however this conflicts with goal #&#x2060;6. We could additionally add a default and assume that most sites will use N _third party service providers_, and by default assign each with 1/N of the budget (unless otherwise configured.) Deciding on a value for N is non-trivial: too large an N and we conflict with goal #&#x2060;7a; too small and we conflict with goal #&#x2060;2.
