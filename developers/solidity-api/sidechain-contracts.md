# Sidechain Contracts

Aura Finance is expanding to become a cross-chain protocol. Deploying to chains beyond Ethereum, with the intent to deploy to wherever the balancer is deployed and emitting rewards. As part of this expansion, a new set of contracts had been deployed.&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2023-06-20 at 15.48.22.png" alt=""><figcaption></figcaption></figure>



## Sidechain Contracts

* AuraBalProxyOFT.sol : L1 Aura BAL proxy OFT&#x20;
* AuraBalOFT.sol : L2 Aura BAL OFT&#x20;
* AuraProxyOFT.sol : L1 Aura proxy OFT&#x20;
* AuraOFT.sol : L2 Aura OFT&#x20;
* Create2Factory.sol : A create2 factory to deploy the sidechain contracts to constant addresses&#x20;
* CrossChainConfig.sol : Abstract contract to handle setting LZ configs&#x20;
* CrossChainMessages.sol : Shared LZ messaging library&#x20;
* L1Coordinator.sol : L1 Coordinator handles sending messages to canonical chain&#x20;
* L2Coordinator.sol : L2 Coordinator handles sending messages to canonical chain&#x20;
* PausableOFT.sol : OFT extension that allows a `guardian` address to perform an emergency pause&#x20;
* PausableProxyOFT.sol : Proxy OFT extension that allows a `guardian` address to perform an emergency pause&#x20;
* PauseGuardian.sol : Allows to implement pauses triggered by a `guardian` address
* L1PoolManagerProxy.sol : Allows to permissionless add pools on any supported sidechain
* L2PoolManagerProxy.sol : Given a root gauge on L1PoolManagerProxy it adds a gauge recipient on PoolManagerLite



### AuraBalOFT

_Sidechain auraBAL_

#### constructor

```solidity
constructor(string _name, string _symbol) public
```

_Constructs the AuraBalOFT contract._

**Parameters**

| Name     | Type   | Description          |
| -------- | ------ | -------------------- |
| \_name   | string | The oft token name   |
| \_symbol | string | The oft token symbol |

#### initialize

```solidity
function initialize(address _lzEndpoint, address _guardian) external
```

Initialize the contract.

**Parameters**

| Name         | Type    | Description                 |
| ------------ | ------- | --------------------------- |
| \_lzEndpoint | address | LayerZero endpoint contract |
| \_guardian   | address | Pause guardian              |

### AuraBalProxyOFT

_Send and receive auraBAL to and from all the sidechains. all auraBAL sat in this bridge will be staked in the auraBAL compounder and rewards distributed to the L2 staking contracts_

#### HarvestToken

```solidity
struct HarvestToken {
  address token;
  uint256 rewards;
}
```

#### vault

```solidity
address vault
```

_auraBAL compounder vault contract address_

#### internalTotalSupply

```solidity
uint256 internalTotalSupply
```

_Internally tracking of total auraBAL supply bridged_

#### harvestSrcChainIds

```solidity
uint16[] harvestSrcChainIds
```

_Harvest src chain IDs array_

#### totalClaimable

```solidity
mapping(address => uint256) totalClaimable
```

_token address mapped to amount_

#### claimable

```solidity
mapping(address => mapping(uint16 => uint256)) claimable
```

_token address mapped srcChainId mapped to amount claimable_

#### rewardReceiver

```solidity
mapping(uint16 => address) rewardReceiver
```

_srcChainId mapped to reward receiver_

#### authorizedHarvesters

```solidity
mapping(address => bool) authorizedHarvesters
```

_Authorized harvesters_

#### ofts

```solidity
mapping(address => address) ofts
```

_Token to OFT_

#### Harvest

```solidity
event Harvest(address caller, uint256 totalUnderlyingSum)
```

_Emitted when harvest rewards._

**Parameters**

| Name               | Type    | Description                                           |
| ------------------ | ------- | ----------------------------------------------------- |
| caller             | address | The caller                                            |
| totalUnderlyingSum | uint256 | The total amount of auraBal staked on all sidechains. |

#### constructor

```solidity
constructor(address _lzEndpoint, address _token, address _vault, address _guardian, address _sudo, uint256 _inflowLimit) public
```

_Constructs the AuraBalProxyOFT contract_

**Parameters**

| Name          | Type    | Description                    |
| ------------- | ------- | ------------------------------ |
| \_lzEndpoint  | address | LayerZero endpoint contract    |
| \_token       | address | The proxied token (auraBAL)    |
| \_vault       | address | The AuraBal compounder vault   |
| \_guardian    | address | The pause guardian address     |
| \_sudo        | address | The super user address         |
| \_inflowLimit | uint256 | Initial inflow limit per epoch |

#### setAdapterParams

```solidity
function setAdapterParams(uint16 _srcChainId, bytes32 _selector, bytes _adapterParams) external
```

_Sets the configuration for a given source chain ID and selector._

**Parameters**

| Name            | Type    | Description          |
| --------------- | ------- | -------------------- |
| \_srcChainId    | uint16  | The source chain ID. |
| \_selector      | bytes32 | The selector.        |
| \_adapterParams | bytes   | The adapter params.  |

#### setRewardReceiver

```solidity
function setRewardReceiver(uint16 _srcChainId, address _receiver) external
```

_Set reward receiver for src chain_

**Parameters**

| Name         | Type    | Description          |
| ------------ | ------- | -------------------- |
| \_srcChainId | uint16  | The source chain ID  |
| \_receiver   | address | The receiver address |

#### updateAuthorizedHarvesters

```solidity
function updateAuthorizedHarvesters(address _harvester, bool _authorized) external
```

_Adds or remove an address from the harvesters' whitelist_

**Parameters**

| Name         | Type    | Description                         |
| ------------ | ------- | ----------------------------------- |
| \_harvester  | address | address of the authorized harvester |
| \_authorized | bool    | Whether to add or remove harvester  |

#### setOFT

```solidity
function setOFT(address _token, address _oft) external
```

_Set OFT for token_

**Parameters**

| Name    | Type    | Description            |
| ------- | ------- | ---------------------- |
| \_token | address | Token contract address |
| \_oft   | address | OFT contract address   |

#### setHarvestSrcChainIds

```solidity
function setHarvestSrcChainIds(uint16[] _srcChainIds) external
```

_Set srcChainIds to loop through for harvest_

**Parameters**

| Name          | Type      | Description      |
| ------------- | --------- | ---------------- |
| \_srcChainIds | uint16\[] | Source chain IDs |

#### circulatingSupply

```solidity
function circulatingSupply() public view returns (uint256)
```

_returns the circulating amount of tokens on current chain_

#### harvest

```solidity
function harvest(uint256[] _totalUnderlying, uint256 _totalUnderlyingSum) external
```

\_Harvest rewards from the compounder and distribute them to the source chains

```
 Collect the amount of auraBAL that is staked on each source chain (L2). Then
 trigger a harvest on the vault which calculates the amount of auraBAL that has
 been earned since the last claim and the amount of extra rewards. These rewards
 are then lazily distributed to the src chains proportionally.

 Lazily meaning the claimable values are just added to a claimable mapping for
 processing latest via processClaimable_
```

**Parameters**

| Name                 | Type       | Description                                                 |
| -------------------- | ---------- | ----------------------------------------------------------- |
| \_totalUnderlying    | uint256\[] | Array of totalUnderlying auraBAL staked on the source chain |
| \_totalUnderlyingSum | uint256    | Sum of values in \_totalUnderlying array                    |

#### processClaimable

```solidity
function processClaimable(address _token, uint16 _srcChainId, address _zroPaymentAddress) external payable
```

_Process claimable rewards_

**Parameters**

| Name                | Type    | Description                       |
| ------------------- | ------- | --------------------------------- |
| \_token             | address | The token to process              |
| \_srcChainId        | uint16  | The source chain ID               |
| \_zroPaymentAddress | address | The LayerZero ZRO payment address |

#### vaultExecute

```solidity
function vaultExecute(uint256 _value, bytes _data) external returns (bool, bytes)
```

Execute a function on the vault

_In order to account for the withdrawalPenalty this contract needs To be the owner of the auraBAL vault. Therefore it needs to be able to call vault owner functions. Rather than wrapping each function we can just use an execute pointing at the vault_

#### rescue

```solidity
function rescue(address _token, address _to, uint256 _amount) external
```

Rescues the specified amount of tokens from the bridge and transfers them to the specified address.

_This function is only callable by the sudo address._

**Parameters**

| Name     | Type    | Description                                            |
| -------- | ------- | ------------------------------------------------------ |
| \_token  | address | The address of the token to be rescued.                |
| \_to     | address | The address to which the tokens should be transferred. |
| \_amount | uint256 | The amount of tokens to be rescued.                    |

### AuraOFT

_Sidechain AURA_

#### canonicalChainId

```solidity
uint16 canonicalChainId
```

_canonical chain ID_

#### Locked

```solidity
event Locked(address caller, uint256 amount)
```

_Emitted when locked cvx on the L1 chain_

**Parameters**

| Name   | Type    | Description               |
| ------ | ------- | ------------------------- |
| caller | address | The msg.sender            |
| amount | uint256 | The amount of cvx locked. |

#### constructor

```solidity
constructor(string _name, string _symbol, uint16 _canonicalChainId) public
```

_Constructs the AuraOFT contract._

**Parameters**

| Name               | Type   | Description            |
| ------------------ | ------ | ---------------------- |
| \_name             | string | The oft token name     |
| \_symbol           | string | The oft token symbol   |
| \_canonicalChainId | uint16 | The canonical chain id |

