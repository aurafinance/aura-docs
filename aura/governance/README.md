# Governance

### Context

Aura is a decentralized and community-governed protocol. Token holders direct all actions undertaken through [Aura Vote Locking](../usdaura/vote-locking.md).

The core things to be governed in the Aura system are as follows:

1. Internal proposals (e.g. setting fee rates)
2. [Aura treasury](https://aura.defilytica.com/#/treasury)
3. Balancer Snapshot voting
4. [Balancer Gauge voting](gauge-voting.md)



### How does it work

Aura has prepared for full on-chain governance by implementing vote delegation on the Vote Locked Aura contract. That said, until the Aura voter base matures, a safer method will be used for executing governance decisions.

#### Phase 1 - Snapshot & multisigs

**Protocol Multisig -** Internal proposals, Gauge votes

**Treasury Multisig -** Treasury&#x20;

Read more about the [multisig composition here](multisig-composition.md).



#### Phase 2 - Full on chain voting

Provided it is technically feasible and doesn't require compromising on security, Aura is committed to transitioning this mechanism on-chain via the likes of _Governor bravo_ in the forthcoming months when appropriate.&#x20;

"Internal governance proposals (e.g. setting fee rates)" - this can be moved on chain with GovernorBravo implementation once the vote distribution is sufficiently mature.

"Aura treasury" - this can be moved forward with either [Gnosis SafeSnap](https://docs.snapshot.org/plugins/safesnap) or an on-chain treasury management solution.

"Balancer Gauge voting" - custom smart contracts can be used for this. Given the uptick in gas costs in voting for gauges each week, this solution may be deployed on a cheaper L2 and forwarded.

&#x20;

