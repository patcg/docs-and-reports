# Design Dimensions for Private Measurement Technical Specification MVP

At our September 2022 meeting at TPAC, 8 people signed up to be editors to draft a _Private Measurement Technical Specification MVP._  That group produced [Private Measurement Important Dimensions for Attribution.](README.md)

At the February 2023 in person meeting in NYC, we ran a [survey and discussed the results](https://github.com/patcg/meetings/issues/91). We were able to discuss two of these topics in detail at the meeting, and intend to continue that conversation in upcoming meetings. As part of those discussions, we had general agreement some of these design dimensions that the editors of the _Private Measurement Technical Specification MVP_ can use to make progress on the spec.

This document serves to capture the dimensions with general agreement from the Community Group and is intended to be updated as more are discussed and reach general agreement. It **does not** signify any form of final commitment from the Community Group.


# Dimensions with General Agreement


## Server Architecture may exist

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ may include server processing dependencies, however any server architecture must achieve both a high level of security (which the editors will align with the ongoing [Threat Model](../threat-model) work) as well as ability to explain the privacy and security properties of the system to end users.

More generally, the community group is willing to engage with proposals that use different mechanisms to protect data while it being processed on servers, explicitly including both Multi-Party Computation (MPC) and Trusted Execution Environment (TEE) server-side components.
These technologies each have shortcomings, but they can form part of a larger system of protections that includes mitigations for the different vulnerabilities of its constituent components.
The group could consider both technical and procedural mitigations.

If the _Private Measurement Technical Specification MVP_ supports both an MPC-based and a TEE-based implementation, then every effort should be made to design them to be cross-compatible, to minimize the engineering burden and maximize utility for API users who choose to engage with both implementations.


## Privacy defined at least by Differential Privacy

We’ve explored three main definitions of privacy:

1. Information theoretic
2. K-anonymity
3. Differential privacy

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ should use a definition of privacy based on differential privacy. This does not preclude the use of other privacy definitions in conjunction with differential privacy, however any proposal should aim to provide differential privacy guarantees.

### Private measurement of single events
The group has discussed the use-case of “differentially private measurement of single events”. Specifically, whether, given differential privacy protections, we would permit queries which ask about the outcome for a single event (did impression lead to a conversion, for instance). For more information about this, see https://github.com/patcg/docs-and-reports/issues/41 and the [agenda topic](https://github.com/patcg/meetings/issues/112) from the May 2023 meeting.

We have reached consensus that these types of queries can be particularly sensitive, so we must take care when setting the proper [differential privacy scope](#privacy-unit--privacy-budget-scoping) and parameters (e.g., epsilon and delta) to ensure that user contributions are adequately protected. Any protection needs to cover people that submit many events within the chosen privacy scope. However, queries of this form will not necessarily be prohibited, although additional protections will be considered if differential privacy guarantees are deemed too weak.

## Privacy unit / privacy budget scoping

A privacy budget scope denotes a boundary for user data leakage, formally described in terms of a privacy definition, which is allowed by a private measurement design. Proposals define a scope, or scopes, within which a limited maximum amount of data may be disclosed.

### Inclusion of a time or interaction dimension

In order to be useful, measurement systems need to produce a continuous release of information about user activity. Measurement systems that provide absolute limits on information release would degrade in utility, until at the limit only supporting measurement for brand-new users. Instead, measurement systems must limit the _rate_ of information that is released.

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ should _at least_ include a time or interaction component to the budget scope. That is, as the user browses the web and interacts with sites more, we would expect that the private measurement system will allow more information about that user to be disclosed.

It is worth noting that, at least among existing differential privacy deployments, a time dimension in the privacy unit is very common. See https://desfontain.es/privacy/real-world-differential-privacy.html for a list of examples here.
