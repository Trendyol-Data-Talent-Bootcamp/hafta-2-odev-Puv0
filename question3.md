# Günün her bir dakikası için aktif kullanıcı sayısının hesaplanması.

per_min tablosunda, her dakika bulunan farklı device id sayısını bulduktan sonra şimdiki dakika ile, beş dakika sonrasının toplamını hesaplayarak aktif kullanıcı sayısını hesapladım

```SQL
with per_min as(
select
timestamp_trunc(view_ts,minute) view_period,
count(distinct deviceid) active_user_count,
from ilhan_sozer.pageview
group by 1
order by view_period
)
SELECT 
view_period,
sum(active_user_count) OVER (order by view_period rows between 5 preceding and current row) as active_user_count
from per_min

```