#### initialize

```solidity
function initialize(address _lzEndpoint, address _guardian) external
```

Initialize the contract.

**Parameters**

| Name         | Type    | Description                 |
| ------------ | ------- | --------------------------- |
| \_lzEndpoint | address | LayerZero endpoint contract |
| \_guardian   | address | The pause guardian          |

#### setAdapterParams

```solidity
function setAdapterParams(uint16 _srcChainId, bytes32 _selector, bytes _adapterParams) external
```

_Sets the configuration for a given source chain ID and selector._

**Parameters**

| Name            | Type    | Description          |
| --------------- | ------- | -------------------- |
| \_srcChainId    | uint16  | The source chain ID. |
| \_selector      | bytes32 | The selector.        |
| \_adapterParams | bytes   | The adapter params.  |

#### lock

```solidity
function lock(address _receiver, uint256 _cvxAmount, address _zroPaymentAddress) external payable
```

_Lock CVX on the L1 chain_

**Parameters**

| Name                | Type    | Description                                               |
| ------------------- | ------- | --------------------------------------------------------- |
| \_receiver          | address | address that will be receiving the refund and vlaura lock |
| \_cvxAmount         | uint256 | Amount of CVX to lock for vlCVX on L1                     |
| \_zroPaymentAddress | address | The LayerZero ZRO payment address                         |

### AuraProxyOFT

_Send and receive AURA to and from all the Sidechains and receives lock requests from the sidechains_

#### locker

```solidity
address locker
```

_Aura Locker contract address_

#### constructor

```solidity
constructor(address _lzEndpoint, address _token, address _locker, address _guardian, address _sudo, uint256 _inflowLimit) public
```

_Constructs the AuraProxyOFT contract_

**Parameters**

| Name          | Type    | Description                      |
| ------------- | ------- | -------------------------------- |
| \_lzEndpoint  | address | LayerZero endpoint contract      |
| \_token       | address | The proxied token (auraBAL)      |
| \_locker      | address | The Aura Locker contract address |
| \_guardian    | address | The pause guardian address       |
| \_sudo        | address | The super user address           |
| \_inflowLimit | uint256 | Initial inflow limit per epoch   |

### Create2Factory

Deploy contracts using CREATE2 opcode.

_A factory contract that uses the CREATE2 opcode to deploy contracts with a deterministic address._

#### Deployed

```solidity
event Deployed(bytes32 salt, address deployed)
```

_Event emitted when a contract is successfully deployed._

**Parameters**

| Name     | Type    | Description                                                                         |
| -------- | ------- | ----------------------------------------------------------------------------------- |
| salt     | bytes32 | A unique value used as part of the computation to determine the contract's address. |
| deployed | address | The address where the contract has been deployed.                                   |

#### deployer

```solidity
mapping(address => bool) deployer
```

#### updateDeployer

```solidity
function updateDeployer(address _deployer, bool _authorized) external
```

Adds or remove an address from the deployers' whitelist

**Parameters**

| Name         | Type    | Description                        |
| ------------ | ------- | ---------------------------------- |
| \_deployer   | address | address of the authorized deployer |
| \_authorized | bool    | Whether to add or remove deployer  |

#### deploy

```solidity
function deploy(uint256 amount, bytes32 salt, bytes bytecode, bytes[] callbacks) external returns (address)
```

Deploys a contract using the CREATE2 opcode.

**Parameters**

| Name      | Type     | Description                                                                         |
| --------- | -------- | ----------------------------------------------------------------------------------- |
| amount    | uint256  | The amount of Ether to be sent with the transaction deploying the contract.         |
| salt      | bytes32  | A unique value used as part of the computation to determine the contract's address. |
| bytecode  | bytes    | The bytecode that will be used to create the contract.                              |
| callbacks | bytes\[] | Callbacks to execute after contract is created.                                     |

**Return Values**

| Name | Type    | Description                                       |
| ---- | ------- | ------------------------------------------------- |
| \[0] | address | The address where the contract has been deployed. |

#### computeAddress

```solidity
function computeAddress(bytes32 salt, bytes32 codeHash) external view returns (address)
```

#### receive

```solidity
receive() external payable
```

_Fallback function that accepts Ether._

### CrossChainConfig

_Setter/Getter logic for cross chain layer zero config_

#### getAdapterParams

```solidity
mapping(uint16 => mapping(bytes32 => bytes)) getAdapterParams
```

_srcChainId mapped to selector and configuration_

#### SetAdapterParams

```solidity
event SetAdapterParams(uint16 srcChainId, bytes32 selector, bytes adapterParams)
```

_Emitted a configuration is set for a given source chain id._

**Parameters**

| Name          | Type    | Description          |
| ------------- | ------- | -------------------- |
| srcChainId    | uint16  | The source chain ID. |
| selector      | bytes32 | The selector.        |
| adapterParams | bytes   | The configuration.   |

#### setAdapterParams

```solidity
function setAdapterParams(uint16 _srcChainId, bytes32 _selector, bytes _adapterParams) external virtual
```

_Sets the configuration for a given source chain ID and selector._

**Parameters**

| Name            | Type    | Description          |
| --------------- | ------- | -------------------- |
| \_srcChainId    | uint16  | The source chain ID. |
| \_selector      | bytes32 | The selector.        |
| \_adapterParams | bytes   | The adapter params.  |

### CrossChainMessages

_Share types for cross chain messages_

#### MAGIC\_BYTES

```solidity
bytes4 MAGIC_BYTES
```

\_Magic Bytes to pad the custom message with bytes4(keccak256("_isCustomMessage(bytes)"))_

#### MessageType

```solidity
enum MessageType {
  LOCK,
  FEES,
  FEES_CALLBACK
}
```

### L1Coordinator

_Tracks the amount of fee debt accrued by each sidechain and sends AURA back to each sidechain for rewards_

#### REWARD\_MULTIPLIER\_DENOMINATOR

```solidity
uint256 REWARD_MULTIPLIER_DENOMINATOR
```

#### balToken

```solidity
address balToken
```

_BAL token contract_

#### auraToken

```solidity
address auraToken
```

_AURA token contract_

#### auraOFT

```solidity
address auraOFT
```

_AURA OFT token contract_

#### treasury

```solidity
address treasury
```

_AURA treasury address_

#### booster

```solidity
address booster
```

_Booster contract address_

#### rewardMultiplier

```solidity
uint256 rewardMultiplier
```

_Reward multiplier_

#### feeDebtOf

```solidity
mapping(uint16 => uint256) feeDebtOf
```

_src chain ID mapped to total feeDebt_

#### settledFeeDebtOf

```solidity
mapping(uint16 => uint256) settledFeeDebtOf
```

_src chain ID mapped to total settled feeDebt_

#### distributedFeeDebtOf

```solidity
mapping(uint16 => uint256) distributedFeeDebtOf
```

_src chain ID mapped to total distributed feeDebt_

#### bridgeDelegates

```solidity
mapping(uint16 => address) bridgeDelegates
```

_src chain ID to bridgeDelegate_

#### l2Coordinators

```solidity
mapping(uint16 => address) l2Coordinators
```

_src chain ID to L2Coordinator address_

#### distributors

```solidity
mapping(address => bool) distributors
```

_sender to isDistributor_

#### BridgeDelegateUpdated

```solidity
event BridgeDelegateUpdated(uint16 srcChainId, address bridgeDelegate)
```

**Parameters**

| Name           | Type    | Description                  |
| -------------- | ------- | ---------------------------- |
| srcChainId     | uint16  | Source chain ID              |
| bridgeDelegate | address | The bridge delegate contract |

#### L2CoordinatorUpated

```solidity
event L2CoordinatorUpated(uint16 srcChainId, address l2Coordinator)
```

**Parameters**

| Name          | Type    | Description                |
| ------------- | ------- | -------------------------- |
| srcChainId    | uint16  | Source chain ID            |
| l2Coordinator | address | The l2Coordinator contract |

#### DisributorUpdated

```solidity
event DisributorUpdated(address distributor, bool active)
```

**Parameters**

| Name        | Type    | Description                       |
| ----------- | ------- | --------------------------------- |
| distributor | address | Distributor address               |
| active      | bool    | If they are an active distributor |

#### FeeDebtNotified

```solidity
event FeeDebtNotified(uint16 srcChainId, uint256 amount)
```

**Parameters**

| Name       | Type    | Description                     |
| ---------- | ------- | ------------------------------- |
| srcChainId | uint16  | Source chain ID                 |
| amount     | uint256 | Amount of fee that was notified |

#### AuraDistributed

```solidity
event AuraDistributed(uint16 srcChainId, uint256 amount)
```

**Parameters**

| Name       | Type    | Description                         |
| ---------- | ------- | ----------------------------------- |
| srcChainId | uint16  | Source chain ID                     |
| amount     | uint256 | Amount of AURA that was distributed |

#### FeeDebtSettled

```solidity
event FeeDebtSettled(uint16 srcChainId, uint256 amount)
```

**Parameters**

| Name       | Type    | Description                         |
| ---------- | ------- | ----------------------------------- |
| srcChainId | uint16  | Source chain ID                     |
| amount     | uint256 | Amount of fee debt that was settled |

#### RewardMultiplierUpdated

```solidity
event RewardMultiplierUpdated(uint256 multiplier)
```

**Parameters**

| Name       | Type    | Description           |
| ---------- | ------- | --------------------- |
| multiplier | uint256 | The reward multiplier |

#### BoosterUpdated

```solidity
event BoosterUpdated(address booster)
```

**Parameters**

