# Distribution

Aura's proposed and intended token emission and distribution will resemble the following:

### Emission

![](<../../.gitbook/assets/aura emission.png>)

### Distribution

* 50% Balancer LP rewards Rewarded pro-rata for BAL received on Aura
* 10% for StableSwap auraBAL/BPT over 4 years
* 5% for Aura liquidity
* 17.5% Treasury vested over 4 years
* 2.5% bootstrapping token holder base
* 1% for future incentives
* 2% BAL treasury over 2 years
* 2% veBAL bootstrapping incentives
* 10% for the Aura team over 2 years
* 100% Fair launched no VC - Seed investors

#### AURA vs BAL (LP Rewards)

In the above distribution, it notes that 50% of AURA supply will be distributed pro-rata for BAL earned on Aura. The chart above is modelled assuming 80% of all $BAL is earned on Aura.

The following function is used to calculate the amount of Aura units minted per BAL. On day 1, there will be 3.9 AURA emitted per BAL. This trends towards 1.4 AURA per BAL as supply increases.

`auraUnitsMinted = ((500 - (totalSupply() - 50000000) / 100000) * 2.5 + 700) / 500) * balEarned`
