---
description: The AuraBalVault compounder is ERC4626 compatible
---

# AuraBalVault  - Compounder

Stake auraBal  into the AuraBalVault, which will automatically compound the rewards. The Vault earns three reward tokens: BAL, bb-a-USD, and AURA. BAL and bb-a-USD are sold in the market for auraBal, which is then re-stake. AURA added as an additional reward token that can be claimed.&#x20;

## AuraBalVault

Forked from llama.airforce \


#### isHarvestPermissioned

```solidity
bool isHarvestPermissioned
```

#### authorizedHarvesters

```solidity
mapping(address => bool) authorizedHarvesters
```

#### constructor

```solidity
constructor(address _token, address _virtualRewardFactory) public
```

#### setHarvestPermissions

```solidity
function setHarvestPermissions(bool _status) external
```

Sets whether only whitelisted addresses can harvest

**Parameters**

| Name     | Type | Description                              |
| -------- | ---- | ---------------------------------------- |
| \_status | bool | Whether or not harvests are permissioned |

#### updateAuthorizedHarvesters

```solidity
function updateAuthorizedHarvesters(address _harvester, bool _authorized) external
```

Adds or remove an address from the harvesters' whitelist

**Parameters**

| Name         | Type    | Description                         |
| ------------ | ------- | ----------------------------------- |
| \_harvester  | address | address of the authorized harvester |
| \_authorized | bool    | Whether to add or remove harvester  |

#### harvest

```solidity
function harvest(uint256 _minAmountOut) public
```

Claim rewards and swaps them to auraBAL for restaking

**Parameters**

| Name           | Type    | Description                                    |
| -------------- | ------- | ---------------------------------------------- |
| \_minAmountOut | uint256 | - min amount of auraBAL to receive for harvest |

#### harvest

```solidity
function harvest() public
```

Claim rewards and swaps them to auraBAL for restaking

### FeeForwarder

Forwards collected fees to Vault.

#### Forwarded

```solidity
event Forwarded(address vault, address token, uint256 amount)
```

#### constructor

```solidity
constructor(address _dao) public
```

**Parameters**

| Name  | Type    | Description    |
| ----- | ------- | -------------- |
| \_dao | address | Address of DAO |

#### forward

```solidity
function forward(address vault, address token, uint256 amount) public
```

### GenericUnionVault&#x20;

#### withdrawalPenalty

```solidity
uint256 withdrawalPenalty
```

#### MAX\_WITHDRAWAL\_PENALTY

```solidity
uint256 MAX_WITHDRAWAL_PENALTY
```

#### FEE\_DENOMINATOR

```solidity
uint256 FEE_DENOMINATOR
```

#### underlying

```solidity
address underlying
```

#### virtualRewardFactory

```solidity
address virtualRewardFactory
```

#### strategy

```solidity
address strategy
```

#### extraRewards

```solidity
address[] extraRewards
```

#### isExtraReward

```solidity
mapping(address => bool) isExtraReward
```

#### WithdrawalPenaltyUpdated

```solidity
event WithdrawalPenaltyUpdated(uint256 _penalty)
```

#### Harvest

```solidity
event Harvest(address _caller, uint256 _value)
```

#### CallerIncentiveUpdated

```solidity
event CallerIncentiveUpdated(uint256 _incentive)
```

#### StrategySet

```solidity
event StrategySet(address _strategy)
```

#### ExtraRewardAdded

```solidity
event ExtraRewardAdded(address _reward, address extraReward)
```

#### ExtraRewardCleared

```solidity
event ExtraRewardCleared(address _reward)
```

#### constructor

```solidity
constructor(address _token, address _virtualRewardFactory) public
```

#### setWithdrawalPenalty

```solidity
function setWithdrawalPenalty(uint256 _penalty) external
```

Updates the withdrawal penalty

**Parameters**

| Name      | Type    | Description                               |
| --------- | ------- | ----------------------------------------- |
| \_penalty | uint256 | - the amount of the new penalty (in BIPS) |

#### setStrategy

```solidity
function setStrategy(address _strategy) external
```

Set the address of the strategy contract

**Parameters**

| Name       | Type    | Description                        |
| ---------- | ------- | ---------------------------------- |
| \_strategy | address | - address of the strategy contract |

#### extraRewardsLength

```solidity
function extraRewardsLength() external view returns (uint256)
```

Count of extra rewards

#### addExtraReward

```solidity
function addExtraReward(address _reward) external returns (bool)
```

Add extra reward contract

**Parameters**

| Name     | Type    | Description                      |
| -------- | ------- | -------------------------------- |
| \_reward | address | VirtualBalanceRewardPool address |

**Return Values**

| Name | Type | Description  |
| ---- | ---- | ------------ |
| \[0] | bool | bool success |

#### clearExtraRewards

