# 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.

sport_group tablosu ile, her spor dalında en çok madalya kazanmış ülkeleri sıraladım ve her spor için row numaraları verdim.

```SQL
with sport_group as ( 
select sport,country,count(medal) as total_medal,
rank() over(partition by sport order by count(medal) DESC) as row_number
from ilhan_sozer.summer_medals
group by sport,country
order by sport,total_medal DESC
)
-- bu sorgu ile de sıralanmış tablomuzdan 1. 3. 5. satırları seçerek ilgili ülkeleri bulmuş oldum
SELECT 
  sport,
  country,
  total_medal,
  row_number
  from sport_group
  where row_number in(1,3,5)
```
