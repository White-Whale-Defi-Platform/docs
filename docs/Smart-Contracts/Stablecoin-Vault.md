# Stablecoin Vault

The Stablecoin Vault Contract is one of the flagship contracts in the White Whale system. It combined with a bot system allows for the automated trading of UST in response to a de-pegging event or to just perform a simple arbitrage operation. The Stablecoin Vault allows users to deposit just UST to then be exposed to arbitrage operations.

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `trader` | CanonicalAddr | Address of the assigned trader for this vault |
| `pool_address` | CanonicalAddr | Contract address of TerraSwap pool that will be interacted with for trades|
| `anchor_money_market_address` | CanonicalAddr | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | CanonicalAddr | Contract address of aUST token |
| `seignorage_address` | CanonicalAddr | Contract address of Seignorage system. This is used for burning/minting as a part of L1 arbitrage |
| `profit_check_address` | CanonicalAddr | Contract address of Profit Check Contract. |
| `anchor_min_withdraw_amount` | Uint128 | Minimum UST withdraw required to trigger a withdrawal from Anchor Money Market |

## InstantiateMsg

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub pool_address: String,
    pub anchor_money_market_address: String,
    pub aust_address: String,
    pub seignorage_address: String,
    pub profit_check_address: String,
    pub community_fund_addr: String,
    pub warchest_addr: String,
    pub asset_info: AssetInfo,
    pub slippage: Decimal,
    pub token_code_id: u64,
    pub warchest_fee: Decimal,
    pub community_fund_fee: Decimal,
    pub max_community_fund_fee: Uint128,
    pub anchor_min_withdraw_amount: Uint128
}
```

```javascript
{
    "pool_address": '<UST Address>',
    "asset_info": {
        "native_token": { "denom": "uusd" }
    },
    "aust_address": '<aUST Address>',
    "anchor_money_market_address": '<Anchor MM Address>',
    "seignorage_address": '<Seignorage Address>',
    "profit_check_address": '<Profit Check Address>',
    "slippage": "0.01",
    "token_code_id": 148,
    "community_fund_addr": "<Community Fund Address>",
    "warchest_addr": "<Warchest Fund Address>",
    "warchest_fee": "0.001",
    "community_fund_fee": "0.005",
    "max_community_fund_fee": "1000000",
    "denom": "uusd",
    "anchor_min_withdraw_amount": "100000000"
}
```

| Key | Type | Description |
| :--- | :--- | :--- |
| `pool_address` | CanonicalAddr | Contract address of TerraSwap pool that will be interacted with for trades  d|
| `anchor_money_market_address` | CanonicalAddr | Contract address of Anchor Money Market system. This is used for depositing and withdrawing from Anchor |
| `aust_address` | CanonicalAddr | Contract address of aUST token |
| `seignorage_address` | CanonicalAddr | Contract address of Seignorage system. This is used for burning/minting as a part of L1 arbitrage |
| `profit_check_address` | CanonicalAddr | Contract address of Profit Check Contract |
| `warchest_address` | CanonicalAddr | Contract address of Profit Check Contract |
| `asset_info` | AssetInfo | Struct detailing the token to be used for trading |
| `slippage` | Decimal | Slippage rate regarded as acceptable. |
| `token_code_id` | u64 | The Stored Code Object ID for the LP token creator. This is used on instantiation to creation an LP token when the Vault is created |
| `warchest_fee` | Decimal | Configurable fee rate for the warchest contract. |
| `community_fund_fee` | Decimal | Configurable fee rate for the community fund |
| `max_community_fund_fee` | Uint128 | Absolute max rate for community fund |
| `anchor_min_withdraw_amount` | Uint128 | Minimum UST withdraw required to trigger a withdrawal from Anchor Money Market |
