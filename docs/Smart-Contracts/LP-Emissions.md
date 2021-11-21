# LP Emissions Contract

This contract handles liquidity mining incetives. It distributes WHALE tokens to WHALE/UST LP token stakers who can claim then claim them.  

## Config

| Key | Type | Description |
| :--- | :--- | :--- |
| `owner` | Addr | Address of the owner of the contract |
| `whale_token` | Addr | Address of the WHALE token |
| `staking_token` | Addr | Address of the WHALE/UST staking token |
| `staking_token_decimals` | u8 |  |
| `distribution_schedule` | (u64,u64,Uint128) | The distribution schedule parameters |


## State

| Key | Type | Description |
| :--- | :--- | :--- |
| `last_distributed` | u64 | Timestamp at which the global_reward_index was last updated |
| `total_bond_amount` | Uint128 | Total number of WHALE-UST LP tokens staked with the contract |
| `global_reward_index` | Decimal | Used to calculate WHALE rewards accured over time elapsed. Ratio =  Total distributed WHALE tokens / total bond amount |
| `leftover` | Uint128 |  Number of WHALE tokens that are yet to be distributed |
| `reward_rate_per_token` | Decimal | Number of WHALE tokens distributed per staked LP token |

## Messages

See [lp_emissions messages](https://github.com/White-Whale-Defi-Platform/contracts/blob/audit/v1/packages/white_whale/src/tokenomics/lp_emissions.rs).