# Working Parameters for Private Measurement Technical Specification MVP

At our September 2022 meeting at TPAC, 8 people signed up to be editors to draft a _Private Measurement Technical Specification MVP._  That group produced [Private Measurement Important Dimensions for Attribution.](https://github.com/patcg/docs-and-reports/tree/main/design-dimensions)

At the February 2023 in person meeting in NYC, we ran a [survey and discussed the results](https://github.com/patcg/meetings/issues/91). We were able to discuss two of these topics in detail at the meeting, and intend to continue that conversation in upcoming meetings. As part of those discussions, we reached soft consensus on working parameters that the editors of the _Private Measurement Technical Specification MVP_ can work from.

This document serves to capture those parameters (and is intended to be updated as more are discussed and reach soft consensus.)


# Parameters with Soft Consensus


## Server Architecture may exist

The community group has reached soft consensus that the _Private Measurement Technical Specification MVP_ need not be limited to only on-device processing, however there is a very high bar for any server architecture that is included. This included both a high level of security (which the editors will align with the ongoing [Threat Model](https://github.com/patcg/docs-and-reports/tree/main/threat-model) work) as well as ability to explain the privacy and security properties of the system to end users.


## Privacy defined by Differential Privacy 

We’ve explored three main definitions of privacy:

1. Information theoretic
2. K-anonymity
3. Differential privacy

The community group has reached soft consensus that the _Private Measurement Technical Specification MVP_ should use a definition of privacy based on differential privacy. This does not rule out the use of other definitions of privacy being used in conjunction with differential privacy, however any proposal should aim to achieve a differential privacy guarantee.
