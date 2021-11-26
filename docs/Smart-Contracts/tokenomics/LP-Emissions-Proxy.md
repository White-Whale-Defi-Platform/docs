# LP Emissions Proxy Contract

This proxy contract handles staking the provided LP tokens to our own LP emissions contract while allowing for dual incetives from Astroport. A detailed explanation can be found [here](https://astroport.medium.com/project-guide-how-to-integrate-dual-incentives-with-astroport-generators-2aa13169c5c1)

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `generator_contract_addr` | Addr | Address of generator contract |
| `pair_addr` | Addr | Address of the WHALE/UST pair |
| `lp_token_addr` | Addr | Address of the WHALE/UST LP token |
| `reward_contract_addr` | Addr | Address of the LP emissions contract |
| `reward_token_addr` | Addr | Whale token address |


## Messages
## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub generator_contract_addr: String,
    pub pair_addr: String,
    pub lp_token_addr: String,
    pub reward_contract_addr: String,
    pub reward_token_addr: String,
}
```


| Key | Type | Description |
| :--- | :--- | :--- |
| `generator_contract_addr` | String | Address of generator contract |
| `pair_addr` | String | Address of the WHALE/UST pair |
| `lp_token_addr` | String | Address of the WHALE/UST LP token |
| `reward_contract_addr` | String | Address of the LP emissions contract |
| `reward_token_addr` | String | Whale token address |


## ExecuteMsg

### Receive

Receives LP tokens sent by Generator contract. Further sends them to the $WHALE LP Staking contract

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
        Recieve(Cw20ReceiveMsg),
}
```

### UpdateRewards

Claims pending rewards from the $WHALE LP staking contract

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
        UpdateRewards {},
}
```

### SendRewards

Transfers $WHALE rewards

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SendRewards { 
        account: Addr, 
        amount: Uint128 
    },
}

```

| Key | Type | Description |
| :--- | :--- | :--- |
| `account` | Addr | Address of the LP staker |
| `amount` | Uint128 | Amount of reward tokens to transfer |


### Withdraw

Withdraws LP Tokens from the staking contract. Rewards are NOT claimed when withdrawing LP tokens

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Withdraw { 
        account: Addr, 
        amount: Uint128 
    },
}

```


| Key | Type | Description |
| :--- | :--- | :--- |
| `account` | Addr | Address of the LP staker |
| `amount` | Uint128 | Amount of reward tokens to transfer |

### EmergencyWithdraw

Withdraws LP Tokens from the staking contract. Rewards are NOT claimed when withdrawing LP tokens. Uses same withdraw function internally.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EmergencyWithdraw { 
        account: Addr, 
        amount: Uint128 
    },
}
```



| Key | Type | Description |
| :--- | :--- | :--- |
| `account` | Addr | Address of the LP staker |
| `amount` | Uint128 | Amount of reward tokens to transfer |

## QueryMsg

### `Deposit`

Gets the bonded amount of LP tokens

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Deposit {} 
}
```

### `Reward`

Returns the amount of WHALE held by this contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Reward {} 
}
```

### `PendingToken`

Returns the claimable amount of WHALE.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    PendingToken {} 
}
```

### `RewardInfo`

Return the WHALE token address.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    RewardInfo {} 
}
```
