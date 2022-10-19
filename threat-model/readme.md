# Security Considerations


## Document Status: DRAFT

This document is currently a **draft**, submitted to the PATCG (and eventually the PATWG) with the intention of receiving broad feedback. In this state, it does not yet represent any form of consensus in either group, nor should it be seen as a specific endorsement from any contributors. This document will likely evolve significantly through the consensus process.


## Introduction

In this document, we outline the security considerations for proposed purpose-constrained APIs for the web platform (that is, within browsers, mobileOSs, and other user-agents) specified by the Private Advertising Technologies Working Group (PATWG).

Many of these proposals attempt to leverage the concept of _private computation_ as a component of these purpose-constrained APIs. An ideal private computation system would allow for the evaluation of a predefined function (i.e., the constrained purpose,) without revealing any new information to any party beyond the output of that predefined function. Private computation can be used to perform aggregation over inputs which, individually, must not be revealed.

Private computation can be instantiated using several technologies:

* Multi-party computation (MPC) distributes information between multiple independent entities using secret sharing.
* A trusted execution environment (TEE) isolates computation and its state by using specialized hardware.
* Fully homomorphic encryption (FHE) enables computation on the ciphertext of encrypted inputs.

Though the implementation details differ for each technology, ultimately they all rely on finding two entities - or _aggregators_ - that can be trusted not to conspire to reveal private inputs. The forms considered by existing attribution proposals are MPC and TEEs.

For our threat model, we assume that an active attacker can control the network and has the ability to corrupt any number of clients, the parties who call the proposed APIs, and some subset of aggregators, when used.

In the presence of this adversary, APIs should aim to achieve the following goals:

1. **Privacy**: Clients (and, more specifically, the vendors who distribute the clients) trust that (within the threat models), the API is purpose constrained. That is, all parties learn nothing beyond the intended result (e.g., a differentially private aggregation function computed over the client inputs.)
2. **Correctness:** Parties receiving the intended result trust that the protocol is executed correctly. Moreover, the amount that a result can be skewed by malicious input is bounded and known.

