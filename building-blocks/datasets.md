---
description: All community datasets currently available in Spice
---

# Community Data

The Spice.ai platform includes a comprehensive set of read-to-query community, sample, and blockchain datasets.

### TPC-H

Explore the [TPC-H Benchmark dataset](../reference/sql-query-tables/tpc-h.md). The dataset is widely used to evaluate the analytic query capabilities of databases.

<table data-header-hidden><thead><tr><th width="230.5"></th><th width="235"></th></tr></thead><tbody><tr><td>Customer</td><td><code>tpch.customer</code></td></tr><tr><td>Lineitem</td><td><code>tpch.lineitem</code></td></tr><tr><td>Part</td><td><code>tpch.part</code></td></tr><tr><td>Partsupp</td><td><code>tpch.partsupp</code></td></tr><tr><td>Orders</td><td><code>tpch.orders</code></td></tr><tr><td>Nation</td><td><code>tpch.nation</code></td></tr><tr><td>Region</td><td><code>tpch.region</code></td></tr><tr><td>Supplier</td><td><code>tpch.supplier</code></td></tr></tbody></table>

### Blockchain Datasets

Explore SQL-queryable datasets organized by chain and data type, each with representative tables. Click the section header links for more information and complete table listings.

#### [Ethereum datasets](../reference/sql-query-tables/ethereum/)

<table data-header-hidden><thead><tr><th width="230.5"></th><th width="235"></th><th></th></tr></thead><tbody><tr><td>Blocks</td><td><code>eth.blocks</code></td><td><code>eth.recent_blocks</code></td></tr><tr><td>Transactions</td><td><code>eth.transactions</code></td><td><code>eth.recent_transactions</code></td></tr><tr><td>Logs</td><td><code>eth.logs</code></td><td><code>eth.recent_logs</code></td></tr><tr><td>Contracts</td><td><code>eth.contracts</code></td><td></td></tr><tr><td>Traces</td><td><code>eth.traces</code></td><td><code>eth.recent_traces</code></td></tr><tr><td>Withdrawals</td><td><code>eth.withdrawals</code></td><td><code>eth.recent_withdrawals</code></td></tr></tbody></table>

[**Ethereum Token datasets**](../reference/sql-query-tables/ethereum/token-tables/)

<table data-header-hidden><thead><tr><th width="278.5"></th><th width="236"></th><th></th></tr></thead><tbody><tr><td>Contracts (erc20, erc721, erc1155)</td><td><code>eth.contracts</code></td><td></td></tr><tr><td>Tokens (erc20, erc721, erc1155)</td><td><code>eth.tokens</code></td><td></td></tr><tr><td>Token Transfers (erc20, erc721, erc1155)</td><td><code>eth.token_transfers</code></td><td><code>eth.recent_token_transfers</code></td></tr><tr><td>Token Mints (erc20, erc721, erc1155)</td><td><code>eth.token_mints</code></td><td><code>eth.recent_token_mints</code></td></tr></tbody></table>

[**Ethereum NFT datasets**](../reference/sql-query-tables/ethereum/nft-tables/)

<table data-header-hidden><thead><tr><th width="223.5"></th><th width="266"></th><th></th></tr></thead><tbody><tr><td>NFTs</td><td><code>eth.nfts</code></td><td></td></tr><tr><td>NFT Contracts</td><td><code>eth.nft_contracts</code></td><td></td></tr><tr><td>NFT Transfers (with optional hold time)</td><td><code>eth.nft_transfers</code></td><td><code>eth.recent_nft_transfers</code></td></tr><tr><td>NFT Owners</td><td><code>eth.nft_owners</code></td><td></td></tr><tr><td>NFT Airdrop Transfers</td><td><code>eth.nft_airdrop_transfers</code></td><td><code>eth.recent_nft_airdrop_transfers</code></td></tr></tbody></table>

[**Ethereum Wallet Balances datasets**](../reference/sql-query-tables/ethereum/wallet-balances/)

| A block-level view of all changes to any account's Ether balance | `eth.wallet_balances` | `eth.recent_wallet_balances` |
| ---------------------------------------------------------------- | --------------------- | ---------------------------- |

[**Ethereum Uniswap datasets**](../reference/sql-query-tables/ethereum/uniswap-v2-tables/)

<table data-header-hidden><thead><tr><th width="280.5"></th><th></th><th></th></tr></thead><tbody><tr><td>Uniswap V2 Pools</td><td><code>eth.uniswap_v2.pools</code></td><td></td></tr><tr><td>Uniswap V2 Pool Statistics (reserves and price info)</td><td><code>eth.uniswap_v2.pool_stats</code></td><td></td></tr><tr><td>Uniswap V2 Events (swaps, mints, burns, etc)</td><td><code>eth.uniswap_v2.event_swaps</code></td><td><code>eth.uniswap_v2.recent_event_swaps</code></td></tr><tr><td>Uniswap V3 Pools</td><td><code>eth.uniswap_v3.pools</code></td><td></td></tr><tr><td>Uniswap V3 Pool Statistics (reserves and price info)</td><td><code>eth.uniswap_v3.pool_stats</code></td><td></td></tr><tr><td>Uniswap V3 Events (swaps, mints, burns, etc)</td><td><code>eth.uniswap_v3.event_swaps</code></td><td><code>eth.uniswap_v3.recent_event_swaps</code></td></tr></tbody></table>

