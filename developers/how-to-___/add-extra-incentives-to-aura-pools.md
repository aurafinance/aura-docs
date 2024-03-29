# Add extra incentives to Aura pools

So you want to add a token as an additional incentive to an Aura pool? Well, there are a number of methods to accomplish this. Here we will identify the main avenue.

#### Step 1:  Funding the StashRewardDistro

On each chain there is a `StashRewardDistro` contract that collects and distributed incentives on a weekly basis. View the addresses here [deployed-addresses](../deployed-addresses/ "mention")&#x20;

Call `token.approve(stashRewardDistroAddress, amount)`

then `stashRewardDistro.fundPool` defining how many periods (weeks) to split the reward across

Any rewards added here are then to be distributed on the upcoming epoch (Thursday at 0000 UTC), provided step 2 has been followed.

#### Step 2: Hooking up the extra reward

&#x20;The reward token then needs to be added to the protocol on the back end. This can be done by contacting the Balancer Maxis and requesting the token to be added, or the Aura protocol multisig.

#### Step 3: Rewards queued

&#x20;The next thursday that rolls around, the tokens will be queued up and distributed linearly over the defined periods, provided Step 2 has been executed
