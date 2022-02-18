## Injections
Paper on NoSQL injection: https://arxiv.org/ftp/arxiv/papers/1506/1506.04082.pdf

## Simple bolean true injection
```sql
# This is equal to 1=1 in SQLi
http://<target-site>?id{\"\$ne\":null}
```Z