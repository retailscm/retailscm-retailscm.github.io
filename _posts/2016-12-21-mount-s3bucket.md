---
layout: post
title: 將S3 Mount到EC2 Ubuntu 16.04的主機上
---

S3是Amazon比較便宜的存儲方案，我們有一些備份或者是上傳檔案這一類對於並發IO要求不高的大量資料存儲可使用到S3，但S3原生的管理界面需要登入Amazon來使用，這篇則記錄下來如何將S3 Mount到EC2上，這樣則較不會有使用上的限制。

### 使用s3fs來將S3Bucket作為外部存儲

* 將EC2和S3放到同一個VPC中
* 申請Amazon的訪問證書，URL為[IAM](https://console.aws.amazon.com/iam/home?#/security_credential)，展開訪問密鑰并建立一組需要用到的密鑰
* sudo apt-get install s3fs
* 將證書密碼來產出到密碼文件中
{% highlight %}
echo MYIDENTITY:MYCREDENTIAL > /etc/passwd-s3fs
chmod 600 passwd-s3fs
sudo mv passwd-s3fs /etc/passwd-s3fs
{% endhighlight %}
* 產生好需要的Folder
{% highlight %}
sudo mkdir /s3bucket
sudo chown ubuntu:ubuntu /s3bucket
mkdir ~/cache
{% endhighlight %}
* 修改/etc/fstab，加入mybucket /s3bucket fuse.s3fs _netdev,allow_other 0 0
* reboot之後就可以看到掛載的S3了

### 相關資源

* [s3fs-fuse](https://github.com/s3fs-fuse/s3fs-fuse)
* [How To Setup S3 And FTP](http://www.rswebsols.com/tutorials/programming/setup-s3-ftp)
* [FTP/SFTP access to an Amazon S3 Bucket](http://stackoverflow.com/questions/23939179/ftp-sftp-access-to-an-amazon-s3-bucket)