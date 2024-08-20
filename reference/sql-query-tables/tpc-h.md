---
description: The TPC-H Benchmark sample data
---

# TPC-H

As described in the [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/) specification:

> “TPC-H is a decision support benchmark. It consists of a suite of business-oriented ad hoc queries and concurrent data modifications. The queries and the data populating the database have been chosen to have broad industry-wide relevance. This benchmark illustrates decision support systems that examine large volumes of data, execute queries with a high degree of complexity, and give answers to critical business questions.”

TPC-H comes with various data set sizes to test different scaling factors. Spice.ai TPC-H dataset consists of the base scale factor (several million elements) and is available under `tpch` schema . The columns and their schema available for each table can be viewed with the `describe` command:

```sql
DESCRIBE tpch.customer;
DESCRIBE tpch.lineitem;
DESCRIBE tpch.part;
DESCRIBE tpch.partsupp;
DESCRIBE tpch.orders;
DESCRIBE tpch.nation;
DESCRIBE tpch.region
DESCRIBE tpch.supplier;
```

### Database Entities, Relationships, and Characteristics

The components of TPC-H consist of eight separate and individual tables (the Base Tables). The relationships between columns in these tables are illustrated in the following ER diagram:

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>The TPC-H Schema. Source: <a href="http://www.tpc.org/tpc_documents_current_versions/pdf/tpc-h_v2.17.1.pdf">TPC Benchmark H Standard Specification</a></p></figcaption></figure>

### Example TPC-H query

TPC-H comes with pre-defined queries to ask different business questions.  Query below reports the amount of business that was billed, shipped, and returned. More information about TPC-H and all the queries involved can be found in the official [TPC Benchmark H Standard Specification](https://www.tpc.org/tpc\_documents\_current\_versions/pdf/tpc-h\_v2.17.1.pdf).

```sql
select
	l_returnflag,
	l_linestatus,
	sum(l_quantity) as sum_qty,
	sum(l_extendedprice) as sum_base_price,
	sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
	sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
	avg(l_quantity) as avg_qty,
	avg(l_extendedprice) as avg_price,
	avg(l_discount) as avg_disc,
	count(*) as count_order
from
	tpch.lineitem
where
	l_shipdate <= date '1998-12-01' - interval '90' day
group by
	l_returnflag,
	l_linestatus
order by
	l_returnflag,
	l_linestatus;
```

The query lists totals for extended price, discounted extended price, discounted extended price plus tax, average quantity, average extended price, and average discount. These aggregates are grouped by RETURNFLAG and LINESTATUS, and listed in ascending order of RETURNFLAG and LINESTATUS.