| Name    | Type    | Description          |
| ------- | ------- | -------------------- |
| booster | address | The booster contract |

#### constructor

```solidity
constructor(address _lzEndpoint, address _booster, address _balToken, address _auraToken, address _auraOFT, address _treasury) public
```

#### setAdapterParams

```solidity
function setAdapterParams(uint16 _srcChainId, bytes32 _selector, bytes _adapterParams) external
```

_Sets the configuration for a given source chain ID and selector._

**Parameters**

| Name            | Type    | Description          |
| --------------- | ------- | -------------------- |
| \_srcChainId    | uint16  | The source chain ID. |
| \_selector      | bytes32 | The selector.        |
| \_adapterParams | bytes   | The adapter params.  |

#### setBridgeDelegate

```solidity
function setBridgeDelegate(uint16 _srcChainId, address _bridgeDelegate) external
```

_Set bridge delegate for given srcChainId_

**Parameters**

| Name             | Type    | Description                    |
| ---------------- | ------- | ------------------------------ |
| \_srcChainId     | uint16  | ID of the source chain         |
| \_bridgeDelegate | address | Address of the bridge delegate |

#### setL2Coordinator

```solidity
function setL2Coordinator(uint16 _srcChainId, address _l2Coordinator) external
```

_Set L2 Coordinator for given srcChainId_

**Parameters**

| Name            | Type    | Description              |
| --------------- | ------- | ------------------------ |
| \_srcChainId    | uint16  | ID of the source chain   |
| \_l2Coordinator | address | Address of l2Coordinator |

#### setDistributor

```solidity
function setDistributor(address _distributor, bool _active) external
```

_Set distributor as valid or invalid so the can call harvest_

**Parameters**

| Name          | Type    | Description               |
| ------------- | ------- | ------------------------- |
| \_distributor | address | Distributor address       |
| \_active      | bool    | Is the distributor active |

#### setRewardMultiplier

```solidity
function setRewardMultiplier(uint256 _multiplier) external
```

_Set the reward multiplier_

**Parameters**

| Name         | Type    | Description        |
| ------------ | ------- | ------------------ |
| \_multiplier | uint256 | The new multiplier |

#### setBooster

```solidity
function setBooster(address _booster) external
```

_Set the booster address_

**Parameters**

| Name      | Type    | Description                  |
| --------- | ------- | ---------------------------- |
| \_booster | address | The booster contract address |

#### distributeAura

```solidity
function distributeAura(uint16 _srcChainId, address _zroPaymentAddress, address _sendFromZroPaymentAddress, bytes _sendFromAdapterParams) external payable
```

_Distribute AURA to the src chain using the BAL float in this contract mint AURA by calling distributeL2Fees on the Booster and then send those AURA tokens to the src chain_

#### settleFeeDebt

```solidity
function settleFeeDebt(uint16 _srcChainId, uint256 _amount) external
```

_Receive CRV from the L2 via some thirdpart bridge to settle the feeDebt for the remote chain_

#### receive

```solidity
receive() external payable
```

### L2Coordinator

_Coordinates LZ messages and actions from the L1 on the L2_

#### canonicalChainId

```solidity
uint16 canonicalChainId
```

_canonical chain ID_

#### auraOFT

```solidity
address auraOFT
```

_AuraOFT contract_

#### booster

```solidity
address booster
```

_Booster contract_

#### balToken

```solidity
address balToken
```

_The BAL token contract_

#### bridgeDelegate

```solidity
address bridgeDelegate
```

_The bridge delegate contract_

#### accBalRewards

```solidity
uint256 accBalRewards
```

_Accumulated BAL rewards_

#### accAuraRewards

```solidity
uint256 accAuraRewards
```

_Accumulated AURA rewards_

#### BridgeDelegateUpdated

```solidity
event BridgeDelegateUpdated(address bridgeDelegate)
```

_Emitted when the bridge delegate address is updated._

**Parameters**

| Name           | Type    | Description                      |
| -------------- | ------- | -------------------------------- |
| bridgeDelegate | address | The new bridge delegate address. |

#### RewardAdded

```solidity
event RewardAdded(address token, uint256 reward)
```

_Emmitted when rewards are added_

**Parameters**

| Name   | Type    | Description       |
| ------ | ------- | ----------------- |
| token  | address | The reward token  |
| reward | uint256 | The reward amount |

#### constructor

```solidity
constructor(address _auraOFT, uint16 _canonicalChainId) public
```

#### mintRate

```solidity
function mintRate() public view returns (uint256)
```

Get mint rate based on accumulated BAL and AURA

_Dev mint rate is aura per bal rewards_

#### initialize

```solidity
function initialize(address _booster, address _balToken, address _lzEndpoint) external
```

Initializes the booster and balToken addresses

_This function should only be called by the owner of the contract_

**Parameters**

| Name         | Type    | Description                 |
| ------------ | ------- | --------------------------- |
| \_booster    | address | Address of the booster      |
| \_balToken   | address | Address of the balToken     |
| \_lzEndpoint | address | LayerZero endpoint contract |

#### setBridgeDelegate

```solidity
function setBridgeDelegate(address _bridgeDelegate) external
```

#### setAdapterParams

```solidity
function setAdapterParams(uint16 _srcChainId, bytes32 _selector, bytes _adapterParams) external
```

_Sets the configuration for a given source chain ID and selector._

**Parameters**

| Name            | Type    | Description          |
| --------------- | ------- | -------------------- |
| \_srcChainId    | uint16  | The source chain ID. |
| \_selector      | bytes32 | The selector.        |
| \_adapterParams | bytes   | The adapter params.  |

#### mint

```solidity
function mint(address _to, uint256 _amount) external
```

_Mint function called by Booster.rewardClaimed. uses the CVX (L2) balance of this contract to transfer tokens_

**Parameters**

| Name     | Type    | Description                                 |
| -------- | ------- | ------------------------------------------- |
| \_to     | address | Address to send CVX (L2) to                 |
| \_amount | uint256 | Amount of CRV rewardClaimed was called with |

#### queueNewRewards

```solidity
function queueNewRewards(address _originalSender, uint256 _fees, uint256 _rewards, address _zroPaymentAddress) external payable
```

_Called by the booster.earmarkRewards to register feeDebt with the L1 and receive CVX tokens in return_

**Parameters**

| Name                | Type    | Description                                            |
| ------------------- | ------- | ------------------------------------------------------ |
| \_originalSender    | address | Sender that initiated the Booster call                 |
| \_fees              | uint256 | Amount of CRV that was received as fees                |
| \_rewards           | uint256 | Amount of CRV that was received by the reward contract |
| \_zroPaymentAddress | address | The LayerZero ZRO payment address                      |

### PausableOFT

Extension to the OFT standard that allows a `guardian` address to perform an emergency pause on the `sendFrom` function.

#### constructor

```solidity
constructor(string _name, string _symbol) public
```

_Constructs the PausableOFT contract_

**Parameters**

| Name     | Type   | Description          |
| -------- | ------ | -------------------- |
| \_name   | string | The oft token name   |
| \_symbol | string | The oft token symbol |

#### sendFrom

```solidity
function sendFrom(address _from, uint16 _dstChainId, bytes _toAddress, uint256 _amount, address payable _refundAddress, address _zroPaymentAddress, bytes _adapterParams) public payable
```

_Override sendFrom to add pause modifier_

### PausableProxyOFT

Extension to the ProxyOFT standard that allows a `guardian` address to perform an emergency pause.

* When paused all messages received are added to a queue to be processed after `queueDelay` time has passed.
* When paused no messages can be sent via `sendFrom`.

#### epochDuration

```solidity
uint256 epochDuration
```

_Duration of each inflow epoch_

#### sudo

```solidity
address sudo
```

_Addres of super user_

#### inflowLimit

```solidity
uint256 inflowLimit
```

_Transfer inflow limit per epoch_

#### queueDelay

```solidity
uint256 queueDelay
```

_Queue delay_

#### outflow

```solidity
mapping(uint256 => uint256) outflow
```

_Epoch mapped to transfer outflow_

#### inflow

```solidity
mapping(uint256 => uint256) inflow
```

_Epoch mapped to transfer inflow_

#### queue

```solidity
mapping(bytes32 => bool) queue
```

_Transfer queue_

#### QueuedFromChain

```solidity
event QueuedFromChain(uint256 epoch, uint16 srcChainId, address to, uint256 amount, uint256 timestamp)
```

**Parameters**

| Name       | Type    | Description                  |
| ---------- | ------- | ---------------------------- |
| epoch      | uint256 | The epoch                    |
| srcChainId | uint16  | The source chain             |
| to         | address | Address to transfer to       |
| amount     | uint256 | Amount to transfer           |
| timestamp  | uint256 | Time the transfer was queued |

#### Rescue

```solidity
event Rescue(address token, address to, uint256 amount)
```

**Parameters**

| Name   | Type    | Description     |
| ------ | ------- | --------------- |
| token  | address | Token address   |
| to     | address | Send to address |
| amount | uint256 | Amount to send  |

#### constructor

```solidity
constructor(address _token, address _sudo, uint256 _inflowLimit) public
```

**Parameters**

| Name          | Type    | Description                      |
| ------------- | ------- | -------------------------------- |
| \_token       | address | Proxy token (eg AURA or auraBAL) |
| \_sudo        | address | The super user address           |
| \_inflowLimit | uint256 | Initial inflow limit per epoch   |

#### setQueueDelay

```solidity
function setQueueDelay(uint256 _delay) external
```

_Amount of time that a transfer has to sit in the queue until it can be processed_

**Parameters**

