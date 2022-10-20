# Security Considerations


## Document Status: DRAFT

This document is currently a **draft**, submitted to the PATCG (and eventually the PATWG) with the intention of receiving broad feedback. In this state, it does not yet represent any form of consensus in either group, nor should it be seen as a specific endorsement from any contributors. This document will likely evolve significantly through the consensus process.


## Introduction

In this document, we outline the security considerations for proposed purpose-constrained APIs for the web platform (that is, within browsers, mobileOSs, and other user-agents) specified by the Private Advertising Technologies Working Group (PATWG).

We assume that an active attacker can control the network and has the ability to corrupt any number of clients, the parties who call the proposed APIs, and some (proposal specific) subset of aggregators.

In the presence of this adversary, APIs should aim to achieve the following goals:



1. **Privacy**: Clients (and, more specifically, the vendors who distribute the clients) trust that (within the threat models), the API is purpose constrained. That is, all parties learn nothing beyond the intended result (e.g., a differentially private aggregation function computed over the client inputs.)
2. **Correctness:** Parties receiving the intended result trust that the protocol is executed correctly. Moreover, the amount that a result can be skewed by malicious input is bounded and known.

Specific proposed purpose constrained APIs will provide their own analysis about how they achieve these properties.


## 1. Threat Model

In this section, we enumerate the potential actors that may participate in a proposed API, their assets (secrets that we either aim to protect the privacy of, or which confer some capability that enables further attacks on the system), their capabilities (including those of a malicious, compromised, or compelled actor), and potential mitigations which for attacks enabled by those capabilities.


### 1.1. Client/User


#### 1.1.1. Assets



1. Original inputs provided to client APIs. Clients expose these APIs to other actors below, which can modify the client’s assets, but should not reveal them.
2. Unencrypted input shares, for systems which rely on secret sharing among aggregators.


#### 1.1.2. Capabilities



1. Individuals can reveal their own input and compromise their own privacy.
2. Clients (user agent software) can compromise privacy by leaking their assets (beyond specified aggregators, when used.)
3. Clients may affect correctness of a protocol by reporting false input.


#### 1.1.3. Mitigations



1. The client/user may be able to provide certain forms of validation to mitigate either individual client’s or coalitions of clients' ability to compromise correctness. These include (but are not limited to):
    1. Bound checking via zero-knowledge proof that an oblivious representation of a value lies within certain bounds. This would provide the client/user the ability to lie or misrepresent a value, but constrains that ability.
    2. Anonymous assertion via tokens that provide some form of authorization that can be tied to a specific input. Such tokens may be provided by other parties, to assert the authenticity of a report, or reflect that a user has been recognized as an authenticated individual user in another context. These tokens must be anonymous, meaning that it cannot be attributed to a specific user (e.g. by using a cryptographic technique like blind signatures.)


### 1.2. First Party Site/App


#### 1.2.1. Assets



1. Unencrypted inputs into the proposed API.
2. First party context (e.g. first party cookies / information in first party storage.)
3. User-agent provided information (e.g. user agent string, device information, user configurations.)
4. Encrypted outputs from the proposed API.
    1. In some cases, these are somewhat separated from the other assets (i.e. encrypted outputs may be delayed, and not connected to first party context.)


#### 1.2.2. Capabilities



1. First parties can modify client assets through the proposed APIs.
2. First parties can choose which encrypted outputs from the proposed API are provided to aggregators.


#### 1.2.3. Mitigations



