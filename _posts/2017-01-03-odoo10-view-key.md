---
layout: post
title: Odoo10的View中不可以設定row_number() over()流水號作為Key
---

最近在測試Odoo10的時候，發現Odoo8中的客制報表在Odoo10出現排序錯誤，Filter結果也出現錯誤，追查了很久，最終發現是Odoo10的效能優化所導致的異常。

### Odoo10的查詢機制

* Odoo10的Query分為了兩步
* 首先使用查詢條件選出資料的ID欄位，其他任何搜尋結果欄位都不帶入
* 然後真正的查詢需要的欄位，這個第二步的SQL條件為ID in 第一步查出的ID

這樣查詢效能有一些提升，但因為在Odoo8中使用的是流水號作為ID，這樣兩次查詢的結果排序可能就會有差異，因而造成Odoo10中作為第二次查詢條件的in會出現不同步，而導致了此錯誤產生。
因此在Odoo10中實際使用View的時候，需要依據View的邏輯來將組成不會變化的唯一ID作為Key來解決此問題。