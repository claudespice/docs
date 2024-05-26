---
description: Ethereum EigenLayer tables available to query via SQL
---

# EigenLayer Tables

Learn about [EigenLayer](https://www.eigenlayer.xyz/) and read the [whitepaper](https://2039955362-files.gitbook.io/\~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FPy2Kmkwju3mPSo9jrKKt%2Fuploads%2F2dCfPgItRfQbX25KriQv%2Fwhitepaper.pdf?alt=media\&token=d4d94480-3f01-4e63-bc92-a0658ea37aab).

**Ethereum EigenLayer tables available to query**

| Name                                                                                                                                                        | Description                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| [`eth.eigenlayer.eigenpods`](eth.eigenlayer.eigenpods.md)                                                                                                   | All EigenPods                                                                                                      |
| [`eth.eigenlayer.eigenpod_validators`](eth.eigenlayer.eigenpod\_validators.md)                                                                              | All EigenPod Validators                                                                                            |
| [`eth.eigenlayer.eigenpod_balances`](eth.eigenlayer.eigenpod\_balances.md)                                                                                  | EigenPod ETH wallet changed balances including the delta from last block                                           |
| [`eth.eigenlayer.strategy_manager_added_to_deposit_whitelist`](eth.eigenlayer.delegation\_manager\_operator\_details\_modified.md)                          | StrategyManager event for when a strategy is added to the approved list of strategies for deposit                  |
| [`eth.eigenlayer.strategy_manager_deposits`](eth.eigenlayer.strategy\_manager\_deposits.md)                                                                 | StrategyManager event for when a new deposit occurs on behalf of `depositor`                                       |
| [`eth.eigenlayer.strategy_manager_removed_from_deposit_whitelist`](eth.eigenlayer.strategy\_manager\_removed\_from\_deposit\_whitelist.md)                  | StrategyManager event for when a strategy is removed from the approved list of strategies for deposit              |
| [`eth.eigenlayer.strategy_manager_share_withdrawal_queued`](eth.eigenlayer.strategy\_manager\_share\_withdrawal\_queued.md)                                 | StrategyManager event for when a new withdrawal occurs on behalf of `depositor`                                    |
| [`eth.eigenlayer.strategy_manager_strategy_whitelister_changed`](eth.eigenlayer.strategy\_manager\_strategy\_whitelister\_changed.md)                       | StrategyManager event for when the `strategyWhitelister` is changed                                                |
| [`eth.eigenlayer.strategy_manager_withdrawal_completed`](eth.eigenlayer.strategy\_manager\_withdrawal\_completed.md)                                        | StrategyManager event for when a queued withdrawal is completed                                                    |
| [`eth.eigenlayer.strategy_manager_withdrawal_delay_blocks_set`](eth.eigenlayer.strategy\_manager\_withdrawal\_delay\_blocks\_set.md)                        | StrategyManager event for when the `withdrawalDelayBlocks` variable is modified from `previousValue` to `newValue` |
| [`eth.eigenlayer.strategy_manager_withdrawal_queued`](eth.eigenlayer.strategy\_manager\_withdrawal\_queued.md)                                              | StrategyManager event for when a new withdrawal is queued by `depositor`                                           |
| [`eth.eigenlayer.withdrawal_router_balances`](eth.eigenlayer.withdrawal\_router\_balances.md)                                                               | Current per-block, Ether balance for the DelayedWithdrawalRouter contract                                          |
| [`eth.eigenlayer.withdrawal_router_claimed`](eth.eigenlayer.withdrawal\_router\_claimed.md)                                                                 | Decoded `DelayedWithdrawalClaimed` events for the `DelayedWithdrawalRouter` contract                               |
| [`eth.eigenlayer.withdrawal_router_created`](eth.eigenlayer.withdrawal\_router\_created.md)                                                                 | Decoded `DelayedWithdrawalCreated` events from the `DelayedWithdrawalRouter` contract                              |
| [`eth.eigenlayer.delegation_manager_min_withdrawal_delay_blocks_set`](eth.eigenlayer.delegation\_manager\_strategy\_withdrawal\_delay\_blocks\_set.md)      | DelegationManager event for when the minimum withdrawal delay blocks are set                                       |
| [`eth.eigenlayer.delegation_manager_operator_details_modified`](eth.eigenlayer.delegation\_manager\_operator\_details\_modified.md)                         | DelegationManager event for when the operator details are modified                                                 |
| [`eth.eigenlayer.delegation_manager_operator_metadata_uri_updated`](eth.eigenlayer.delegation\_manager\_operator\_metadata\_uri\_updated.md)                | DelegationManager event for when the operator metadata URI is updated                                              |
| [`eth.eigenlayer.delegation_manager_operator_registered`](eth.eigenlayer.delegation\_manager\_operator\_registered.md)                                      | DelegationManager event for when an operator is registered                                                         |
| [`eth.eigenlayer.delegation_manager_operator_shares_decreased`](eth.eigenlayer.delegation\_manager\_operator\_shares\_decreased.md)                         | DelegationManager event for when an operator's shares are decreased                                                |
| [`eth.eigenlayer.delegation_manager_operator_shares_increased`](eth.eigenlayer.delegation\_manager\_operator\_shares\_increased.md)                         | DelegationManager event for when an operator's shares are increased                                                |
| [`eth.eigenlayer.delegation_manager_staker_delegated`](eth.eigenlayer.delegation\_manager\_staker\_delegated.md)                                            | DelegationManager event for when a staker is delegated                                                             |
| [`eth.eigenlayer.delegation_manager_staker_force_undelegated`](eth.eigenlayer.delegation\_manager\_staker\_force\_undelegated.md)                           | DelegationManager event for when a staker is force undelegated                                                     |
| [`eth.eigenlayer.delegation_manager_staker_undelegated`](eth.eigenlayer.delegation\_manager\_staker\_undelegated.md)                                        | DelegationManager event for when a staker is undelegated                                                           |
| [`eth.eigenlayer.delegation_manager_strategy_withdrawal_delay_blocks_set`](eth.eigenlayer.delegation\_manager\_strategy\_withdrawal\_delay\_blocks\_set.md) | DelegationManager event for when the strategy withdrawal delay blocks are set                                      |
| [`eth.eigenlayer.delegation_manager_withdrawal_queued`](eth.eigenlayer.delegation\_manager\_withdrawal\_queued.md)                                          | DelegationManager event for when a withdrawal is queued                                                            |
| [`eth.eigenlayer.delegation_manager_withdrawal_completed`](eth.eigenlayer.delegation\_manager\_withdrawal\_completed.md)                                    | DelegationManager event for when a withdrawal is completed                                                         |
| [`eth.eigenlayer.delegation_manager_withdrawal_migrated`](eth.eigenlayer.delegation\_manager\_withdrawal\_migrated.md)                                      | DelegationManager event for when a withdrawal is migrated                                                          |
| The table list is also available as a SQL query using `show tables`. For example:                                                                           |                                                                                                                    |

```sql
SHOW TABLES IN eth.eigenlayer
```

**Improving query performance - indexed columns**

Query performance can be significantly improved by adding `WHERE` clauses to your query on specific indexed columns. All Eigenlayer tables have an index on `block_number`.