```solidity
function clearExtraRewards() external
```

Clear extra rewards array

#### totalUnderlying

```solidity
function totalUnderlying() public view returns (uint256 total)
```

Query the amount currently staked

**Return Values**

| Name  | Type    | Description                         |
| ----- | ------- | ----------------------------------- |
| total | uint256 | - the total amount of tokens staked |

#### balanceOfUnderlying

```solidity
function balanceOfUnderlying(address user) external view returns (uint256 amount)
```

Returns the amount of underlying a user can claim

**Parameters**

| Name | Type    | Description                               |
| ---- | ------- | ----------------------------------------- |
| user | address | - address whose claimable amount to query |

**Return Values**

| Name   | Type    | Description        |
| ------ | ------- | ------------------ |
| amount | uint256 | - claimable amount |

#### deposit

```solidity
function deposit(uint256 _amount, address _receiver) public returns (uint256 _shares)
```

Deposit user funds in the autocompounder and mints tokens representing user's share of the pool in exchange

**Parameters**

| Name       | Type    | Description                           |
| ---------- | ------- | ------------------------------------- |
| \_amount   | uint256 | - the amount of underlying to deposit |
| \_receiver | address |                                       |

**Return Values**

| Name     | Type    | Description                   |
| -------- | ------- | ----------------------------- |
| \_shares | uint256 | - the amount of shares issued |

#### redeem

```solidity
function redeem(uint256 _shares, address _receiver, address _owner) public returns (uint256 withdrawn)
```

Unstake underlying token in proportion to the amount of shares sent

**Parameters**

| Name       | Type    | Description                 |
| ---------- | ------- | --------------------------- |
| \_shares   | uint256 | - the number of shares sent |
| \_receiver | address |                             |
| \_owner    | address |                             |

**Return Values**

| Name      | Type    | Description                                     |
| --------- | ------- | ----------------------------------------------- |
| withdrawn | uint256 | - the amount of underlying returned to the user |

#### harvest

```solidity
function harvest() public virtual
```

Claim rewards and swaps them to auraBAL for restaking

#### asset

```solidity
function asset() public view returns (address)
```

The address of the underlying token used for the Vault for accounting, depositing, and withdrawing.

#### totalAssets

```solidity
function totalAssets() public view returns (uint256)
```

Total amount of the underlying asset that is “managed” by Vault.

#### convertToShares

```solidity
function convertToShares(uint256 _assets) public view returns (uint256)
```

The amount of shares that the Vault would exchange for the amount of assets provided, in an ideal scenario where all the conditions are met.

#### convertToAssets

```solidity
function convertToAssets(uint256 _shares) public view returns (uint256)
```

The amount of assets that the Vault would exchange for the amount of shares provided, in an ideal scenario where all the conditions are met.

#### maxDeposit

```solidity
function maxDeposit(address) public pure returns (uint256)
```

Maximum amount of the underlying asset that can be deposited into the Vault for the receiver, through a deposit call.

#### previewDeposit

```solidity
function previewDeposit(uint256 _assets) public view returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their deposit at the current block, given current on-chain conditions.

#### maxMint

```solidity
function maxMint(address) public pure returns (uint256)
```

Maximum amount of shares that can be minted from the Vault for the receiver, through a mint call.

#### previewMint

```solidity
function previewMint(uint256 _shares) public view returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their mint at the current block, given current on-chain conditions.

#### mint

```solidity
function mint(uint256 _shares, address _receiver) public returns (uint256)
```

Mints exactly shares Vault shares to receiver by depositing assets of underlying tokens.

#### maxWithdraw

```solidity
function maxWithdraw(address _owner) public view returns (uint256)
```

Maximum amount of the underlying asset that can be withdrawn from the owner balance in the Vault, through a withdraw call.

#### previewWithdraw

```solidity
function previewWithdraw(uint256 _assets) public view returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their withdrawal at the current block, given current on-chain conditions.

#### withdraw

```solidity
function withdraw(uint256 _assets, address _receiver, address _owner) public returns (uint256)
```

Burns shares from owner and sends exactly assets of underlying tokens to receiver.

#### maxRedeem

```solidity
function maxRedeem(address _owner) public view returns (uint256)
```

Maximum amount of Vault shares that can be redeemed from the owner balance in the Vault, through a redeem call.

#### previewRedeem

```solidity
function previewRedeem(uint256 _shares) public view returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their redeemption at the current block, given current on-chain conditions.

### AuraBalStrategy

#### vault

```solidity
address vault
```

#### rewardTokens

```solidity
address[] rewardTokens
```

#### rewardHandlers

```solidity
mapping(address => address) rewardHandlers
```

#### FEE\_DENOMINATOR

```solidity
uint256 FEE_DENOMINATOR
```

#### constructor