| Name    | Type    | Description |
| ------- | ------- | ----------- |
| \_delay | uint256 | Queue delay |

#### setInflowLimit

```solidity
function setInflowLimit(uint256 _limit) external
```

_Set the inflow limit per epoch_

**Parameters**

| Name    | Type    | Description            |
| ------- | ------- | ---------------------- |
| \_limit | uint256 | Inflow limit per epoch |

#### getCurrentEpoch

```solidity
function getCurrentEpoch() external view returns (uint256)
```

_Get current epoch_

#### sendFrom

```solidity
function sendFrom(address _from, uint16 _dstChainId, bytes _toAddress, uint256 _amount, address payable _refundAddress, address _zroPaymentAddress, bytes _adapterParams) public payable
```

_Override sendFrom to add pause modifier_

#### processQueued

```solidity
function processQueued(uint256 _epoch, uint16 _srcChainId, address _to, uint256 _amount, uint256 _timestamp) external
```

_Process a queued transfer Transfer has to be a valid root and the queue delay has to have passed_

**Parameters**

| Name         | Type    | Description                        |
| ------------ | ------- | ---------------------------------- |
| \_epoch      | uint256 | Epoch                              |
| \_srcChainId | uint16  | Source chain ID                    |
| \_to         | address | Address to transfer to             |
| \_amount     | uint256 | Amount to transfer                 |
| \_timestamp  | uint256 | Time when this transfer was queued |

#### rescue

```solidity
function rescue(address _token, address _to, uint256 _amount) external virtual
```

_In a doomsday bridge scenario there may be a case where funds need to be rescued._

**Parameters**

| Name     | Type    | Description     |
| -------- | ------- | --------------- |
| \_token  | address | Token address   |
| \_to     | address | Send to address |
| \_amount | uint256 | Amount to send  |

### PauseGuardian

_Contract module which allows children to implement an emergency stop mechanism that can be triggered by an authorized immutable guardian address._

#### guardian

```solidity
address guardian
```

_The guardian address_

#### pause

```solidity
function pause() external
```

This function pauses the contract.

_This function can only be called by the 'guardian'._

#### unpause

```solidity
function unpause() external
```

This function is used to unpause the contract.

_This function can only be called by the 'guardian' of the contract._

### IArbitrumGatewayRouter

#### outboundTransfer

```solidity
function outboundTransfer(address _l1Token, address _to, uint256 _amount, bytes _data) external payable returns (bytes)
```

### ArbitrumBridgeSender

#### gatewayRouter

```solidity
address gatewayRouter
```

_The Gnosis bridge address_

#### l1Crv

```solidity
address l1Crv
```

_Mainnet CRV token address_

#### constructor

```solidity
constructor(address _gatewayRouter, address _crv, address _l1Crv) public
```

_Constructs the GnosisBridgeSender contract._

**Parameters**

| Name            | Type    | Description                         |
| --------------- | ------- | ----------------------------------- |
| \_gatewayRouter | address | The arbitrum gatewayRouter address. |
| \_crv           | address | The L2 token address.               |
| \_l1Crv         | address | The L1 token address.               |

#### send

```solidity
function send(uint256 _amount) external
```

\_Function to send a specified amount of tokens. Requirements:

* The caller must be the owner of the contract.\_

**Parameters**

| Name     | Type    | Description                         |
| -------- | ------- | ----------------------------------- |
| \_amount | uint256 | The amount of CRV tokens to be sent |

### BridgeDelegateReceiver

_Receive bridged tokens from the L2 on L1_

#### l1Coordinator

```solidity
address l1Coordinator
```

_The L1Coordinator address_

#### srcChainId

```solidity
uint16 srcChainId
```

_The source chain id_

#### SettleFeeDebt

```solidity
event SettleFeeDebt(uint256 amount)
```

_Emitted when tokens are sent to a recipient._

**Parameters**

| Name   | Type    | Description                     |
| ------ | ------- | ------------------------------- |
| amount | uint256 | The amount of fee debt settled. |

#### constructor

```solidity
constructor(address _l1Coordinator, uint16 _srcChainId) public
```

_Constructs the BridgeDelegateReceiver contract._

**Parameters**

| Name            | Type    | Description                        |
| --------------- | ------- | ---------------------------------- |
| \_l1Coordinator | address | The address of the L1 Coordinator. |
| \_srcChainId    | uint16  | The source chain id.               |

#### settleFeeDebt

```solidity
function settleFeeDebt(uint256 _amount) external
```

_Settle fee debt on the L1 Coordinator._

**Parameters**

| Name     | Type    | Description                                                                             |
| -------- | ------- | --------------------------------------------------------------------------------------- |
| \_amount | uint256 | The amount debt to settle Requirements: - The caller must be the owner of the contract. |

### BridgeDelegateSender

_Sends tokens to L1 via a bridge_

#### crv

```solidity
address crv
```

_The crv token address_

#### l1Receiver

```solidity
address l1Receiver
```

_The L1Receiver address_

#### Send

```solidity
event Send(address to, uint256 amount)
```

_Emitted when tokens are sent to a recipient._

**Parameters**

| Name   | Type    | Description                   |
| ------ | ------- | ----------------------------- |
| to     | address | The address of the recipient. |
| amount | uint256 | The amount of tokens sent.    |

#### L1ReceiverUpated

```solidity
event L1ReceiverUpated(address l1Receiver)
```

_Emitted when the l1 receiver address is updated._

**Parameters**

| Name       | Type    | Description                  |
| ---------- | ------- | ---------------------------- |
| l1Receiver | address | The new l1 receiver address. |

#### setL1Receiver

```solidity
function setL1Receiver(address _l1Receiver) external
```

Allows the owner of the contract to set the L1 receiver address.

_This function requires the owner of the contract to call it and pass in a valid address. If the address is valid, the l1Receiver variable is updated and an event is emitted._

**Parameters**

| Name         | Type    | Description                  |
| ------------ | ------- | ---------------------------- |
| \_l1Receiver | address | The new l1 receiver address. |

#### send

```solidity
function send(uint256 _amount) external virtual
```

_Function to send a specified amount of tokens_

**Parameters**

| Name     | Type    | Description                     |
| -------- | ------- | ------------------------------- |
| \_amount | uint256 | The amount of tokens to be sent |

### SimpleBridgeDelegateSender

_Only used as an example and for tests Sends tokens to "L1" via a simple transfer_

#### token

```solidity
address token
```

_The ERC20 bridged token address._

#### constructor

```solidity
constructor(address _token) public
```

_Constructs the SimpleBridgeDelegateSender contract._

**Parameters**

| Name    | Type    | Description                                |
| ------- | ------- | ------------------------------------------ |
| \_token | address | The address of the ERC20 token to be sent. |

#### send

```solidity
function send(uint256 _amount) external
```

_Sends tokens to a l1Receiver._

**Parameters**

| Name     | Type    | Description                                                                                 |
| -------- | ------- | ------------------------------------------------------------------------------------------- |
| \_amount | uint256 | The amount of tokens to send. Requirements: - The caller must be the owner of the contract. |

### SidechainClaimZap

Claim zap to bundle various reward claims

_Claims from all pools, Bridges/Locks to L1 if Wanted and compounds if needed._

#### receive

```solidity
receive() external payable
```

#### cvx

```solidity
address cvx
```

#### cvxCrv

```solidity
address cvxCrv
```

#### compounder

```solidity
address compounder
```

#### owner

```solidity
address owner
```

#### canonicalChainID

```solidity
uint16 canonicalChainID
```

#### ClaimRewardsAmounts

```solidity
struct ClaimRewardsAmounts {
  uint256 lockCvxMaxAmount;
  uint256 depositCvxCrvMaxAmount;
  uint256 bridgeCvxMaxAmount;
}
```

#### Options

```solidity
struct Options {
  bool useAllWalletFunds;
  bool sendCvxToL1;
  bool lockCvxL1;
  bool useCompounder;
  bool refundEth;
  bool overrideL1Receiver;
  address l1Receiever;
  address zro;
  bytes adapterParams;
}
```

#### initialize

```solidity
function initialize(address _owner, address _cvx, address _cvxCrv, address _compounder) external
```

#### getName

```solidity
function getName() external pure returns (string)
```

Returns meta data of contract

#### setApprovals

```solidity
function setApprovals() external
```

Approve spending of: cvxCrv -> Compounder

#### claimRewards

```solidity
function claimRewards(address zroPaymentAddress, address[] rewardContracts, address[] extraRewardContracts, address[] tokenRewardContracts, address[] tokenRewardTokens, struct SidechainClaimZap.ClaimRewardsAmounts amounts, struct SidechainClaimZap.Options options) external payable
```

Claim all the rewards

**Parameters**

| Name                 | Type                                         | Description                                                           |
| -------------------- | -------------------------------------------- | --------------------------------------------------------------------- |
| zroPaymentAddress    | address                                      |                                                                       |
| rewardContracts      | address\[]                                   | Array of addresses for LP token rewards                               |
| extraRewardContracts | address\[]                                   | Array of addresses for extra rewards                                  |
| tokenRewardContracts | address\[]                                   | Array of addresses for token rewards e.g vlCvxExtraRewardDistribution |
| tokenRewardTokens    | address\[]                                   | Array of token reward addresses to use with tokenRewardContracts      |
| amounts              | struct SidechainClaimZap.ClaimRewardsAmounts | Claim rewards amounts.                                                |
| options              | struct SidechainClaimZap.Options             | Claim options                                                         |

### SimpleStrategy

Simple strategy to harvest all vault's extra rewards and queue them.

