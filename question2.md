# 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.

```SQL
  
 with tb as (select year,
sport,
athlete,
event,
lead(athlete,1) over(partition by athlete,event order by year) as next_olympics,
lead(athlete,2) over(partition by athlete,event order by year) as other_next_olympics
from ilhan_sozer.summer_medals
where year >= 1980
order by year)
SELECT
DISTINCT sport,
athlete,
from tb
WHERE next_olympics is not null and other_next_olympics is not null
order by sport,athlete;

```
