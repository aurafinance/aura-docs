# Frequently Asked Questions

* How often are earnings updated? Why am I staking my lp token, but I don't get anything?
  *   Block by block. Sometimes if rewards aren’t flowing, `harvestRewards()` may need to be called on the pool. Those that call the `harvestRewards()` function are incentivized by 0.5% of the harvested BAL amount.


* What is the difference between Current vAPR and Projected vAPR?
  *   Current APR is based off of Aura smart contract data (ie rewardRate field in pool contracts), rewards lag slightly and take a \~week to accrue/ramp up which means there will be a short period where Aura BAL APR < current Balancer BAL APR. Projected APR is the rate from Balancer (basically the APR target).


* How long does it take for the projected vAPR to catch up with the current vAPR?
  *   Approximately 1 week if all parameters stay the same: Vote weight, pool TVL, and relative TVL between Aura and other veBAL holders.


* Are Rewards for a BPT pool accumulating while it is in a bootstrapping phase?
  *   Yes


*   How long does the bootstrapping phase of a BPT pool take?

    * \~1 week


*   Are staking rewards for the rETH/WETH pool factored in the APR math like that of the wstETH/WETH pool?

    * Rewards aren’t yet displayed in APR modals but they do accrue


*   Is 1 vlAURA = 1 veBAL?

    * It is possible that this is the case, though likely not. It is a function of locked vlAURA vs. the veBAL owned by Aura
    * [https://dune.com/0xahtle7/aura-finance-dashboard](https://dune.com/0xahtle7/aura-finance-dashboard)
    * [https://dune.com/aura\_finance/aura](https://dune.com/aura\_finance/aura)


*   How do you get LPs staked balance and rewards by the contract?

    * Go to the Info tab on the Stake table you’ll find the reward pool address, Balance comes from the rewardPool contract ( balanceOf on the contract)


*   How do you calculate boost?

    * `function getBoost() { const adjustedGaugeBalance = 0.4 * gaugeBalance + ((0.6 * vebalBalance) / vebalTotalSupply) * gaugeTotalSupply; const workingBalance = gaugeBalance < adjustedGaugeBalance ? gaugeBalance : adjustedGaugeBalance; const zeroBoostWorkingBalance = 0.4 * gaugeBalance; const zeroBoostWorkingSupply = gaugeWorkingSupply - workingBalance + zeroBoostWorkingBalance; const boostedFraction = workingBalance / gaugeWorkingSupply; const unboostedFraction = zeroBoostWorkingBalance / zeroBoostWorkingSupply; return boostedFraction / unboostedFraction; }`


*   How can I get rewardRate directly from the contract without using the subgraph?

    * Yes, use the UI -> Info tab -> Reward pool -> Etherscan -> rewardRate


*   How can I get the totalAssets (total auraBAL) I have staked in auraBALRewards?

    * Total staked == balanceof
    * ERC4626 vaults always compound into the native asset but this doesn't. No rewards are denominated in auraBAL, so the balanceof is not to change and the rewards for auraBAL are in AURA, BAL, and bb-a-usd which accrues to extraRewards and earned.


* Where can I get all of the reward tokens for a specific BPT? preferably on-chain
  * Use “Rewards contract address” to show BAL rewards, use “extraRewards” on rewards contract to show any extra rewards, and use “mint( ) “on AURA contract for logic of how much AURA is minted per BAL