#### auraBalToken

```solidity
address auraBalToken
```

_The $AURABAL token address_

#### vault

```solidity
address vault
```

_The AuraBal compounder vault address_

#### constructor

```solidity
constructor(address _auraBalToken, address _vault) public
```

_Simple constructor_

**Parameters**

| Name           | Type    | Description                  |
| -------------- | ------- | ---------------------------- |
| \_auraBalToken | address | The $AURABAL token address   |
| \_vault        | address | The AuraBal compounder vault |

#### harvest

```solidity
function harvest() external returns (uint256)
```

#### setApprovals

```solidity
function setApprovals() external
```

#### stake

```solidity
function stake(uint256 _amount) external
```

#### totalUnderlying

```solidity
function totalUnderlying() external view returns (uint256)
```

#### withdraw

```solidity
function withdraw(uint256 _amount) external
```

Allows the Vault to withdraw a given amount of auraBal.

_Only callable by the vault._

**Parameters**

| Name     | Type    | Description                        |
| -------- | ------- | ---------------------------------- |
| \_amount | uint256 | The amount of auraBal to withdraw. |

#### harvest

```solidity
function harvest(uint256) public returns (uint256 harvested)
```

This function is used to process extra rewards for the vault.

_This function will loop through the extra rewards and transfer the balance of the reward token to the rewards address. It will then queue the new rewards with the balance._

#### PoolInfo

```solidity
struct PoolInfo {
  address lptoken;
  address token;
  address gauge;
  address crvRewards;
  address stash;
  bool shutdown;
}
```

#### earmarkRewards

```solidity
function earmarkRewards(uint256 _pid, address _zroPaymentAddress) external payable returns (bool)
```

#### poolLength

```solidity
function poolLength() external view returns (uint256)
```

#### lockRewards

```solidity
function lockRewards() external view returns (address)
```

#### poolInfo

```solidity
function poolInfo(uint256 _pid) external view returns (struct IBoosterLite.PoolInfo poolInfo)
```

#### lockIncentive

```solidity
function lockIncentive() external view returns (uint256)
```

#### stakerIncentive

```solidity
function stakerIncentive() external view returns (uint256)
```

#### earmarkIncentive

```solidity
function earmarkIncentive() external view returns (uint256)
```

#### platformFee

```solidity
function platformFee() external view returns (uint256)
```

#### FEE\_DENOMINATOR

```solidity
function FEE_DENOMINATOR() external view returns (uint256)
```

### AuraClaimZapV3

Claim zap to bundle various reward claims

_Claims from all pools, and stakes cvxCrv and CVX if wanted. v2: - change exchange to use curve pool - add getReward(address,token) type - add option to lock cvx - add option use all funds in wallet v3: - add option to deposit to compounder - reduce calls to cvxcrv rewards/compounder - removed enum and option bitshifting - introduced options struct - gas optimisation on use all funds balances - helper functions to reduce code repetition_

#### crv

```solidity
address crv
```

#### cvx

```solidity
address cvx
```

#### cvxCrv

```solidity
address cvxCrv
```

#### crvDepositWrapper

```solidity
address crvDepositWrapper
```

#### cvxCrvRewards

```solidity
address cvxCrvRewards
```

#### locker

```solidity
address locker
```

#### owner

```solidity
address owner
```

#### compounder

```solidity
address compounder
```

#### ClaimRewardsAmounts

```solidity
struct ClaimRewardsAmounts {
  uint256 depositCrvMaxAmount;
  uint256 minAmountOut;
  uint256 depositCvxMaxAmount;
  uint256 depositCvxCrvMaxAmount;
}
```

#### Options

```solidity
struct Options {
  bool claimCvxCrv;
  bool claimLockedCvx;
  bool lockCvxCrv;
  bool lockCrvDeposit;
  bool useAllWalletFunds;
  bool useCompounder;
  bool lockCvx;
}
```

#### constructor

```solidity
constructor(address _crv, address _cvx, address _cvxCrv, address _crvDepositWrapper, address _cvxCrvRewards, address _locker, address _compounder) public
```

**Parameters**

| Name                | Type    | Description                                                     |
| ------------------- | ------- | --------------------------------------------------------------- |
| \_crv               | address | CRV token (0xD533a949740bb3306d119CC777fa900bA034cd52);         |
| \_cvx               | address | CVX token (0x4e3FBD56CD56c3e72c1403e103b45Db9da5B9D2B);         |
| \_cvxCrv            | address | cvxCRV token (0x62B9c7356A2Dc64a1969e19C23e4f579F9810Aa7);      |
| \_crvDepositWrapper | address | crvDepositWrapper (0x8014595F2AB54cD7c604B00E9fb932176fDc86Ae); |
| \_cvxCrvRewards     | address | cvxCrvRewards (0x3Fe65692bfCD0e6CF84cB1E7d24108E434A7587e);     |
| \_locker            | address | vlCVX (0xD18140b4B819b895A3dba5442F959fA44994AF50);             |
| \_compounder        | address | cvxCrv autocompounder vault                                     |

#### getName

```solidity
function getName() external pure returns (string)
```

Returns meta data of contract.

#### setApprovals

```solidity
function setApprovals() external
```

Approve spending of: crv -> crvDepositor cvxCrv -> cvxCrvRewards cvxCrv -> Compounder cvx -> Locker

#### claimRewards

```solidity
function claimRewards(address[] rewardContracts, address[] extraRewardContracts, address[] tokenRewardContracts, address[] tokenRewardTokens, struct AuraClaimZapV3.ClaimRewardsAmounts amounts, struct AuraClaimZapV3.Options options) external
```

Claim all the rewards

**Parameters**

| Name                 | Type                                      | Description                                                           |
| -------------------- | ----------------------------------------- | --------------------------------------------------------------------- |
| rewardContracts      | address\[]                                | Array of addresses for LP token rewards                               |
| extraRewardContracts | address\[]                                | Array of addresses for extra rewards                                  |
| tokenRewardContracts | address\[]                                | Array of addresses for token rewards e.g vlCvxExtraRewardDistribution |
| tokenRewardTokens    | address\[]                                | Array of token reward addresses to use with tokenRewardContracts      |
| amounts              | struct AuraClaimZapV3.ClaimRewardsAmounts | Claim rewards amounts.                                                |
| options              | struct AuraClaimZapV3.Options             | Claim options                                                         |

### AuraViewHelpers

View-only contract to combine calls

_IMPORTANT: These functions are extremely gas-intensive and should not be called from within a transaction._

#### balancerVault

```solidity
contract IBalancerVault balancerVault
```

#### Token

```solidity
struct Token {
  address addr;
  uint8 decimals;
  string symbol;
  string name;
}
```

#### Pool

```solidity
struct Pool {
  uint256 pid;
  address lptoken;
  address token;
  address gauge;
  address crvRewards;
  address stash;
  bool shutdown;
  address rewardToken;
  bytes32 poolId;
  uint256[] normalizedWeights;
  address[] poolTokens;
  uint256[] underlying;
  uint256 totalSupply;
  struct AuraViewHelpers.RewardsData rewardsData;
  struct AuraViewHelpers.ExtraRewards[] extraRewards;
}
```

#### Vault

```solidity
struct Vault {
  address addr;
  address underlying;
  uint256 totalUnderlying;
  uint256 totalSupply;
  uint256 withdrawalPenalty;
  struct AuraViewHelpers.ExtraRewards[] extraRewards;
}
```

#### VaultAccount

```solidity
struct VaultAccount {
  address addr;
  uint256 balance;
  uint256 balanceOfUnderlying;
  uint256[] extraRewardsEarned;
}
```

#### Locker

```solidity
struct Locker {
  uint256 epoch;
  uint256 totalSupply;
  uint256 lockedSupply;
  struct AuraViewHelpers.RewardsData rewardsData;
}
```

#### LockerAccount

```solidity
struct LockerAccount {
  address addr;
  uint256 total;
  uint256 unlockable;
  uint256 locked;
  uint256 nextUnlockIndex;
  uint128 rewardPerTokenPaid;
  uint128 rewards;
  address delegate;
  uint256 votes;
  struct AuraLocker.LockedBalance[] lockData;
  struct AuraLocker.EarnedData[] claimableRewards;
}
```

#### RewardsData

```solidity
struct RewardsData {
  uint256 periodFinish;
  uint256 lastUpdateTime;
  uint256 rewardRate;
  uint256 rewardPerTokenStored;
  uint256 queuedRewards;
}
```

#### ExtraRewards

```solidity
struct ExtraRewards {
  address addr;
  address rewardsToken;
  struct AuraViewHelpers.RewardsData rewardsData;
}
```

#### PoolBalances

```solidity
struct PoolBalances {
  uint256 pid;
  uint256 earned;
  uint256[] extraRewardsEarned;
  uint256 staked;
}
```

#### getVault

```solidity
function getVault(address _vault) external view returns (struct AuraViewHelpers.Vault vault)
```

#### getVaultAccount

```solidity
function getVaultAccount(address _vault, address _account) external view returns (struct AuraViewHelpers.VaultAccount vaultAccount)
```

#### getLocker

```solidity
function getLocker(address _locker) external view returns (struct AuraViewHelpers.Locker locker)
```

#### getLockerAccount

```solidity
function getLockerAccount(address _locker, address _account) external view returns (struct AuraViewHelpers.LockerAccount lockerAccount)
```

#### getPools

```solidity
function getPools(address _booster) external view returns (struct AuraViewHelpers.Pool[])
```

#### getCvxCrvRewards

