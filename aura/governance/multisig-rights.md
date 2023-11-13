# Multisig Rights

{% hint style="info" %}
The system is non-custodial. There should be no circumstances under which admin functions can pause, siphon off, or redirect user capital; thus, time-lock delays are not required.
{% endhint %}

### Immutable Contracts

All Aura smart contracts are immutable therefore, there are no admin rights to update smart contracts

### Pausable Contracts

Aura smart contracts are not pausable; users always have control of their funds.

### Admin Functions

Most Aura governance follows the process described [here](./),  once the governance process has taken place, then the  [Protocol Multisig](multisig-composition.md#protocol-multisig) can perform admin actions.



* [AuraLocker](https://etherscan.io/address/0x3Fa73f1E5d8A792C80F426fc8F84FBF7Ce9bBCAC) (vlAura).&#x20;
  * Add another reward token
  * Approve a particular distributor to add rewards&#x20;
  * Set the incentive for kicking (capped)
  * Shut down the locker allowing all deposits to be withdrawn immediately
  * Recover non-reward erc20 tokens
* [Booster](https://etherscan.io/address/0x7818A1DA7BD1E64c199029E86Ba244a9798eEE10)&#x20;
  * Change platform fees allocated to stakers within hard-coded ranges,  with an absolute fee ceiling of 25%
* [Voter Proxy ](https://etherscan.io/address/0xaF52695E1bB01A16D33D7194C28C42b10e0Dbec2)
  * Set reward with-drawer and deposit for unprotected tokens ending up in the VoterProxy from direct or airdrop incentives to ve holders.
  * Set the gaugeController and minter (BAL) address
  * Apply a new "operator" on the [whitelisted proxy contract](https://etherscan.io/address/0xaF52695E1bB01A16D33D7194C28C42b10e0Dbec2) if and ONLY if the operator is not set or the Booster is wholly shut down.
* Shutdown/pause new deposits to Pool staking contracts.
  * _**Allows users to withdraw all funds**_
* Shutdown/pause new deposits to the protocol.
  * _**Allows users to withdraw all funds**_

&#x20;

