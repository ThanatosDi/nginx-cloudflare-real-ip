# 使用Nginx和CloudFlare獲取真實的訪客IP地址（還原訪客IP）
該項目旨在修改您的nginx配置，以使您能夠獲得Webflare反向代理網絡背後的Web應用程序訪問者的真實IP地址。可以安排Bash腳本來創建一個自動的最新Cloudflare ip列表文件

為了向原始請求提供每個客戶端（訪問者）IP地址，Cloudflare添加了“ CF-Connecting-IP”標頭。我們將捕獲標題並獲取訪問者的真實IP地址

## Nginx配置
通過小的配置修改，我們可以集成替換訪問者的真實IP地址，而不是獲取CloudFlare的負載均衡器的IP地址

用您喜歡的文本編輯器打開“ /etc/nginx/nginx.conf”文件，然後將以下幾行添加到http {....}塊中的nginx.conf中。
```bash
include /etc/nginx/cloudflare;
```
bash腳本可以手動運行，也可以安排自動刷新CloudFlare的IP列表
```bash
#!/bin/bash
echo "#Cloudflare" > /etc/nginx/cloudflare;
echo "" >> /etc/nginx/cloudflare;

echo "# - IPv4" >> /etc/nginx/cloudflare;
for i in `curl https://www.cloudflare.com/ips-v4`; do
        echo "set_real_ip_from $i;" >> /etc/nginx/cloudflare;
done

echo "" >> /etc/nginx/cloudflare;
echo "# - IPv6" >> /etc/nginx/cloudflare;
for i in `curl https://www.cloudflare.com/ips-v6`; do
        echo "set_real_ip_from $i;" >> /etc/nginx/cloudflare;
done

echo "" >> /etc/nginx/cloudflare;
echo "real_ip_header CF-Connecting-IP;" >> /etc/nginx/cloudflare;

#test configuration and reload nginx
nginx -t && systemctl reload nginx
```

## 輸出
您的 "/etc/nginx/cloudflare" 文件可能如下所示  
```
#Cloudflare ip addresses

# - IPv4
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 104.16.0.0/12;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 131.0.72.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 173.245.48.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;

# - IPv6
set_real_ip_from 2400:cb00::/32;
set_real_ip_from 2405:8100::/32;
set_real_ip_from 2405:b500::/32;
set_real_ip_from 2606:4700::/32;
set_real_ip_from 2803:f800::/32;
set_real_ip_from 2c0f:f248::/32;
set_real_ip_from 2a06:98c0::/29;

```

### License

[Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0)


### DISCLAIMER
----------
請注意：此倉庫中的所有工具/腳本均按“原樣”發布，沒有任何形式的保證，包括但不限於其安裝，使用或性能。我們不承擔任何和所有明示或暗示的保證，包括但不限於任何非侵權，適銷性和/或適用於特定目的的保證。我們不保證該技術將滿足您的要求，其操作不會中斷或沒有錯誤，或者任何錯誤都會得到糾正。

使用這些腳本和工具的風險自負。不能保證它們已經在可比的環境中經過了全面的測試，對於因使用它們引起的任何損壞或數據丟失，我們不承擔任何責任。

在任何非測試環境中使用之前，您有責任檢查和測試您徹底運行的所有腳本。

謝謝，

[Ergin BULUT](https://www.erginbulut.com)