```solidity
function getCvxCrvRewards(address _cvxCrvRewards) public view returns (struct AuraViewHelpers.Pool)
```

#### getPool

```solidity
function getPool(struct IBooster.PoolInfo poolInfo, uint256 _pid) public view returns (struct AuraViewHelpers.Pool)
```

#### getPoolsBalances

```solidity
function getPoolsBalances(address _booster, address _account) external view returns (struct AuraViewHelpers.PoolBalances[])
```

#### getPoolBalances

```solidity
function getPoolBalances(address _rewardPool, uint256 _pid, address _account) public view returns (struct AuraViewHelpers.PoolBalances)
```

#### getTokens

```solidity
function getTokens(address[] _addresses) public view returns (struct AuraViewHelpers.Token[])
```

#### getEarmarkingReward

```solidity
function getEarmarkingReward(uint256 pool, address booster, address token) public returns (uint256 pending)
```

#### getMultipleEarmarkingRewards

```solidity
function getMultipleEarmarkingRewards(uint256[] pools, address booster, address token) external returns (uint256[] pendings)
```

### BoosterLiteHelper

Invokes booster.earmarkRewards for multiple pools on BoosterLite

_Allows anyone to call `earmarkRewards` via the booster._

#### booster

```solidity
contract IBoosterLite booster
```

#### crv

```solidity
address crv
```

#### constructor

```solidity
constructor(address _booster, address _crv) public
```

**Parameters**

| Name      | Type    | Description                                         |
| --------- | ------- | --------------------------------------------------- |
| \_booster | address | Booster.sol                                         |
| \_crv     | address | Crv e.g. 0xba100000625a3754423978a60c9317c58a424e3D |

#### earmarkRewards

```solidity
function earmarkRewards(uint256[] _pids, address _zroPaymentAddress) external payable returns (uint256)
```

#### processIdleRewards

```solidity
function processIdleRewards(uint256[] _pids) external
```

Invoke processIdleRewards for each pool id.

**Parameters**

| Name   | Type       | Description       |
| ------ | ---------- | ----------------- |
| \_pids | uint256\[] | Array of pool ids |





## Convex Platform "Lite" Contracts

The convex platform contracts developed for the sidechain are ‘lite’ variations of those used on Ethereum. In practice this means that contracts have functionality changed or removed, multiple contracts are merged together or contracts are not utilised on the sidechain. &#x20;

* BaseRewardPool4626
* BoosterOwnerLite&#x20;
* BoosterLite&#x20;
* PoolManagerLite
* VoterProxyLite

### BaseRewardPool4626

Simply wraps the BaseRewardPool with the new IERC4626 Vault standard functions.

_See https://github.com/fei-protocol/ERC4626/blob/main/src/interfaces/IERC4626.sol#L58 This is not so much a vault as a Reward Pool, therefore asset:share ratio is always 1:1. To create most utility for this RewardPool, the "asset" has been made to be the crvLP token, as opposed to the cvxLP token. Therefore, users can easily deposit crvLP, and it will first go to the Booster and mint the cvxLP before performing the normal staking function._

#### asset

```solidity
address asset
```

The address of the underlying ERC20 token used for the Vault for accounting, depositing, and withdrawing.

#### constructor

```solidity
constructor(uint256 pid_, address stakingToken_, address rewardToken_, address operator_, address rewardManager_, address lptoken_) public
```

_See BaseRewardPool.sol_

#### totalAssets

```solidity
function totalAssets() external view virtual returns (uint256)
```

Total amount of the underlying asset that is "managed" by Vault.

#### deposit

```solidity
function deposit(uint256 assets, address receiver) public virtual returns (uint256)
```

Mints `shares` Vault shares to `receiver`.

_Because `asset` is not actually what is collected here, first wrap to required token in the booster._

#### mint

```solidity
function mint(uint256 shares, address receiver) external virtual returns (uint256)
```

Mints exactly `shares` Vault shares to `receiver` by depositing `assets` of underlying tokens.

#### withdraw

```solidity
function withdraw(uint256 assets, address receiver, address owner) public virtual returns (uint256)
```

Redeems `shares` from `owner` and sends `assets` of underlying tokens to `receiver`.

#### redeem

```solidity
function redeem(uint256 shares, address receiver, address owner) external virtual returns (uint256)
```

Redeems `shares` from `owner` and sends `assets` of underlying tokens to `receiver`.

#### convertToShares

```solidity
function convertToShares(uint256 assets) public view virtual returns (uint256)
```

The amount of shares that the vault would exchange for the amount of assets provided, in an ideal scenario where all the conditions are met.

#### convertToAssets

```solidity
function convertToAssets(uint256 shares) public view virtual returns (uint256)
```

The amount of assets that the vault would exchange for the amount of shares provided, in an ideal scenario where all the conditions are met.

#### maxDeposit

```solidity
function maxDeposit(address) public view virtual returns (uint256)
```

Total number of underlying assets that can be deposited by `owner` into the Vault, where `owner` corresponds to the input parameter `receiver` of a `deposit` call.

#### previewDeposit

```solidity
function previewDeposit(uint256 assets) external view virtual returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their deposit at the current block, given current on-chain conditions.

#### maxMint

```solidity
function maxMint(address owner) external view virtual returns (uint256)
```

Total number of underlying shares that can be minted for `owner`, where `owner` corresponds to the input parameter `receiver` of a `mint` call.

#### previewMint

```solidity
function previewMint(uint256 shares) external view virtual returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their mint at the current block, given current on-chain conditions.

#### maxWithdraw

```solidity
function maxWithdraw(address owner) public view virtual returns (uint256)
```

Total number of underlying assets that can be withdrawn from the Vault by `owner`, where `owner` corresponds to the input parameter of a `withdraw` call.

#### previewWithdraw

```solidity
function previewWithdraw(uint256 assets) public view virtual returns (uint256 shares)
```

Allows an on-chain or off-chain user to simulate the effects of their withdrawal at the current block, given current on-chain conditions.

#### maxRedeem

```solidity
function maxRedeem(address owner) external view virtual returns (uint256)
```

Total number of underlying shares that can be redeemed from the Vault by `owner`, where `owner` corresponds to the input parameter of a `redeem` call.

#### previewRedeem

```solidity
function previewRedeem(uint256 shares) external view virtual returns (uint256)
```

Allows an on-chain or off-chain user to simulate the effects of their redeemption at the current block, given current on-chain conditions.

#### name

```solidity
function name() external view returns (string)
```

_Returns the name of the token._

#### symbol

```solidity
function symbol() external view returns (string)
```

_Returns the symbol of the token._

#### decimals

```solidity
function decimals() external view returns (uint8)
```

_Returns the decimals places of the token._

#### totalSupply

```solidity
function totalSupply() public view returns (uint256)
```

_Returns the amount of tokens in existence._

#### balanceOf

```solidity
function balanceOf(address account) public view returns (uint256)
```

_Returns the amount of tokens owned by `account`._

#### transfer

```solidity
function transfer(address recipient, uint256 amount) external returns (bool)
```

\_Moves `amount` tokens from the caller's account to `recipient`.

Returns a boolean value indicating whether the operation succeeded.

Emits a {Transfer} event.\_

#### allowance

```solidity
function allowance(address owner, address spender) public view virtual returns (uint256)
```

_See {IERC20-allowance}._

#### approve

```solidity
function approve(address spender, uint256 amount) public virtual returns (bool)
```

\_See {IERC20-approve}.

Requirements:

* `spender` cannot be the zero address.\_

#### transferFrom

```solidity
function transferFrom(address owner, address recipient, uint256 amount) external returns (bool)
```

_Moves `amount` tokens from `sender` to `recipient` using the allowance mechanism. `amount` is then deducted from the caller's allowance._

### BoosterOwnerLite

Immutable booster owner that requires all pools to be shutdown before shutting down the entire convex system

_A timelock is required if forcing a shutdown if there is a bugged pool that can not be withdrawn from. Allow arbitrary calls to other contracts, but limit how calls are made to Booster. A lite version of the original Booster for use on sidechains_

#### poolManager

```solidity
address poolManager
```

#### booster

```solidity
address booster
```

#### stashFactory

```solidity
address stashFactory
```

#### rescueStash

```solidity
address rescueStash
```

#### owner

```solidity
address owner
```

#### pendingowner

```solidity
address pendingowner
```

#### isSealed

```solidity
bool isSealed
```

#### FORCE\_DELAY

```solidity
uint256 FORCE_DELAY
```

#### isForceTimerStarted

```solidity
bool isForceTimerStarted
```

#### forceTimestamp

```solidity
uint256 forceTimestamp
```

#### ShutdownStarted

```solidity
event ShutdownStarted(uint256 executableTimestamp)
```

#### ShutdownExecuted

```solidity
event ShutdownExecuted()
```

#### TransferOwnership

```solidity
event TransferOwnership(address pendingOwner)
```

#### AcceptedOwnership

```solidity
event AcceptedOwnership(address newOwner)
```

#### OwnershipSealed

```solidity
event OwnershipSealed()
```

#### constructor

```solidity
constructor(address _owner, address _poolManager, address _booster, address _stashFactory, address _rescueStash, bool _seal) public
```

**Parameters**

