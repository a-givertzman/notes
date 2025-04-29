# Postgres database

> Some remarks ...  

## Installation

> Some remarks about installation process ...  
> [How to login and authenticate to Postgresql after a fresh install?](https://stackoverflow.com/questions/2172569/how-to-login-and-authenticate-to-postgresql-after-a-fresh-install)

## Authentication

- **Default credentials**
  - host: localhost
  - port: 5432
  - user: postgres
  - pass: postgres  

  ```bash
  "postgresql://user:pass@host:5432/database"
  ```

- **Allow remote access to the default postgres user do:**  
`sudo -u postgres psql postgres`  
then

  ```bash
  \du or \du+
  \password postgres
  ```

  - Enter new password for user "postgres": postgres  
  - Enter it again: postgres  
  - Ready!

## Indexing

### Monitoring indexes

- Duplications
  - V1
  
  ```sql
  SELECT a. indrelid::regclass, a. indexrelid::regclass, b. indexrelid::regclass
  FROM (SELECT *,array_to_string(indkey,' ') AS cols FROM pg_index) a JOIN
    (SELECT * array_to_string(indkey,' ') AS cols FROM pg_index) b ON
  ( a.indrelid=b.indrelid AND a. indexrelid › b. indexrelid AND
    (a.cols LIKE b.cols||'%' AND coalesce(substr(a.cols,length(b.cols)+1,1),' ')=' ') OR
    (b.cols LIKE a.cols||'%' AND coalesce(substr(b.cols,length(a.cols)+1,1),' ')=' ')
  ) ORDER BY indrelid;
  ```
  
  <!-- ![Source](./assets/Indexes-Monitoring_1.png) -->
  <img alt="Source" width="70%" src="./assets/Indexes-Monitoring_1.png" style="margin: 0 15% 0 15%;">

  - V2

  ```sql
  SELECT idstat.relname AS tname, indexrelname AS iname, idstat.idx_scan AS used,
    pg_size_pretty(pg_relation_size(idstat.relid)) AS tsize, 
    pg_size_pretty(pg_relation_size(indexrelid)) AS isize,
    n_tup_upd + n_tup_ins + n_tup_del as writes, indexdef AS create
  FROM Pg_stat_user_indexes AS idstat
  JOIN Pg_indexes ON (indexrelname = indexname AND idstat. schemaname = pg_indexes.schemaname
  JOIN pg_stat_user_tables AS tabstat ON idstat.relid = tabstat.relid
  WHERE idstat.id_scan ‹ 200 AND indexdef !~* 'unique'
  ORDER BY idstat.relname, indexrelname;
  ```

  <!-- ![Source](./assets/Indexes-Monitoring_2.png) -->
  <img alt="Source" width="70%" src="./assets/Indexes-Monitoring_2.png" style="margin: 0 15% 0 15%;">

- Unused indexes

```sql
SELECT
    schemaname 11 '.' I| relname AS table, indexrelname AS index,
    pg_size_pretty(pg_relation_size(i.indexrelid)) AS index_size, id_sican AS index_scans
  FROM pg_stat_user_indexes ui
  JOIN pg_index i ON ui. indexrelid = i. indexrelid
  WHERE NOT indisunique AND idx_scan < 50 AND pg_relation_size(relid) > 5 * 819
  ORDER BY pg_relation_size(i. indexrelid) / nullif(idx_scan, 0) DESC NULLS FIRS pg_relation_size(i.indexrelid) DESC;
```

  <!-- ![Source](./assets/Indexes-Unused.png) -->
  <img alt="Source" width="70%" src="./assets/Indexes-Unused.png" style="margin: 0 15% 0 15%;">
