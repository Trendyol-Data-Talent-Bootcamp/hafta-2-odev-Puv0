# SORU 4 

```SQL
  merge ilhan_sozer.content_category t
using ilhan_sozer.content_category_20201222_00_59  s
on t.id =s.id
-- id eşleşmiş, cdc_dateler farklı ise is_deleted kısmı false değilse, ilgili kayıt güncelleme almıştır
when matched and t.cdc_date != s.cdc_date and cast(s.is_deleted as string) = "false" then
 update set t.cdc_date = s.cdc_date , t.category =s.category 
 -- eğer source tarafından eşlenmemiş bir kayıt varsa, bu target tablosundan silinmiştir bu sebeple is_deleted true olur
 when not matched by source then
 update set t.is_deleted = true
-- Eğer ilgili kayıt, target tablosunda yok ise, insert işlemi gerçekleşir
when not matched by target then
 INSERT (cdc_date,is_deleted,id,category)  VALUES(s.cdc_date,s.is_deleted,s.id,s.category);
 
 -- hashlenmiş iki tabloyu joinleyerek hash değerlerinin aynı olup olmadığını kontrol ettim. 110 tane eşleşmemiş değer çıktı, umarım değişen çok fazla ürünü doğru mergelemişimdir 
 -- merge sorgusunu bir kaç farklı şekilde daha denedim ama bir şey değiştirmedi ... :/ 
 Select farm_fingerprint(to_json_string(t1)),farm_fingerprint(to_json_string(t2))
from ilhan_sozer.content_category t1
FULL OUTER JOIN ilhan_sozer.content_category_target t2
on t1.id = t2.id
where farm_fingerprint(to_json_string(t1)) != farm_fingerprint(to_json_string(t2))

```
