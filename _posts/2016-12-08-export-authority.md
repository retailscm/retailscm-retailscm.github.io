---
layout: post
title: 設定Export的權限
---

這邊文章原始資料來源於[hitechnologia的一篇分享](http://www.hitechnologia.com/forum/odoo-forum-1/question/odoo-openerp-how-to-hide-export-option-more-export-in-odoo-for-single-user-or-all-users-315)，雖然原始版本是對Odoo8所來寫的，但是這個思路卻可以應用在Odoo10上面。

### 修改Odoo10來實現讓Data匯出也在權限控制範圍內

* 修改addons下的web模組，找到static/src/js/views/下的list_view.js
* 修改288行
{% highlight js %}
{ label: _t("Export"), callback: this.on_sidebar_export },
{% endhighlight %}
修改為
{% highlight js %}
this.is_action_enabled('edit') && { label: _t("Export"), callback: this.on_sidebar_export },
{% endhighlight %}
新增加的部分表示只有使用者有修改權限的時候才可以做資料匯出，也可以改為如delte等權限