# Security

Smart contract security is a top priority for those working on Aura Finance. All reasonable precautions must be taken to ensure the protocol is safe to use. Below is a list of some of the things we believe make smart contract systems secure.

### Internally

#### Codebase

Some practices employed on Aura Finance smart contract repositories:

* protected `master` branch with mandatory peer reviews and passing CI (including linting, compiling, and testing)
* \>98% code coverage (using coveralls) and comprehensive integration tests
* Strict linting rules
* Code commented using the Natspec standard

#### Fork testing

Fork testing is helps simulate contract deployments and functionality in a live environment, accounting for external dependencies. Aura comprehensively tests deployments using fork tests.

#### Internal auditing

Developers know their code best, and dedicated time has been taken to manually review all code in the system.



### Audits

#### Audit 1 - Peckshield (7th Apr 2022)

{% file src="../../.gitbook/assets/PeckShield-Audit-Report-AuraFinance-v1.0rc (1).pdf" %}

#### Audit 2 - Halborn (starting 16th May 2022)

TBA.



### Bug bounties

External bug bounties are essential for projects. Aura will increase its bounty as more resources become available.

{% embed url="https://immunefi.com/bounty/aurafinance" %}

