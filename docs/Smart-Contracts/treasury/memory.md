# Memory 

The memory contract represents an abstraction around the use and storage of contract and asset addresses. The contract has two internal maps and provided two types of Raw query calls. 
These are methods implemented in the Memory struct. 
With this request-response model around addresses and assets we gain a small piece of assurance against human error such as mistyped addresses as well as gaining the ability to have many dapps requesting asset info from a common source. 
We are working with external public partners to enshure the registered addresses are correct.

## Config 

### BaseState

See [Dapp BaseState](../Dapp-Base/#basestate)

## InstantiateMsg

No values are needed to instantiate this contract. The sender of the instantiate msg will be marked as the Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct InstantiateMsg {}
```

## ExecuteMsg

```rust
pub enum ExecuteMsg {
    /// Updates the addressbook
    UpdateContractAddresses {
        to_add: Vec<(String, String)>,
        to_remove: Vec<String>,
    },
    UpdateAssetAddresses {
        to_add: Vec<(String, String)>,
        to_remove: Vec<String>,
    },
    /// Sets a new Admin
    SetAdmin { admin: String },
}
```

### `SetAdmin`

Update the configured admin for the memory contract. 

| Key | Type | Description |
| :--- | :--- | :--- |
| `admin` | String | Address of the new Admin. |

```json 
{
    "set_admin": {
        "admin": "terra1..."
    }
}
```

### `UpdateContractAddresses`

Attempt to add and/or remove 1 or more contract address from the memory contract. This call can be used to store contracts one by one or in one go to store many contracts on instantiation of a Dapp.


| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec<(String, String)> | Vector containing a pair mapping for each contract address to be added. |
| `to_remove` | Vec<(String)> | Vector containing memory keys/ids for each contract address to be removed from Memory. |

```json
{
    "update_contract_addresses": {
        "to_add": [("mykey", "terra1...")],
        "to_remove": ["key_to_remove"]
    }
}
```

### `UpdateContractAssets`

Attempt to add and/or remove 1 or more Asset types from the memory contract. This call can be used to store Assets which will be used by the Dapp such as storing a custom CW20 including its address ready for later use.


| Name | Type | Description |
| :--- | :--- | :--- |
| `to_add` | Vec<(String, String)> | Vector containing a pair mapping for each Asset to be added. |
| `to_remove` | Vec<(String)> | Vector containing memory keys/ids for each Asset to be removed from Memory. |

```json
{
    "update_contract_assets": {
        "to_add": [("asset_key", "asset_1")],
        "to_remove": ["key_to_remove"]
    }
}
```

## QueryMsg

```rust
pub enum QueryMsg {
    /// Queries assets based on name
    QueryAssets {
        names: Vec<String>,
    },
    QueryContracts {
        names: Vec<String>,
    },
}
```