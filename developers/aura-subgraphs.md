# Aura Subgraphs

The Aura Subgraph indexes data on the Aura smart contracts with a GraphQL interface. It updates data in response to function calls and contract events to maintain data on the `Booster`, `Pools`, `AuraLocker` etc, to power front-end apps and integrations.

## Current Versions

<table>
  <thead>
    <tr>
      <th width="204">Network</th>
      <th width="440">Subgraph URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ethereum Mainnet</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-mainnet/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-mainnet/v0.0.1/</a>
      </td>
    </tr>
    <tr>
      <td>Arbitrum</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-arbitrum/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-arbitrum/v0.0.1/</a>
      </td>
    </tr>
    <tr>
      <td>Optimism</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-optimism/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-optimism/v0.0.1/</a>
      </td>
    </tr>
    <tr>
      <td>Gnosis</td>
      <td>
        <a href="https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-gnosis/api">https://subgraph.satsuma-prod.com/cae76ab408ca/1xhub-ltd/aura-finance-gnosis/api</a>
      </td>
    </tr>
    <tr>
      <td>Base</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-base/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-base/v0.0.1/</a>
      </td>
    </tr>
    <tr>
      <td>Polygon</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-polygon/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-polygon/v0.0.1/</a>
      </td>
    </tr>
    <tr>
      <td>Avalanche</td>
      <td>
        <a href="https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-avalanche/v0.0.1/">https://api.subgraph.ormilabs.com/api/public/396b336b-4ed7-469f-a8f4-468e1e26e9a8/subgraphs/aura-finance-avalanche/v0.0.1/</a>
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

The schema of GraphQL elements is the same on every Network. You can extract the whole schema with packages such as [`get-graphql-schema`](https://www.npmjs.com/package/get-graphql-schema) or by seding the following query:

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
