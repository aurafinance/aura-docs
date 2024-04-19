# Aura Subgraphs

The Aura Subgraph indexes data on the Aura smart contracts with a GraphQL interface. It updates data in response to function calls and contract events to maintain data on the `Booster`, `Pools`, `AuraLocker` etc, to power front-end apps and integrations.



## Curren Versions

<table><thead><tr><th width="204">Network</th><th width="440">Subgraph URL</th><th>Example</th></tr></thead><tbody><tr><td>Ethereum Mainnet</td><td><a href="https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v2-1">https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v2-1</a></td><td><a href="https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v2-1/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D&#x26;variables=%22%7B%5Cn++%5C%22accountAddress%5C%22%3A+%5C%22ADD_ADDRESS_HERE%5C%22%2C%5Cn++%5C%22blockNumber%5C%22%3A+19667581%5Cn%7D%22">Link</a></td></tr><tr><td>Arbitrum</td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-arbitrum">https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-arbitrum</a></td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-arbitrum/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Optimism</td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-optimism">https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-optimism</a></td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-optimism/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Gnosis</td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-gnosis-chain">https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-gnosis-chain</a></td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-gnosis-chain/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Base</td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-base">https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-base</a></td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-base/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Polygon Pos</td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-polygon">https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-polygon</a></td><td><a href="https://api.thegraph.com/subgraphs/name/aurafinance/aura-finance-polygon/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Polygon Zkevm</td><td><a href="https://api.studio.thegraph.com/query/69982/aura-finance-zkevm/version/latest">https://api.studio.thegraph.com/query/69982/aura-finance-zkevm/version/latest</a></td><td><a href="https://api.studio.thegraph.com/query/69982/aura-finance-zkevm/version/latest/graphql?query=query+accountData+%7B%0A++accounts+%28first%3A+5%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++++pool+%7B%0A++++++++factoryPoolData+%7B%0A++++++++++balancerPoolId%0A++++++++%7D%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D">Link</a></td></tr><tr><td>Avalanche</td><td><a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-avalanche/version/v0.0.1/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-avalanche/version/v0.0.1/api</a></td><td></td></tr><tr><td></td><td></td><td></td></tr></tbody></table>



## Deprecated Versions <a href="#graphql-schema" id="graphql-schema"></a>

<table><thead><tr><th width="204">Network</th><th>Subgraph URL</th></tr></thead><tbody><tr><td>Ethereum Mainnet</td><td><a href="https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v1">https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v1</a></td></tr><tr><td></td><td></td></tr></tbody></table>

## GraphQL Schema <a href="#graphql-schema" id="graphql-schema"></a>

The schema of GraphQL elements available is defined in [schema](https://gist.github.com/0xahtle7/bb161fb101225c3f97f03bbd05c9d011). &#x20;

The data included in this subgraph data layer is the data that is most applicable to the front-end. It aims at the very least to keep track of all the resources `Account`  and keep track of basic pool data and AuraLocker

## **Examples**

Get. the balance of an account at a given block

Note: account address is case sensitve, search with lowercase  see [example](https://graph.data.aura.finance/subgraphs/name/aura/aura-mainnet-v2-1/graphql?query=query+accountData%28%24accountAddress%3A+String%21%29+%7B%0A++accounts%28where%3A+%7Bid%3A+%24accountAddress%7D%29+%7B%0A++++id%0A++++poolAccounts+%7B%0A++++++id%0A++++++staked%0A++++++rewards+%7B%0A++++++++rewards%0A++++++%7D%0A++++%7D%0A++++auraLockerAccount+%7B%0A++++++balance%0A++++++balanceLocked%0A++++++balanceNextUnlockIndex%0A++++++delegateUpdatedAt%0A++++++id%0A++++++userLocksLength%0A++++++account+%7B%0A++++++++id%0A++++++%7D%0A++++%7D%0A++%7D%0A%7D\&variables=%22%7B%5Cn++%5C%22accountAddress%5C%22%3A+%5C%22ADD\_ADDRESS\_HERE%5C%22%2C%5Cn++%5C%22blockNumber%5C%22%3A+19667581%5Cn%7D%22)&#x20;

```graphql
query accountData($accountAddress: String!, $blockNumber: Int!) {
  accounts(where: {id: $accountAddress}, block: {number: $blockNumber}) {
    id
    auraLockerAccount {
      balanceLocked
      balanceNextUnlockIndex
      delegateUpdatedAt
      id
      userLocksLength
      account {
        id
        poolAccounts(where: {}) {
          id
          pool {
            id
            totalStaked
            totalSupply
          }
        }
      }
    }
  }
}
```