| Name           | Type    | Description                                                                                          |
| -------------- | ------- | ---------------------------------------------------------------------------------------------------- |
| \_owner        | address | Owner (e.g. CVX multisig)                                                                            |
| \_poolManager  | address | PoolManager (e.g. PoolManagerSecondaryProxy or 0xD20904e5916113D11414F083229e9C8C6F91D1e1)           |
| \_booster      | address | The booster (e.g. 0xF403C135812408BFbE8713b5A23a04b3D48AAE31)                                        |
| \_stashFactory | address | Creates stashes (e.g. 0x884da067B66677e72530df91eabb6e3CE69c2bE4)                                    |
| \_rescueStash  | address | Rescues tokens for subsequent vlCVX redistribution (e.g. 0x01140351069af98416cC08b16424b9E765436531) |
| \_seal         | bool    |                                                                                                      |

#### transferOwnership

```solidity
function transferOwnership(address _owner) external
```

#### acceptOwnership

```solidity
function acceptOwnership() external
```

#### sealOwnership

```solidity
function sealOwnership() external
```

#### setBoosterOwner

```solidity
function setBoosterOwner() external
```

#### setFactories

```solidity
function setFactories(address _rfactory, address _sfactory, address _tfactory) external
```

#### setFeeManager

```solidity
function setFeeManager(address _feeM) external
```

#### shutdownSystem

```solidity
function shutdownSystem() external
```

#### queueForceShutdown

```solidity
function queueForceShutdown() external
```

#### forceShutdownSystem

```solidity
function forceShutdownSystem() external
```

#### execute

```solidity
function execute(address _to, uint256 _value, bytes _data) external returns (bool, bytes)
```

#### setRescueTokenDistribution

```solidity
function setRescueTokenDistribution(address _distributor, address _rewardDeposit, address _treasury) external
```

#### setRescueTokenReward

```solidity
function setRescueTokenReward(address _token, uint256 _option) external
```

#### setStashExtraReward

```solidity
function setStashExtraReward(address _stash, address _token) external
```

#### setStashRewardHook

```solidity
function setStashRewardHook(address _stash, address _hook) external
```

#### setStashFactoryImplementation

```solidity
function setStashFactoryImplementation(address _v1, address _v2, address _v3) external
```

### BoosterLite

_A lite version of the original Booster for use on sidechains_

#### crv

```solidity
address crv
```

#### lockIncentive

```solidity
uint256 lockIncentive
```

#### stakerIncentive

```solidity
uint256 stakerIncentive
```

#### earmarkIncentive

```solidity
uint256 earmarkIncentive
```

#### platformFee

```solidity
uint256 platformFee
```

#### MaxFees

```solidity
uint256 MaxFees
```

#### FEE\_DENOMINATOR

```solidity
uint256 FEE_DENOMINATOR
```

#### owner

```solidity
address owner
```

#### feeManager

```solidity
address feeManager
```

#### poolManager

```solidity
address poolManager
```

#### staker

```solidity
address staker
```

#### minter

```solidity
address minter
```

#### rewardFactory

```solidity
address rewardFactory
```

#### stashFactory

```solidity
address stashFactory
```

#### tokenFactory

```solidity
address tokenFactory
```

#### treasury

```solidity
address treasury
```

#### rewards

```solidity
address rewards
```

#### isShutdown

```solidity
bool isShutdown
```

#### PoolInfo

```solidity
struct PoolInfo {
  address lptoken;
  address token;
  address gauge;
  address crvRewards;
  address stash;
  bool shutdown;
}
```

#### poolInfo

```solidity
struct BoosterLite.PoolInfo[] poolInfo
```

#### gaugeMap

```solidity
mapping(address => bool) gaugeMap
```

#### Deposited

```solidity
event Deposited(address user, uint256 poolid, uint256 amount)
```

#### Withdrawn

```solidity
event Withdrawn(address user, uint256 poolid, uint256 amount)
```

#### PoolAdded

```solidity
event PoolAdded(address lpToken, address gauge, address token, address rewardPool, address stash, uint256 pid)
```

#### PoolShutdown

```solidity
event PoolShutdown(uint256 poolId)
```

#### OwnerUpdated

```solidity
event OwnerUpdated(address newOwner)
```

#### FeeManagerUpdated

```solidity
event FeeManagerUpdated(address newFeeManager)
```

#### PoolManagerUpdated

```solidity
event PoolManagerUpdated(address newPoolManager)
```

#### FactoriesUpdated

```solidity
event FactoriesUpdated(address rewardFactory, address stashFactory, address tokenFactory)
```

#### RewardContractsUpdated

```solidity
event RewardContractsUpdated(address rewards)
```

#### FeesUpdated

```solidity
event FeesUpdated(uint256 lockIncentive, uint256 stakerIncentive, uint256 earmarkIncentive, uint256 platformFee)
```

#### TreasuryUpdated

```solidity
event TreasuryUpdated(address newTreasury)
```

#### constructor

```solidity
constructor(address _staker) public
```

_Constructor doing what constructors do. It is noteworthy that a lot of basic config is set to 0 - expecting subsequent calls setFeeManager etc._

**Parameters**

| Name     | Type    | Description                                           |
| -------- | ------- | ----------------------------------------------------- |
| \_staker | address | VoterProxyLite (locks the crv and adds to all gauges) |

#### initialize

```solidity
function initialize(address _minter, address _crv, address _owner) external
```

Initialize the contract.

**Parameters**

| Name     | Type    | Description                           |
| -------- | ------- | ------------------------------------- |
| \_minter | address | CVX token, or the thing that mints it |
| \_crv    | address | CRV Token address                     |
| \_owner  | address | Owner address                         |

#### setOwner

```solidity
function setOwner(address _owner) external
```

Owner is responsible for setting initial config, updating vote delegate and shutting system

#### setFeeManager

```solidity
function setFeeManager(address _feeM) external
```

Fee Manager can update the fees (lockIncentive, stakeIncentive, earmarkIncentive, platformFee)

#### setPoolManager

```solidity
function setPoolManager(address _poolM) external
```

Pool manager is responsible for adding new pools

#### setFactories

```solidity
function setFactories(address _rfactory, address _sfactory, address _tfactory) external
```

Factories are used when deploying new pools. Only the stash factory is mutable after init

#### setRewardContracts

```solidity
function setRewardContracts(address _rewards) external
```

Only called once, to set the addresses of cvxCrv (rewards)

#### setFees

```solidity
function setFees(uint256 _lockFees, uint256 _stakerFees, uint256 _callerFees, uint256 _platform) external
```

Fee manager can set all the relevant fees

**Parameters**

| Name         | Type    | Description                                   |
| ------------ | ------- | --------------------------------------------- |
| \_lockFees   | uint256 | % for cvxCrv stakers where 1% == 100          |
| \_stakerFees | uint256 | % for CVX stakers where 1% == 100             |
| \_callerFees | uint256 | % for whoever calls the claim where 1% == 100 |
| \_platform   | uint256 | % for "treasury" or vlCVX where 1% == 100     |

#### setTreasury

```solidity
function setTreasury(address _treasury) external
```

Set the address of the treasury (i.e. vlCVX)

#### poolLength

```solidity
function poolLength() external view returns (uint256)
```

END SETTER SECTION ///

#### addPool

```solidity
function addPool(address _lptoken, address _gauge, uint256 _stashVersion) external returns (bool)
```

Called by the PoolManager (i.e. PoolManagerProxy) to add a new pool - creates all the required contracts (DepositToken, RewardPool, Stash) and then adds to the list!

#### shutdownPool

```solidity
function shutdownPool(uint256 _pid) external returns (bool)
```

Shuts down the pool by withdrawing everything from the gauge to here (can later be claimed from depositors by using the withdraw fn) and marking it as shut down

#### shutdownSystem

```solidity
function shutdownSystem() external
```

Shuts down the WHOLE SYSTEM by withdrawing all the LP tokens to here and then allowing for subsequent withdrawal by any depositors.

#### deposit

```solidity
function deposit(uint256 _pid, uint256 _amount, bool _stake) public returns (bool)
```

Deposits an "\_amount" to a given gauge (specified by \_pid), mints a `DepositToken` and subsequently stakes that on Convex BaseRewardPool

#### depositAll

```solidity
function depositAll(uint256 _pid, bool _stake) external returns (bool)
```

Deposits all a senders balance to a given gauge (specified by \_pid), mints a `DepositToken` and subsequently stakes that on Convex BaseRewardPool

#### withdraw

```solidity
function withdraw(uint256 _pid, uint256 _amount) public returns (bool)
```

Withdraw a given amount from a pool (must already been unstaked from the Convex Reward Pool - BaseRewardPool uses withdrawAndUnwrap to get around this)

#### withdrawAll

```solidity
function withdrawAll(uint256 _pid) public returns (bool)
```

Withdraw all the senders LP tokens from a given gauge

#### withdrawTo

```solidity
function withdrawTo(uint256 _pid, uint256 _amount, address _to) external returns (bool)
```

Allows the actual BaseRewardPool to withdraw and send directly to the user

#### claimRewards

```solidity
function claimRewards(uint256 _pid, address _gauge) external returns (bool)
```

Allows a stash to claim secondary rewards from a gauge

#### setGaugeRedirect

```solidity
function setGaugeRedirect(uint256 _pid) external returns (bool)
```

Tells the Curve gauge to redirect any accrued rewards to the given stash via the VoterProxy

#### earmarkRewards

```solidity
function earmarkRewards(uint256 _pid, address _zroPaymentAddress) external payable returns (bool)
```

Basically a hugely pivotal function. Responsible for collecting the crv from gauge, and then redistributing to the correct place. Pays the caller a fee to process this.

#### rewardClaimed

```solidity
function rewardClaimed(uint256 _pid, address _address, uint256 _amount) external returns (bool)
```

Callback from reward contract when crv is received.

_Goes off and mints a relative amount of `CVX` based on the distribution schedule._

### PoolManagerLite