1. Modification of client assets should be limited by the API interface to only allow for intended modifications.
2. Use of differential privacy (see [section 3. Aggregation and Anonymization](#3-Aggregation-and-Anonymization)) should be used to prevent


### 1.3. Delegated Parties (Cross Site/App)

A “delegated party” is a party which the first party embeds within their site/app, typically as a service provider to help manage the usage of the types of activities this group looks to support.

Here, we assume that a delegated party is both embedded in a first party site/app, and that they are cross-site. We assume partitioned storage access/cookies (as the goal of the PATWG’s efforts are to build APIs that support functionality without the need for unpartitioned storage access/cookies.)

In some cases, a first party may decide to allow a service provider to run directly in their first party context. In these cases, the service provider will be assumed, under the threat model, to have all the assets and capabilities of the first party, and not those of a delegated party.

See [section 2. First Parties, Embedded Parties and Delegated Parties](#2-First-Parties-Embedded-Parties-and-Delegated-Parties) for more details on first parties vs delegated parties.


#### 1.3.1. Assets



1. All the same assets as First Parties ([section 1.2.1](#121-Assets))
    1. Exception that 1.2.1.2 would be partitioned delegated party context.


#### 1.3.2. Capabilities



1. The same capabilities as First Parties ([section 1.2.2](#122-Capabilities))
2. Delegated parties can modify client assets through the proposed APIs, which may include overwriting/modifying data provided by the first party or other delegated parties.


#### 1.3.3. Mitigations



1. The same mitigations as Third Parties ([section 1.2.3](#123-Mitigations))
2. The client should prevent the ability of a delegated party to overwrite/modify data provided by the first party or other delegated parties. If this is not possible, the client should allow the first party to control and limit a delegated party's ability in this manner. (See [section 2. First Parties, Embedded Parties and Delegated Parties](#2-First-Parties-Embedded-Parties-and-Delegated-Parties) for more details.)


### 1.4. Aggregator

An aggregator is an individual party which participates in an aggregation protocol. We assume that some (proposal specific) subset of the aggregators act honestly and do not collude with other aggregators or any other parties. Here we outline assets and capabilities assuming that an aggregator colludes up to, but not exceeding, that threshold.


#### 1.4.1. Assets



1. Unencrypted individual share.
2. All the assets from first and third parties.
3. Shares of the output of the aggregation protocol.
4. Identity of other aggregators.


#### 1.4.2. Capabilities



1. Aggregators may defeat correctness by emitting bogus output shares.
2. Aggregators (who collude with a first or third party) may learn information about the clients/users which contribute to the protocol.
3. Aggregators may compromise the availability of the system by refusing to participate in the protocol.
4. Aggregators can count the total number of inputs to the system, and, depending on the protocol, counts of shares at any point during the protocol.


#### 1.4.3. Mitigations



1. The secret sharing scheme used to provide inputs to the aggregators must ensure privacy as long as the (proposal specific) subset of aggregators do not reveal their shares.
2. The aggregation protocol should provide robust analysis that it is in fact a differentially private function (see [section 3. Aggregation and Anonymization](#3-Aggregation-and-Anonymization).)
3. Bogus inputs can be generated that encode “null” or “noop” shares, designed to not affect the aggregation protocol, but can mask the total number of true inputs.


### 1.5. Aggregator collusion

If enough aggregators (beyond the proposal specific subset) collude e.g. by sharing unencrypted input shares), then none of the properties of the system hold. Such scenarios are outside the threat mode.


### 1.6. Attacker on the network

We assume the existence of attackers on the network links between various parties.


#### 1.6.1. Capabilities



1. Observation of network traffic. Attackers may observe messages exchanged between parties exchanged at the IP layer.
    1. Time of transmission by clients could reveal information about user activity.
2. Observation of message size could allow the attacker to learn how much input is being submitted by a specific user.
3. Tampering with network traffic. Attackers may drop messages or inject new messages into communication between parties.


#### 1.6.2. Mitigations



1. All messages exchanged between parties should be encrypted / use TLS / use HTTPS.
2. All messages between aggregators and to/from first/third parties should be mutually authenticated to prevent impersonation.
3. Timing of messages should be carefully considered as not to leak anything beyond what the attacker would learn absent the proposed API.


## 2. First Parties, Embedded Parties, and Delegated Parties

The use cases considered by the PATWG involve combining data across two (or possibly more) distinct first parties. (Use cases that involve a single first party should be supported by first party storage access/cookies, and out of scope for the PATWG.) Quite often this involves either one first party being embedded in the other first party’s site/app, or (possibly more commonly) an additional “delegated party” being embedded on both first parties’ sites/apps.

Two sites/apps are distinct first parties ([cross-site](https://tess.oconnor.cx/2020/10/parties)) within a web browser when they have distinct registrable domains (i.e. eTLD+1.) It’s important to note that this definition can apply to where content is acquired as well as where code is executed. For our purposes, we are concerned primarily with the execution context. As such, APIs this group designs should consider the ability of delegated parties to run within embedded context, and avoid creating an incentive for first party’s to directly embed service providers within their own context.

This comes with certain considerations that need to be made, particularly because a first party may embed multiple delegated parties into their site/app. As such, proposed APIs must take into consideration interactions between delegated parties, such as (but not limited to):



1. The ability for one delegated party to corrupt the results of another delegated party.
2. The ability for one delegate party to disable functionality of another delegated party, such as exhausting various limits put in place.
3. The ability to delegate to multiple parties and bypass privacy budget restrictions


## 3. Aggregation and Anonymization

Any output from the proposed APIs must avoid leaking information about any individual clients/users. To achieve this the output should leverage the maximum possible level of aggregations across multiple clients/users. They should further provide anonymity, meaning that the ability to infer individual client/user information is limited, even across queries – this property can be achieved by requiring a differential privacy guarantee for the output.


### 3.1 Differential Privacy

A differentially private output limits the inference that can be made from two queries which differ by an individual input. For example, calculating typical aggregation functions like _sum_ and _mean_ across two sets which differ by a single element will tell you the exact value of that element (making those functions entirely non-differentially private.) Making a function differentially private typically requires two factors:



1. Sensitivity of the function (the amount the aggregate can be influenced by a single input) needs to be bounded, and
2. Random noise, proportional to the sensitivity needs to be added.

Differential privacy is parameterized by ε, which measures the amount of individual differential privacy leakage, and the noise is inversely proportional to ε.


#### 3.1.1 Privacy Budgeting

The amount of this individual differential privacy leakage accumulates across every output from a system, and as such we typically manage such a system with a “budget”. Such budgets are typically managed over discrete units of time called “epochs”.

Note that over infinite epochs, infinite information is released. Our goal is not to prevent the divergence of differential information release in the limit, but rather to control how quickly that release diverges.


### 3.2 Unit of Privacy

When managing a privacy budget, that budget is assigned to some party who can use it to produce differentially private outputs from the system. For example, the budget could be managed per individual first parties, per pairs of first parties, or other sets of parties. The choice of this unit will also affect the total amount of differential information released from the system. In our earlier example, if P is the number of first parties, then managing per first party results in an O(P) release, and managing per pair of first party results in an O(P<sup>2</sup>) release.


### 3.3 Privacy Parameters

This results in four parameters which need to reach consensus in the PATWG:



1. _Aggregation minimum threshold_: the number of clients/users that need to be included in a given aggregation.
2. _Epoch_: The amount of time over which a differential privacy budget should be managed.
3. ε: The differential privacy parameter which measures the amount of individual differential information leakage allowed in each epoch.
1. _Unit of privacy_: The set of parties used to manage the privacy budget.