[**Ethereum Sushiswap datasets**](../reference/sql-query-tables/ethereum/sushiswap-tables/)

| Sushiswap Pools                                     | `eth.sushiswap.pools`       |                                    |
| --------------------------------------------------- | --------------------------- | ---------------------------------- |
| Sushiswap Pool Statistics (reserves and price info) | `eth.sushiswap.pool_stats`  |                                    |
| Sushiswap Events (swaps, mints, burns, etc)         | `eth.sushiswap.event_swaps` | `eth.sushiswap.recent_event_swaps` |

[**Ethereum Name Service (ENS) datasets**](../reference/sql-query-tables/ethereum/token-tables-1/)

<table data-header-hidden><thead><tr><th width="368.5"></th><th></th></tr></thead><tbody><tr><td>ENS Domains</td><td><code>ens.domains</code></td></tr></tbody></table>

[**Ethereum Beacon Chain datasets**](../reference/sql-query-tables/ethereum/beacon-chain-tables/)

<table><thead><tr><th width="218"></th><th></th><th></th></tr></thead><tbody><tr><td>Validators</td><td><code>eth.beacon.validators</code></td><td></td></tr><tr><td>Slots</td><td><code>eth.beacon.slots</code></td><td><code>eth.beacon.recent_slots</code></td></tr><tr><td>Attestations</td><td><code>eth.beacon.attestations</code></td><td><code>eth.beacon.recent_attestations</code></td></tr><tr><td>Deposits</td><td><code>eth.beacon.deposits</code></td><td><code>eth.beacon.recent_deposits</code></td></tr><tr><td>Voluntary Exits</td><td><code>eth.beacon.voluntary_exits</code></td><td><code>eth.beacon.recent_voluntary_exits</code></td></tr><tr><td>Attester Slashings</td><td><code>eth.beacon.attester_slashings</code></td><td><code>eth.beacon.recent_attester_slashings</code></td></tr><tr><td>Proposer Slashings</td><td><code>eth.beacon.proposer_slashings</code></td><td><code>eth.beacon.recent_proposer_slashings</code></td></tr><tr><td>BLS To Execution Changes</td><td><code>eth.beacon.bls_to_execution_changes</code></td><td><code>eth.beacon.recent_bls_to_execution_changes</code></td></tr><tr><td>Withdrawals</td><td><code>eth.beacon.withdrawals</code></td><td><code>eth.beacon.recent_withdrawals</code></td></tr></tbody></table>

[**Ethereum Eigenlayer datasets**](../reference/sql-query-tables/ethereum/eigenlayer-tables/)