```solidity
constructor(address _vault, address _balVault, address _auraBalStaking, address _balToken, address _wethToken, address _auraToken, address _auraBalToken, address _bbusdToken, bytes32 _auraBalBalETHBptPoolId, bytes32 _balETHPoolId) public
```

#### setApprovals

```solidity
function setApprovals() external
```

Set approvals for the contracts used when swapping & staking

#### addRewardToken

```solidity
function addRewardToken(address _token, address _handler) external
```

Add a reward token and its handler

**Parameters**

| Name      | Type    | Description                                           |
| --------- | ------- | ----------------------------------------------------- |
| \_token   | address | the reward token to add                               |
| \_handler | address | address of the contract that will sell for BAL or ETH |

#### updateRewardToken

```solidity
function updateRewardToken(address _token, address _handler) external
```

Update the handler of a reward token

**Parameters**

| Name      | Type    | Description                                           |
| --------- | ------- | ----------------------------------------------------- |
| \_token   | address | the reward token to add                               |
| \_handler | address | address of the contract that will sell for BAL or ETH |

#### totalRewardTokens

```solidity
function totalRewardTokens() external view returns (uint256)
```

returns the number of reward tokens

**Return Values**

| Name | Type    | Description                 |
| ---- | ------- | --------------------------- |
| \[0] | uint256 | the number of reward tokens |

#### totalUnderlying

```solidity
function totalUnderlying() public view returns (uint256 total)
```

Query the amount currently staked

**Return Values**

| Name  | Type    | Description                         |
| ----- | ------- | ----------------------------------- |
| total | uint256 | - the total amount of tokens staked |

#### stake

```solidity
function stake(uint256 _amount) public
```

Deposits underlying tokens in the staking contract

#### withdraw

```solidity
function withdraw(uint256 _amount) external
```

Withdraw a certain amount from the staking contract

**Parameters**

| Name     | Type    | Description              |
| -------- | ------- | ------------------------ |
| \_amount | uint256 | - the amount to withdraw |

#### harvest

```solidity
function harvest(uint256 _minAmountOut) public returns (uint256 harvested)
```

Claim rewards and swaps them to auraBAL for restaking

**Parameters**

| Name           | Type    | Description                                     |
| -------------- | ------- | ----------------------------------------------- |
| \_minAmountOut | uint256 | - min amount of LP tokens to receive w/o revert |

**Return Values**

| Name      | Type    | Description            |
| --------- | ------- | ---------------------- |
| harvested | uint256 | - the amount harvested |

### AuraBalStrategyBase

#### BBUSD\_TOKEN

```solidity
address BBUSD_TOKEN
```

#### AURA\_TOKEN

```solidity
address AURA_TOKEN
```

#### AURABAL\_TOKEN

```solidity
address AURABAL_TOKEN
```

#### WETH\_TOKEN

```solidity
address WETH_TOKEN
```

#### BAL\_TOKEN

```solidity
address BAL_TOKEN
```

#### BAL\_ETH\_POOL\_TOKEN

```solidity
address BAL_ETH_POOL_TOKEN
```

#### auraBalStaking

```solidity
contract IBasicRewards auraBalStaking
```

#### balVault

```solidity
contract IBalancerVault balVault
```

#### constructor

```solidity
constructor(address _balVault, address _auraBalStaking, address _balToken, address _wethToken, address _auraToken, address _auraBalToken, address _bbusdToken, bytes32 _auraBalBalETHBptPoolId, bytes32 _balETHPoolId) public
```

### BalancerSwapsHandler

#### SwapPath

```solidity
struct SwapPath {
  bytes32[] poolIds;
  address[] assetsIn;
}
```

#### constructor

```solidity
constructor(address _token, address _strategy, address _balVault, address _wethToken, struct BalancerSwapsHandler.SwapPath _swapPath) public
```

#### getSwapPath

```solidity
function getSwapPath() external view returns (struct BalancerSwapsHandler.SwapPath)
```

#### setApprovals

```solidity
function setApprovals() external
```

#### sell

```solidity
function sell() external
```

### HandlerBase

#### owner

```solidity
address owner
```

#### pendingOwner

```solidity
address pendingOwner
```

#### token

```solidity
address token
```

#### strategy

```solidity
address strategy
```

#### WETH\_TOKEN

```solidity
address WETH_TOKEN
```

#### balVault

```solidity
contract IBalancerVault balVault
```

#### constructor

```solidity
constructor(address _token, address _strategy, address _balVault, address _wethToken) public
```

#### setPendingOwner

```solidity
function setPendingOwner(address _po) external
```

#### applyPendingOwner

```solidity
function applyPendingOwner() external
```

#### rescueToken

```solidity
function rescueToken(address _token, address _to) external
```

#### sell

```solidity
function sell() external virtual
```

#### receive

```solidity
receive() external payable
```
