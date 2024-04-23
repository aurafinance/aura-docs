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

***

### Using the UI

#### Step 1: Navigate to the Pool you want to incentivize

#### Step 2: Enter 'Dev Mode'

<figure><img src="../../.gitbook/assets/Screenshot 2024-04-23 at 09.12.20.png" alt=""><figcaption></figcaption></figure>

#### Step 3: Scroll down and choose the 'Add Extra Rewards' Tab

#### &#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2024-04-23 at 09.13.15.png" alt=""><figcaption></figcaption></figure>

#### Step 4: Enter a token address

Currently this token list is limited, however this will change soon (TM) and it will be possible to add other tokens.

\


<figure><img src="../../.gitbook/assets/Screenshot 2024-04-23 at 09.25.45.png" alt=""><figcaption></figcaption></figure>

#### Step 5: Add the rewards

To add new rewards, you need to add a value of over 5000 USD, rewards that have been previously added have a minimum of 1000 USD.

Rewards have to be manually configured for pools, so they may not appear instantly. They will begin to be distributed on the following Thursday, provided the minimum amounts have been reached.
