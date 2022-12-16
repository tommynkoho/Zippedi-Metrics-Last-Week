# Zippedi-Metrics-Last-Week


with Last_week_Sent_to_Users as (
  
select STR_NBR, count (SKU_NUMBER) as Shelf_Out

from MET_BASE.SHELF_OUT_DATA

where thd_fiscal_week = '202237'

and source in('ZIPPEDI','ZIPPEDI_AISLE_BY_AISLE')

group by STR_NBR

order by STR_NBR),

Last_week_packdown as ( select STR_NBR, COUNT(SKU_NUMBER) as Packdown

FROM `analytics-met-thd.MET_BASE.SHELF_OUT_DATA` SO

 CROSS JOIN UNNEST(SO.SURVEY) AS QA
 
 where QA.Question_ID = 34 and QA.Answer_Id= 2 and thd_fiscal_week = '202237'

and source in('ZIPPEDI','ZIPPEDI_AISLE_BY_AISLE')

group by STR_NBR
order by STR_NBR),

Last_week_worked as ( select STR_NBR, COUNT(SKU_NUMBER) as Worked

FROM `analytics-met-thd.MET_BASE.SHELF_OUT_DATA` SO

 CROSS JOIN UNNEST(SO.SURVEY) AS QA

where QA.Question_ID = 1 and QA.Answer_Id= 2 and thd_fiscal_week = '202237'

and source in('ZIPPEDI','ZIPPEDI_AISLE_BY_AISLE')

group by STR_NBR
order by STR_NBR),

Combine1 as (select Last_week_Sent_to_Users.*, Last_week_packdown.Packdown
from Last_week_Sent_to_Users

join Last_week_packdown on

Last_week_Sent_to_Users.STR_NBR = Last_week_packdown. STR_NBR),

Combine2 as (select Combine1.*, Last_week_worked.Worked

from Combine1

join Last_week_worked on

Combine1.STR_NBR = Last_week_worked.STR_NBR)

Select  combine2.STR_NBR, combine2.Shelf_out, Combine2.Worked, Combine2.Packdown, (combine2.Worked/Combine2.Shelf_out)*100 as Worked_Percentage, (Combine2.Packdown/Combine2.Worked)*100 as Packdown_Percentage, `analytics-met-thd.MET_BASE.MV_MET_STR_HIER`.RGN_NM as Regions

from combine2

join `analytics-met-thd.MET_BASE.MV_MET_STR_HIER` on 

combine2.STR_NBR = `analytics-met-thd.MET_BASE.MV_MET_STR_HIER`.STR_NBR

group by Regions, Combine2.Shelf_out, combine2.Worked, Combine2.Packdown, Worked_Percentage, Packdown_Percentage,combine2.STR_NBR;






![image](https://user-images.githubusercontent.com/17092274/208141540-3211b216-7829-41a2-ad35-70704e2af42c.png)
