# Example Cross-Chain Queries

### View Change in WBTC Reserves

Gets the change in WBTC reserves by comparing the WBTC mints on the Ethereum chain versus the inflows and outflows of BTC to the WBTC custodian addresses on the Bitcoin chain.

**Typical query time**: < 30 seconds

```sql
WITH wbtc_minted AS (
    SELECT SUM(CAST("value" AS NUMERIC)) / 1e8 as wbtc_minted, date_trunc('day', TO_TIMESTAMP(block_timestamp)) as block_timestamp
    FROM eth.token_transfers_erc20 
    WHERE token_address = '0x2260fac5e5542a773aa44fbcfedf7c193bc2c599' AND from_address = '0x0000000000000000000000000000000000000000'
        AND block_timestamp BETWEEN UNIX_TIMESTAMP('2022-01-01 00:00:00') AND UNIX_TIMESTAMP('2022-06-30 23:59:59')
    GROUP BY block_timestamp
), wbtc_custodian_addresses_received AS (
    SELECT SUM(CAST("value" AS NUMERIC)) / 1e8 as custodian_btc_received, date_trunc('day', TO_TIMESTAMP(block_timestamp)) as block_timestamp
    FROM btc.transaction_outputs
    WHERE block_timestamp BETWEEN UNIX_TIMESTAMP('2022-01-01 00:00:00') AND UNIX_TIMESTAMP('2022-06-30 23:59:59') 
        AND addresses[0] IN (SELECT * FROM btc.wbtc_custodian_addresses)
    GROUP BY block_timestamp
), wbtc_custodian_addresses_sent AS (
    SELECT SUM(CAST("value" AS NUMERIC)) / 1e8 as custodian_btc_sent, date_trunc('day', TO_TIMESTAMP(block_timestamp)) as block_timestamp
    FROM btc.transaction_inputs
    WHERE block_timestamp BETWEEN UNIX_TIMESTAMP('2022-01-01 00:00:00') AND UNIX_TIMESTAMP('2022-06-30 23:59:59') 
        AND addresses[0] IN (SELECT * FROM btc.wbtc_custodian_addresses)
    GROUP BY block_timestamp
), joined_tables AS (
    SELECT COALESCE(custodian_btc_received, 0) as custodian_btc_received, 
           COALESCE(custodian_btc_sent, 0) as custodian_btc_sent, 
           COALESCE(wbtc_minted, 0) as wbtc_minted, 
           COALESCE(wm.block_timestamp, wcr.block_timestamp, wcs.block_timestamp) as block_timestamp
    FROM wbtc_minted wm FULL OUTER JOIN wbtc_custodian_addresses_received wcr ON wm.block_timestamp = wcr.block_timestamp
                        FULL OUTER JOIN wbtc_custodian_addresses_sent wcs ON COALESCE(wm.block_timestamp, wcr.block_timestamp) = wcs.block_timestamp
)
SELECT custodian_btc_received, custodian_btc_sent, wbtc_minted, custodian_btc_received - custodian_btc_sent - wbtc_minted AS wbtc_reserve_change, block_timestamp
FROM joined_tables
ORDER BY block_timestamp ASC
```

View this query and some visualizations in our [example Kaggle notebook](https://www.kaggle.com/phillipleblanc/spice-xyz-cross-chain-wbtc).
