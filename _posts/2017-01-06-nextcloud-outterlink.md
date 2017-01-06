---
layout: post
title: NextCloud的一些做法
---

目前在幫公司Survey一些fileserver的解決方案，現在的傾向的方式為用S3作為資料存儲空間，并將其Mount至EC2上，并在EC2安裝NextCloud作為Private Cloud的前端。但因為內部需求，有需要這個fileserver能夠提供http的service將檔案可以外鏈至其他的系統或網站上呈現，這部分在NextCloud的原生做法中就無法Support，所以我在這邊有做出了一些調整記錄如下。

### 讓NextCloud可以外鏈

* 因為NextCloud本身不能外鏈，所以要架設另外一個http server來進行外鏈
* 為避免NextCloud所存資料皆被外鏈取走，因此只針對個別類型的文件開放外鏈，其他類型文件不可外鏈
* NextCloud設定為在分享后，分享頁面下多增加一個Outter Link的textbox來放外鏈的URL

### 修改NextCloud的程式，增加Outter Link

* 修改nextcloud/apps/files_sharing/templates/public.php，尋找<div class="directLink">并在這個tag下增加

{% highlight js %}
<div class="directLink">
	<label for="outterLink"><?php p($l->t('Outter link')) ?></label>
	<input id="outterLink" type="text" readonly value="<?php p('http://fileserver.bizcloud.xyz/'.$_['owner'].'/'.$_['filepath']) ?>">
</div>
{% endhighlight %}

* 然而還有一個問題是在原始的NextCloud中并沒有將filepath開放出來，因此還要修改nextcloud/apps/files_sharing/lib/Controller/ShareController.php，尋找$shareTmpl['dir'] = '';在其下增加

{% highlight js %}
$shareTmpl['filepath'] = $share->getNode()->getInternalPath();
{% endhighlight %}

### Nginx的設定

{% highlight js %}
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /home/ubuntu;

	server_name fileserver.bizcloud.xyz;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	location ~* \.(pdf|html|html|xml|gif|jpg|png|js|css)$ {
		root /fileserver/nextcloud;
	}

}
{% endhighlight %}
