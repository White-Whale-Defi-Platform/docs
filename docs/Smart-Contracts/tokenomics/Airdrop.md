# Airdrop

The Airdrop contract is for WHALE tokens airdrop claim during the intital protocol launch. This airdrop contract is based on the [cw20-merkle-airdrop](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw20-merkle-airdrop/helpers) concept.

Tools for working with cw20 Merkle Airdrops can be found [here](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw20-merkle-airdrop/helpers)

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | Option<String> | Address of owner/admin of the contract |
| `whale_token_address` | String | Address of the WHALE token |
| `merkle_roots` | Option<Vec<String>> | Vector of merkle root proofs airdrop allocations |
| `from_timestamp` | Option<u64> | When the airdrop will begin |
| `to_timestamp` | u64 | When the airdrop will finish |
| `total_airdrop_size` | Uint128 | Total amount of the airdrop |


## Messages
## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub whale_token_address: String,
    pub merkle_roots: Option<Vec<String>>,
    pub from_timestamp: Option<u64>,
    pub to_timestamp: u64,
    pub total_airdrop_size: Uint128,
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | Option<String> | Address of owner/admin of the contract |
| `whale_token_address` | String | Address of the WHALE token |
| `merkle_roots` | Option<Vec<String>> | Vector of merkle root proofs airdrop allocations |
| `from_timestamp` | Option<u64> | When the airdrop will begin |
| `to_timestamp` | u64 | When the airdrop will finish |
| `total_airdrop_size` | Uint128 | Total amount of the airdrop |


## ExecuteMsg

### UpdateConfig

Updates the stored configuration values for the contract. Callable only by an Admin.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<String>,
        merkle_roots: Option<Vec<String>>,
        from_timestamp: Option<u64>,
        to_timestamp: Option<u64>,
    },
}
```

### Claim

Allows a given terra user to claim their WHALE airdrop.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
       Claim {
        claim_amount: Uint128,
        merkle_proof: Vec<String>,
        root_index: u32,
    },
}
```

### TransferUnclaimedTokens

Transfers remaining unclaimed $WHALE tokens. Callable only by an Admin.


```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    TransferUnclaimedTokens { recipient: String, amount: Uint128 },
}

```

| Key | Type | Description |
| :--- | :--- | :--- |
| `recipient` | String | Reciver of the unclaimed tokens |
| `amount` | Uint128 | Amount of airdrop tokens to transfer |


## QueryMsg

### `Config`

Gets the stored configuration information for the contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {} 
}
```

### `State`

Gets the stored state values for the contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    State {} 
}
```

### `UserInfo`

Returns any stored UserInfo related to claiming if found in the merkle roots.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserInfo { address:String } 
}
```

### `HasUserClaimed`

Return the WHALE token address.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    HasUserClaimed { address: String } 
}
```
