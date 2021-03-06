---
description: How to integrate with the Aura Finance protocol
---

# Building on Aura

{% hint style="info" %}
These docs are still a work in progress. Reach out to the team if you have any requests or get stuck integrating
{% endhint %}

### Finding the contracts

Find all Aura contracts here:

{% embed url="https://github.com/aurafinance/aura-contracts-lite" %}

With modified code from the Convex platform here:

{% embed url="https://github.com/aurafinance/convex-platform" %}

### Depositing BPTs

All gauge deposit pools are 4626 vaults, with the `asset` being Balancers LP token (BPT). Simply approve the reward pool and call the `deposit` function.

Find the reference implementation here:

{% embed url="https://github.com/aurafinance/convex-platform/blob/lite/contracts/contracts/BaseRewardPool4626.sol" %}

{% hint style="info" %}
Note: `auraBAL` and `vlAURA` reward contracts are not on the 4626 standard&#x20;
{% endhint %}