Pool Manager Lite

_Add pools to the Booster contract_

#### booster

```solidity
address booster
```

#### operator

```solidity
address operator
```

#### protectAddPool

```solidity
bool protectAddPool
```

#### isShutdown

```solidity
bool isShutdown
```

#### constructor

```solidity
constructor(address _booster) public
```

#### setOperator

```solidity
function setOperator(address _operator) external
```

#### setProtectPool

```solidity
function setProtectPool(bool _protectAddPool) external
```

#### addPool

```solidity
function addPool(address _gauge) external returns (bool)
```

#### addPool

```solidity
function addPool(address _gauge, uint256 _stashVersion) external returns (bool)
```

#### shutdownPool

```solidity
function shutdownPool(uint256 _pid) external returns (bool)
```

#### shutdownSystem

```solidity
function shutdownSystem() external
```

### VoterProxyLite

_A lite version of the original VoterProxy for use on sidechains_

#### mintr

```solidity
address mintr
```

#### crv

```solidity
address crv
```

#### rewardDeposit

```solidity
address rewardDeposit
```

#### withdrawer

```solidity
address withdrawer
```

#### owner

```solidity
address owner
```

#### operator

```solidity
address operator
```

#### constructor

```solidity
constructor() public
```

#### initialize

```solidity
function initialize(address _mintr, address _crv, address _owner) external
```

Initialize the contract.

**Parameters**

| Name    | Type    | Description       |
| ------- | ------- | ----------------- |
| \_mintr | address | CRV minter        |
| \_crv   | address | CRV Token address |
| \_owner | address | The owner         |

#### getName

```solidity
function getName() external pure returns (string)
```

#### setOwner

```solidity
function setOwner(address _owner) external
```

#### setRewardDeposit

```solidity
function setRewardDeposit(address _withdrawer, address _rewardDeposit) external
```

Allows dao to set the reward withdrawal address

**Parameters**

| Name            | Type    | Description            |
| --------------- | ------- | ---------------------- |
| \_withdrawer    | address | Whitelisted withdrawer |
| \_rewardDeposit | address | Distributor address    |

#### setSystemConfig

```solidity
function setSystemConfig(address _mintr) external returns (bool)
```

Allows dao to set the external system config, should it change in the future

**Parameters**

| Name    | Type    | Description                               |
| ------- | ------- | ----------------------------------------- |
| \_mintr | address | Token minter address for claiming rewards |

#### setOperator

```solidity
function setOperator(address _operator) external
```

Set the operator of the VoterProxy

**Parameters**

| Name       | Type    | Description                       |
| ---------- | ------- | --------------------------------- |
| \_operator | address | Address of the operator (Booster) |

#### setStashAccess

```solidity
function setStashAccess(address _stash, bool _status) external returns (bool)
```

#### deposit

```solidity
function deposit(address _token, address _gauge) external returns (bool)
```

Deposit tokens into the Curve Gauge

_Only can be called by the operator (Booster) once this contract has been whitelisted by the Curve DAO_

**Parameters**

| Name    | Type    | Description                  |
| ------- | ------- | ---------------------------- |
| \_token | address | Deposit LP token address     |
| \_gauge | address | Gauge contract to deposit to |

#### withdraw

```solidity
function withdraw(contract IERC20 _asset) external returns (uint256 balance)
```

Withdraw ERC20 tokens that have been distributed as extra rewards

_Tokens shouldn't end up here if they can help it. However, dao can set a withdrawer that can process these to some ExtraRewardDistribution._

#### withdraw

```solidity
function withdraw(address _token, address _gauge, uint256 _amount) public returns (bool)
```

Withdraw LP tokens from a gauge

_Only callable by the operator_

**Parameters**

| Name     | Type    | Description                    |
| -------- | ------- | ------------------------------ |
| \_token  | address | LP token address               |
| \_gauge  | address | Gauge for this LP token        |
| \_amount | uint256 | Amount of LP token to withdraw |

#### withdrawAll

```solidity
function withdrawAll(address _token, address _gauge) external returns (bool)
```

Withdraw all LP tokens from a gauge

_Only callable by the operator_

**Parameters**

| Name    | Type    | Description             |
| ------- | ------- | ----------------------- |
| \_token | address | LP token address        |
| \_gauge | address | Gauge for this LP token |

#### claimCrv

```solidity
function claimCrv(address _gauge) external returns (uint256)
```

Claim CRV from Curve

_Claim CRV for LP token staking from the CRV minter contract_

#### claimRewards

```solidity
function claimRewards(address _gauge) external returns (bool)
```

Claim extra rewards from gauge

_Called by operator (Booster) to claim extra rewards_

#### balanceOfPool

```solidity
function balanceOfPool(address _gauge) public view returns (uint256)
```

#### execute

```solidity
function execute(address _to, uint256 _value, bytes _data) external returns (bool, bytes)
```

### L1PoolManagerProxy

_Allows to permissionless add pools on any supported sidechain. 1. Owner must configure gaugeTypes mapping (lzChainId => balancer gauge type) 2. User most provide a root gauge address and the layer zero chain id, with enought native fee to be able to add a pool on the destination chain._

#### NO\_EXTRA\_GAS

```solidity
uint256 NO_EXTRA_GAS
```

#### PT\_SEND

```solidity
uint16 PT_SEND
```

#### lzChainId

```solidity
uint16 lzChainId
```

_LayerZero chain ID for this chain_

#### gaugeController

```solidity
address gaugeController
```

_Gauge controller address_

#### gaugeCheckpointer

```solidity
address gaugeCheckpointer
```

_Gauge controller address_

#### gaugeTypes

```solidity
mapping(uint16 => string) gaugeTypes
```

_lzChainId => gauge type_

#### AddSidechainPool

```solidity
event AddSidechainPool(uint16 dstChainId, address rootGauge, address dstGauge)
```

#### constructor

```solidity
constructor(uint16 _lzChainId, address _lzEndpoint, address _gaugeController, address _gaugeCheckpointer) public
```

**Parameters**

| Name                | Type    | Description                |
| ------------------- | ------- | -------------------------- |
| \_lzChainId         | uint16  | LayerZero chain ID         |
| \_lzEndpoint        | address | LayerZero endpoint         |
| \_gaugeController   | address | Gauge controller address   |
| \_gaugeCheckpointer | address | Guage checkpointer address |

#### receive

```solidity
receive() external payable
```

#### setGaugeType

```solidity
function setGaugeType(uint16 _lzChainId, string gaugeType) external
```

Maps layer zero chain id with balancer gauge type.

**Parameters**

| Name        | Type   | Description          |
| ----------- | ------ | -------------------- |
| \_lzChainId | uint16 | Layer zero chain id. |
| gaugeType   | string | Balancer gaugeType.  |

#### addPools

```solidity
function addPools(address[] _gauges, uint16 _dstChainId, address _zroPaymentAddress, bytes _adapterParams) external payable returns (bool)
```

Send a message to add a pool on a sidechain.

_Set adapterParams correctly per dstChainId to provide enough gas to add a pool on the destination chain._

**Parameters**

| Name                | Type       | Description                                                                                           |
| ------------------- | ---------- | ----------------------------------------------------------------------------------------------------- |
| \_gauges            | address\[] | The root gauge addresses.                                                                             |
| \_dstChainId        | uint16     | The LayerZero destination chain ID eg optimism is 111                                                 |
| \_zroPaymentAddress | address    | The LayerZero ZRO payment address                                                                     |
| \_adapterParams     | bytes      | The adapter params, very important as default gas limit is not enough to add a pool on any sidechain. |



### L2PoolManagerProxy

_Given a root gauge on L1PoolManagerProxy it adds a gauge recipient on PoolManagerLite_

#### poolManager

```solidity
address poolManager
```

_The poolManager address_

#### isValidGauge

```solidity
mapping(address => bool) isValidGauge
```

_Mapping of valid gauges sent from L1_

#### PoolManagerUpdated

```solidity
event PoolManagerUpdated(address poolManager)
```

#### constructor

```solidity
constructor() public
```

#### initialize

```solidity
function initialize(address _lzEndpoint, address _poolManager) external
```

Initialize the contract.

**Parameters**

| Name          | Type    | Description                 |
| ------------- | ------- | --------------------------- |
| \_lzEndpoint  | address | LayerZero endpoint contract |
| \_poolManager | address | Pool Manager address        |

#### isShutdown

```solidity
function isShutdown() external view returns (bool)
```

#### setPoolManager

```solidity
function setPoolManager(address _poolManager) external
```

#### setPoolManagerOperator

```solidity
function setPoolManagerOperator(address _operator) external
```

sets the poolManager operator.

_Usefull to reset pool manager operator value._

#### ownerAddPool

```solidity
function ownerAddPool(address _gauge) external returns (bool)
```

Adds new pool directly on L2.

**Parameters**

| Name    | Type    | Description        |
| ------- | ------- | ------------------ |
| \_gauge | address | The gauge address. |

#### addPool

```solidity
function addPool(address _gauge) external returns (bool)
```

Adds new pool directly on L2 checking for a valid gauge

**Parameters**

| Name    | Type    | Description        |
| ------- | ------- | ------------------ |
| \_gauge | address | The gauge address. |

#### shutdownPool

```solidity
function shutdownPool(uint256 _pid) external returns (bool)
```

Shutdowns a given pool.

**Parameters**

| Name  | Type    | Description  |
| ----- | ------- | ------------ |
| \_pid | uint256 | The pool id. |

#### shutdownSystem

```solidity
function shutdownSystem() external
```

Shutdows the system, it is not reversible.