<table><thead><tr><th width="218"></th><th></th><th></th></tr></thead><tbody><tr><td>All Eigenpods</td><td><code>eth.eigenlayer.eigenpods</code></td><td></td></tr><tr><td>All Eigenpod Validators</td><td><code>eth.eigenlayer.eigenpod_validators</code></td><td></td></tr><tr><td>Eigenpod ETH wallet changed balances</td><td><code>eth.eigenlayer.eigenpod_balances</code></td><td><code>eth.eigenlayer.recent_eigenpod_balances</code></td></tr><tr><td>Withdrawal Router ETH wallet balances</td><td><code>eth.eigenlayer.withdrawal_router</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for a strategy being approved for a deposit</td><td><code>eth.eigenlayer.strategy_manager_added_to_deposit_whitelist</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when a new deposit occurs on behalf of<code>depositor</code></td><td><code>eth.eigenlayer.strategy_manager_deposits</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for a strategy being removed for a deposit</td><td><code>eth.eigenlayer.strategy_manager_removed_from_deposit_whitelist</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when a new withdrawal occurs on behalf of<code>depositor</code></td><td><code>eth.eigenlayer.strategy_manager_share_withdrawal_queued</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when the<code>strategyWhitelister</code>is changed</td><td><code>eth.eigenlayer.strategy_manager_strategy_whitelister_changed</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when a queued withdrawal is completed</td><td><code>eth.eigenlayer.strategy_manager_withdrawal_completed</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when the<code>withdrawalDelayBlocks</code>variable is modified</td><td><code>eth.eigenlayer.strategy_manager_withdrawal_delay_blocks_set</code></td><td></td></tr><tr><td><code>StrategyManager</code>events for when a new withdrawal is queued by<code>depositor</code></td><td><code>eth.eigenlayer.strategy_manager_withdrawal_queued</code></td><td></td></tr><tr><td>Eigenpod withdrawal recipient balances</td><td><code>eth.eigenlayer.withdrawal_recipient_balances</code></td><td><code>eth.eigenlayer.recent_withdrawal_recipient_balances</code></td></tr><tr><td>Eigenpod withdrawal<code>DelayedWithdrawalRouter</code>balances</td><td><code>eth.eigenlayer.withdrawal_router_balances</code></td><td><code>eth.eigenlayer.recent_withdrawal_router_balances</code></td></tr><tr><td>Decoded<code>DelayedWithdrawalClaimed</code>events for the<code>DelayedWithdrawalRouter</code>contract</td><td><code>eth.eigenlayer.withdrawal_router_claimed</code></td><td><code>eth.eigenlayer.recent_withdrawal_router_claimed</code></td></tr><tr><td>Decoded<code>DelayedWithdrawalCreated</code>events from the<code>DelayedWithdrawalRouter</code>contract</td><td><code>eth.eigenlayer.withdrawal_router_created</code></td><td><code>eth.eigenlayer.recent_withdrawal_router_created</code></td></tr><tr><td>DelegationManager events for when the minimum withdrawal delay blocks are set</td><td><code>eth.eigenlayer.delegation_manager_min_withdrawal_delay_blocks_set</code></td><td></td></tr><tr><td>DelegationManager events for when the operator details are modified</td><td><code>eth.eigenlayer.delegation_manager_operator_details_modified</code></td><td></td></tr><tr><td>DelegationManager events for when the operator metadata URI is updated</td><td><code>eth.eigenlayer.delegation_manager_operator_metadata_uri_updated</code></td><td></td></tr><tr><td>DelegationManager events for when an operator is registered</td><td><code>eth.eigenlayer.delegation_manager_operator_registered</code></td><td></td></tr><tr><td>DelegationManager events for when an operator's shares are decreased</td><td><code>eth.eigenlayer.delegation_manager_operator_shares_decreased</code></td><td></td></tr><tr><td>DelegationManager events for when an operator's shares are increased</td><td><code>eth.eigenlayer.delegation_manager_operator_shares_increased</code></td><td></td></tr><tr><td>DelegationManager events for when a staker is delegated</td><td><code>eth.eigenlayer.delegation_manager_staker_delegated</code></td><td></td></tr><tr><td>DelegationManager events for when a staker is force undelegated</td><td><code>eth.eigenlayer.delegation_manager_staker_force_undelegated</code></td><td></td></tr><tr><td>DelegationManager events for when a staker is undelegated</td><td><code>eth.eigenlayer.delegation_manager_staker_undelegated</code></td><td></td></tr><tr><td>DelegationManager events for when the strategy withdrawal delay blocks are set</td><td><code>eth.eigenlayer.delegation_manager_strategy_withdrawal_delay_blocks_set</code></td><td></td></tr><tr><td>DelegationManager events for when a withdrawal is queued</td><td><code>eth.eigenlayer.delegation_manager_withdrawal_queued</code></td><td></td></tr><tr><td>DelegationManager events for when a withdrawal is completed</td><td><code>eth.eigenlayer.delegation_manager_withdrawal_completed</code></td><td></td></tr><tr><td>DelegationManager events for when a withdrawal is migrated</td><td><code>eth.eigenlayer.delegation_manager_withdrawal_migrated</code></td><td></td></tr></tbody></table>

#### [Ethereum Aave V2 datasets](../reference/sql-query-tables/ethereum/aave-v2-tables/)

|                          |                                  |                                         |
| ------------------------ | -------------------------------- | --------------------------------------- |
| Loans (historical)       | `eth.aave_v2.loan_updates`       | `eth.aave_v2.recent_loan_updates`       |
| Collateral (historical)  | `eth.aave_v2.collateral_updates` | `eth.aave_v2.recent_collateral_updates` |
| Loans (latest view)      | `eth.aave_v2.loans`              |                                         |
| Collateral (latest view) | `eth.aave_v2.collateral`         |                                         |

[**Bitcoin datasets**](../reference/sql-query-tables/bitcoin/)

<table data-header-hidden><thead><tr><th width="240.5"></th><th></th><th></th></tr></thead><tbody><tr><td>Blocks</td><td><code>btc.blocks</code></td><td><code>btc.recent_blocks</code></td></tr><tr><td>Transactions</td><td><code>btc.transactions</code></td><td><code>btc.recent_transactions</code></td></tr><tr><td>Transaction Inputs</td><td><code>btc.transaction_inputs</code></td><td><code>btc.recent_transaction_inputs</code></td></tr><tr><td>Transaction Outputs</td><td><code>btc.transaction_outputs</code></td><td><code>btc.recent_transaction_outputs</code></td></tr></tbody></table>
