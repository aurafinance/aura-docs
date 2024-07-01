# Aura Subgraphs

The Aura Subgraph indexes data on the Aura smart contracts with a GraphQL interface. It updates data in response to function calls and contract events to maintain data on the `Booster`, `Pools`, `AuraLocker` etc, to power front-end apps and integrations.

## Current Versions

<table>
  <thead>
    <tr>
      <th width="204">Network</th>
      <th width="440">Subgraph URL</th>
      <th>Playground</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ethereum Mainnet</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-mainnet/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-mainnet/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-mainnet/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Arbitrum</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-arbitrum/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-arbitrum/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-arbitrum/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Optimism</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-optimism/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-optimism/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-optimism/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Gnosis</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-gnosis/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-gnosis/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-gnosis/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Base</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-base/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-base/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-base/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Polygon</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-polygon/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-polygon/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-polygon/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Polygon zkEVM</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-zkevm/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-zkevm/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-zkevm/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Avalanche</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-avalanche/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-avalanche/api</a>
      </td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-avalanche/playground">Link</a>
      </td>
    </tr>
    <tr>
      <td>Fraxtal</td>
      <td>
        <a href="https://graph.data.aura.finance/subgraphs/name/aura-finance-fraxtal">https://graph.data.aura.finance/subgraphs/name/aura-finance-fraxtal</a>
      </td>
      <td>
        <a href="https://graph.data.aura.finance/subgraphs/name/aura-finance-fraxtal/graphql">Link</a>
      </td>
    </tr>
  </tbody>
</table>

## **Examples**

Get. the balance of an account at a given block

> Note: account address is case sensitve, search with lowercase

```graphql
query accountData($accountAddress: String!, $blockNumber: Int!) {
  accounts(where: { id: $accountAddress }, block: { number: $blockNumber }) {
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

## GraphQL Schema <a href="#graphql-schema" id="graphql-schema"></a>

The data included in this subgraph data layer is the data that is most applicable to the front-end. It aims at the very least to keep track of all the resources `Account` and keep track of basic pool data and AuraLocker

The schema of GraphQL elements is the same on every Network, it's available in the docs section of the [playground](https://subgraph.satsuma-prod.com/1xhub-ltd/aura-finance-mainnet/playground). Alternatively you can extract the whole schema with packages such as [`get-graphql-schema`](https://www.npmjs.com/package/get-graphql-schema) or by seding the following query:

```graphql
query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}
fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}
fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}
fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```
