# Design Dimensions for Private Measurement Technical Specification MVP

At our September 2022 meeting at TPAC, 8 people signed up to be editors to draft a _Private Measurement Technical Specification MVP._  That group produced [Private Measurement Important Dimensions for Attribution.](README.md)

At the February 2023 in person meeting in NYC, we ran a [survey and discussed the results](https://github.com/patcg/meetings/issues/91). We were able to discuss two of these topics in detail at the meeting, and intend to continue that conversation in upcoming meetings. As part of those discussions, we had general agreement some of these design dimensions that the editors of the _Private Measurement Technical Specification MVP_ can use to make progress on the spec.

This document serves to capture the dimensions with general agreement from the Community Group and is intended to be updated as more are discussed and reach general agreement. It **does not** signify any form of final commitment from the Community Group.


# Dimensions with General Agreement


## Server Architecture may exist

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ may include server processing dependencies, however any server architecture must achieve both a high level of security (which the editors will align with the ongoing [Threat Model](../threat-model) work) as well as ability to explain the privacy and security properties of the system to end users.


## Privacy defined at least by Differential Privacy

We’ve explored three main definitions of privacy:

1. Information theoretic
2. K-anonymity
3. Differential privacy

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ should use a definition of privacy based on differential privacy. This does not preclude the use of other privacy definitions in conjunction with differential privacy, however any proposal should aim to provide differential privacy guarantees.

## Privacy budget scoping

The privacy budget scope denotes a "bucketing" of user information leakage (formally described in terms of the privacy definition) that is exposed in the private measurement designs. Proposals define a particular scope (or scopes), within which a certain amount of information can be disclosed.

The community group has reached general agreement that the _Private Measurement Technical Specification MVP_ should _at least_ include a time / interaction component to the budget scope. That is, as the user browsers the web and interacts with sites more, we would expect that the private measurement system will allow more information about that user to be disclosed.
