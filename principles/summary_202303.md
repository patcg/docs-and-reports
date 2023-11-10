# private ad tech privacy principles summary

## Areas to cover

* consent
* control
* profiling
* distress & intrusion
* relevance
* reporting and context †
* transparency
* security
* trust model
* explainability / comprehensibility
* competition †
* inferences
* identifiers
* accountability †
* 

### Some Privacy Principles (W3C TAG Draft Note) to elaborate on

#### context / identity

> A user agent should help its user present the identity they want in each context they are in.

#### minimization

> Sites, user agents, and other actors should minimize the amount of personal data they transfer between actors on the Web.

> Web APIs should be designed to minimize the amount of data that sites need to request to carry out their users' goals and provide granularity and user controls over personal data that is communicated to sites.

> In maintaining duties of protection, discretion and loyalty, user agents should share data only when it either is needed to satisfy a user's immediate goals or aligns with the user's wishes and interests.

#### general preferences

> Sites and user agents should seek to understand and respect people's goals and preferences about use of data about them.

> Specifications that define functionality for telemetry and analytics should explicitly note the telemetry and analytics use to facilitate modal or general user choices.

#### intrusion

> User agents and other actors should take steps to ensure that their user is not exposed to unwanted information.

> A user agent should help users control notifications and other interruptive UI that can be used to manipulate behavior.

> Web sites should use notifications only for information that their users have specifically requested.

## How this fits in

* Helping to guide and evaluate private advertising technology proposals

> This document elaborates on the W3C TAG's Privacy Principles [Privacy-Principles]. The latter document is intended to describe principles of privacy that apply across the Web, and therefore leaves the door open to a variety of approaches so that different use cases can be approached with some flexibility. This document is therefore more specific in detailing how the Web's broader privacy principles are to be understood in an advertising context. 

* Not every topic should or could be covered here!

* [some source documents](https://github.com/npdoty/patcg-docs/blob/principles-sources/principles/sources.md)

## What's still missing?

* ...
* 

## How to contribute

* github issues
* PRs on documents
* biweekly check-in calls?

---

Suggested topics for principles:

continuous release of information over time

whether consent plays into that

collusion and what is trusted

aggregation for measurement

minimize the cross-context data about any user when providing measurement data

limit the ability of sites to perform cross-context recognition

separate the context from the user

privacy definitions (information-theoretic, differential privacy)


--

Some notes on how PATCG would like principles documentation to work, from March 2023 meeting:

explain how advertising-related topics may be aligned with user interests, or be an area where a user could have a general preference
    shivan: shouldn't be a trade-off or discuss that in *this* doc
    martin: explaining to users and others why we are doing what we are doing
        why, and how we approached the problem

aram: less privacy principles, and more our principles as a group about how to deal with proposals. like what tradeoffs we can make and why. what makes a healthy web and a functional user experience across the web, how that interacts with how people understand their privacy.

mt: we ultimately want here is for the people working on the document to say "we think that we have identified a principle based on our engagement with $proposal, we'd like to discuss how we refine and capture that principle so that we can apply it to other work in the group"

regular checkin with the patcg, and specific to the technology/specifications under development

where advertising fits into the priority of constituencies

james aylett: state that trade-off or compromise is going to be necessary, but don't make the trade-offs or balance itself because that's specific to a proposal. risk management in individual proposals.

well-lit path to mitigate the demand for covert tracking; make it possible to implement stronger mitigations

charlie: editorial eye towards what belongs or not. maybe collect/save for later topics that could come up but aren't very specific to current proposals in patcg/individual drafts. specific criteria for inclusion.

charlie: diff view or where we want to refine more general TAG principles