Specific proposed purpose constrained APIs will provide their own analysis about how they achieve these properties. This threat model does not address aspects that are specific to specific private computation designs or configurations. Each private computation option provides different options for defense against attacks.  Web platform vendors can decide what configurations produce adequate safeguards for their APIs and users. This is explored further in [Section 4: Private Computation Configurations](#4-private-computation-configurations).


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

However, we do assume that an attacker can always control at least one aggregator (i.e., there are no perfectly trusted aggregators.)


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

This results in four parameters:

1. _Unit of privacy_: The set of parties used to manage the privacy budget.
2. _Epoch_: The amount of time over which a differential privacy budget should be managed.
3. ε: The differential privacy parameter which measures the amount of individual differential information leakage allowed in each epoch.
4. _Aggregation minimum threshold_: the number of clients/users that need to be included in a given aggregation.

We can divide these parameters into two groups: parameters which should be part of the standard (and thus require consensus) and parameters which can be configured by the web platform vendor. The first two parameters, _unit of privacy_, and _epoch_ should likely be part of the standard. However, the other two parameters, ε and _aggregation minimum threshold_ could differ across web platform vendors without compromising interoperability.

Let's first address, ε. Suppose that we have _Browser A_ and _Mobile OS B_ which, respectively, decide that the appropriate budget is "1 unit/epoch" and "5 unit/epoch". Luckily, in the worst case, privacy budgets are additive, and thus can be split into smaller pieces. A user of this API could then decide to use "1 unit/epoch" on the API from both _Browser A_ and _Mobile OS B_, and then continue to use the remaining "4 unit/epoch" on the API from _Mobile OS B_.

This might be achieved by using a system that distributes information from both Browser A and Mobile OS B to a private computation entity that is configured to consume 1 unit per epoch. Information from Mobile OS B is additionally distributed to another entity that is configured to consume the remaining 4 units. As a practical matter, the two logical private computation entities here could be operated by the same organizations, but they would use different configurations. For instance, each configuration might use different keying material. In this case, Browser A is able to limit information release to 1 unit per epoch by choosing where information can be sent.

Secondly, let's address _aggregation minimum threshold_. Suppose that the same _Browser A_ and _Mobile OS B_ have also, respectively, decided that the _aggregation minimum threshold_ should be "X people" and "2X people". Because this is a minimum, when using the API from both, the minimum can be set to 2X, and satisfy both constraints. However, when using data that comes from _Browser A_ only, the minimum could be reduced to X.

## 4. Private Computation Configurations

Many of these proposals aim to leverage the idea of _private computation_, touched on briefly in the introduction. In it's ideal form, a private computation environment would allow for the evaluation of a predefined function (i.e., the constrained purpose,) without revealing any new information to any party beyond the output of that predefined function. This is commonly used to perform aggregation over inputs which, individually, must not be revealed. It is also often used to then apply differentially private noise to those aggregates.

There are currently two proposed constructions of a private computation environment under consideration: _multi-party computation_ (MPC) and _trusted execution environments_ (TEEs). The following two subsections outline how these can be to create a private computation environment, how they fit into the threat model, and the assumptions required to achieve our goal of assuring that the inputs are not revealed.

### 4.1 Multi-party Computation

Multi-party computation is a cryptographic protocol in which distinct parties can collectively operate on data which remains oblivious to any individual party throughout the computation, but allows for joint evaluation of a predefined function.

These protocols typically work with data which is _secret shared_. For example, a three way _additive_ secret share of a value v = s<sub>1</sub> + s<sub>2</sub> + s<sub>3</sub> can be constructed by generating two random values for s<sub>1</sub> and s<sub>2</sub>, and then computing s<sub>3</sub> = v - s<sub>1 - s<sub>2</sub>. At this point, each value s<sub>i</sub> individually appears random, and thus v remains oblivious as long as no single entity learns all values of s<sub>i</sub>. A similar secret sharing schemes uses XOR in place of addition; alternatively, Shamir's secret sharing uses polynomial interpolation.

In terms of our threat model, these parties are aggregators and we assume that an attacker can control some subset of those aggregators. That exact threshold may be different for a given proposal, for example, we may assume that an attacker can only control one out of three aggregators. This would enable, in cryptographic terms, a maliciously secure, honest two out of three majority MPC.

Another security model for MPC is _honest but curious_, where the input data remains oblivious so long as aggregators do not deviate from the protocol. Since we are assuming that an attacker can control some subset of the aggregators, it would be able to deviate from the protocol, and thus the _honest but curious_ is not suitable.

Given these aggregators, a _client/user_ is able to generate a secret sharing of their input data, and then securely communicate one secret share to each aggregator. This allows the aggregators to then perform the MPC protocol to compute the predefined function. The _client/user_ trusts that an attacker cannot control enough aggregators to violate that protocol.

If the protocol is faithfully executed, the aggregate result can then be reported to the intended recipient without the entities that performed the computation needing to witness the answer.

This threat model does not require that the MPC protocol provide safeguards against an aggregator spoiling the answers from the system. Though a spoiled answer is possible in many protocols, the primary threat that a compromised aggregator presents is to the privacy of _clients/users_. We assume that there is some contractual relationship between those requesting aggregation and the entities that perform that aggregation such that the aggregators lack significant incentive to spoil results.

### 4.2 Trusted Execution Environments (TEEs)

Trusted execution environments are specialized hardware where encrypted data can be sent "in" to an enclave where it is decrypted and operated on, but cannot be otherwise accessed. A TEE can produce an "attestation" that acts as a claim - backed by the vendor of the TEE - that only specific code is actively running.

Different hardware manufacturers offer different types of TEEs, and there are various assumptions which need to be made about the manufacturer, hardware operator, and other tenants on the hardware in order to achieve our ideal of input data remaining oblivious. There are documented attacks for many of these types of hardware, and while we don't expect all web platform vendors to support this construction, some vendors have expressed comfort with the required assumptions.

In order to utilize a TEE to achieve private computation, we need two properties. First, the data must only be able to be decrypted within the TEE, and second, we need to assure that the TEE only executes code which evaluates the predefined function.

In a very simplified form, we could imagine there is one TEE which is used by every _first party_/_delegated party_ to run the predefined function. In this case, the _user/client_ could encrypt their data with the public key of the TEE. However, this ignores assuring the code running is that that's intended. The TEE produces an attestation of the code it's running, however the _client/user_ needs that attestation to be verified before their input data is decrypted.

This can be achieved by utilizing an aggregator who verifies the TEE attestation on behalf of the _client/user_. In this case, the _client/user_ encrypts the data towards the aggregator, and the aggregator provides the decryption key into the TEE if and only if the attestation verifies the TEE is only running the expected code. This still has one problem, however, as we assume that our attacker can compromise at least one aggregator.

We can extend this one step further, by utilizing not one, but multiple aggregators. Each aggregator can hold part of a _threshold key_, which allows data to be encrypted such that it requires all (or a predefined subset) of keys to decrypt. Each aggregator verifies the attestation and provides their decryption key into the TEE if and only if the attestation verifies the TEE is only running the expected code. Thus, only the TEE is ever able to decrypt the data, so long as the attacker is only able to control a subset of aggregators.

### 4.3 Abstracting Private Computation

Both of these constructions rely on a set of aggregators, where we assume that an attacker is unable to control all aggregators. Let a specific set of aggregators be called an aggregator network. Each web platform vendor will need to make a judgment as to if it's reasonable to assume that an aggregator network can be _trusted_.  That is, that attackers would be unable to compromise any subset of the aggregators within that specific aggregator network such that the privacy guarantees of the system could not be met.

To make this judgment, web platform vendors are likely to consider various properties about helper party networks such as diversity in ownership of the company/organization operating the aggregators, diversity in cloud provider (if relevant) used by the aggregators, diversity in jurisdictions in which the aggregators operate, etc.

It's unlikely that all web vendors will arrive at the same decision as to which aggregator networks can be assumed to be trusted. Our aim, however, is for many such aggregator networks to exist that are trusted by most web platforms, allowing for choice and competition for the first/delegated parties that leverage these APIs. Moreover, as we may have web platform vendors providing different privacy budgets (see section [3.3 Privacy Parameters](#33-privacy-parameters),) the first/delegated parties may use multiple aggregator networks to utilize those different budgets.

Just as web vendors unlikely to fully agree on the sets of trusted aggregator network, they may also reach different conclusions as to which constructions of private computation can be utilized. If we consider an aggregator network to be the pairing of both the aggregator parties and the private computation construction, it's reasonably straight forward to for the standard to be unopinionated about the construction. Instead, individual web platform vendors can include the private computation construction when considering which aggregator networks are trusted.