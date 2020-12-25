# SORU 4 

```SQL
  merge ilhan_sozer.content_category t
using ilhan_sozer.content_category_20201222_00_59  s
on t.id =s.id
--id eşleşmiş ve cdc_date değişmediyse, değişiklik yapılmasına gerek yoktur diye düşünerek bu şekilde yazdım.
when matched and t.cdc_date = s.cdc_date then
  update set t.id = s.id
  --- idler eşleşti ama cdc_date değiştiyse, ve is_deleted false ise ürün güncellenmiştir, bu yüzden cdc_date ve category güncelledim
when matched and t.cdc_date != s.cdc_date and cast(s.is_deleted as string) = "false" then
 update set t.cdc_date = s.cdc_date , t.category =s.category 
 -- eğer is_deleted true ise ürünün silindiğini belirttim
 when matched and cast(s.is_deleted as string) = "true" then
 update set t.cdc_date = s.cdc_date , t.is_deleted = s.is_deleted
 -- eğer target tarafından eşleşmediyse, yeni bir ürün eklenmiş bu sebeple insert komutu gerçekleştirdim.
when not matched by target then
 INSERT (cdc_date,is_deleted,id,category)  VALUES(s.cdc_date,s.is_deleted,s.id,s.category);
 
 -- hashlenmiş iki tabloyu joinleyerek hash değerlerinin aynı olup olmadığını kontrol ettim. 110 tane eşleşmemiş değer çıktı, umarım değişen çok fazla ürünü doğru mergelemişimdir 
 -- merge sorgusunu bir kaç farklı şekilde daha denedim ama bir şey değiştirmedi ... :/ 
 Select farm_fingerprint(to_json_string(t1)),farm_fingerprint(to_json_string(t2))
from ilhan_sozer.content_category t1
FULL OUTER JOIN ilhan_sozer.content_category_target t2
on farm_fingerprint(to_json_string(t1)) = farm_fingerprint(to_json_string(t2))
where farm_fingerprint(to_json_string(t1)) != farm_fingerprint(to_json_string(t2))

```
