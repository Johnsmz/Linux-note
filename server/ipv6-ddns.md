# # CloudFlare IPV6 DDNS

- DDNS 域名：**`ipv6-ddns.example.com`**
- 裸域名：**`example.com`**



### **获取裸域名的 Zone ID 及 API Key**

### **添加子域名的 AAAA 记录**

1、记录类型选择「AAAA」，也就是 IPv6 地址记录。  
2、Name 一栏填写子域名。例如 DDNS 域名是 `ipv6-ddns.example.com` 的话这里就填写 `ipv6-ddns`。  
3、IPv6 address 一栏填写 `::1` 即可。  
4、TTL 选择 「2 minutes」。  
5、由于这里不使用 CDN 功能，所以需要点击一下橙色的云图标让其变为灰色。  
6、点击「Add Record」即可添加记录。



### 查询刚才添加的 AAAA 记录的 ID

按以下格式执行命令：

```text
curl -s -X GET "https://api.cloudflare.com/client/v4/zones/<刚才获取的 Zone ID>/dns_records?type=AAAA&name=<DDNS 域名>&content=127.0.0.1&page=1&per_page=100&order=type&direction=desc&match=any" \
    -H "X-Auth-Email: <Cloudflare 账号的邮箱地址>" \
    -H "X-Auth-Key: <刚才获取的 API Key>" \
    -H "Content-Type: application/json" \
    | python -m json.tool
```

例如

```text
curl -s -X GET "https://api.cloudflare.com/client/v4/zones/12345678901234567890/dns_records?type=AAAA&name=ipv6-ddns.example.com&content=127.0.0.1&page=1&per_page=100&order=type&direction=desc&match=any" \
    -H "X-Auth-Email: mail@example.com" \
    -H "X-Auth-Key: 11111111111111111111" \
    -H "Content-Type: application/json" \
    | python -m json.tool
```

运行结果

```text
{
    "errors": [],
    "messages": [],
    "result": [
        {
            "content": "::1",
            "created_on": "2019-06-14T19:01:14.374270Z",
            "id": "22222222222222222222",    ← 22222222222222222222 就是刚才添加 AAAA 记录的 ID，请记下
            "locked": false,
            "meta": {
                "auto_added": false,
                "managed_by_apps": false,
                "managed_by_argo_tunnel": false
            },
            "modified_on": "2019-06-14T19:01:14.374270Z",
            "name": "ipv6-ddns.example.com",
            "proxiable": true,
            "proxied": false,
            "ttl": 120,
            "type": "AAAA",
            "zone_id": "12345678901234567890",
            "zone_name": "example.com"
        }
    ],
    "result_info": {
        "count": 1,
        "page": 1,
        "per_page": 100,
        "total_count": 1,
        "total_pages": 1
    },
    "success": true
}
```

命令执行后会返回一段 JSON，找到 DDNS 域名对应的那个 Object，其中 `id` 的值就是刚才添加的 AAAA 记录的 ID。



### **创建 DDNS 脚本**

请按实际情况**修改以下内容**，完成后粘贴到命令行窗口中按回车即可。

```text
cat << EOF > /etc/ipv6-ddns.sh 
#!/bin/sh
sleep 10
IP6=\`ip -6 addr show dev <拥有公网 IPv6 地址的接口名> | grep global | awk '{print \$2}' | awk -F "/" '{print \$1}'\`
[ -z \$IP6 ] && exit
curl -X PUT "https://api.cloudflare.com/client/v4/zones/<刚才获取的 Zone ID>/dns_records/<刚才获取的 AAAA 记录 ID>" -H "X-Auth-Email: <Cloudflare 账号的邮箱地址>" -H "X-Auth-Key: <刚才获取的 API Key>" -H "Content-Type: application/json" --data '{"type":"AAAA","name":"<DDNS 域名>","content":"'"\${IP6}"'","ttl":120,"proxied":false}'
EOF
chmod +x /etc/ipv6-ddns.sh 
```

例如

```text
cat << EOF > /etc/ipv6-ddns.sh 
#!/bin/sh
sleep 10
IP6=\`ip -6 addr show dev eth0 | grep global | awk '{print \$2}' | awk -F "/" '{print \$1}'\`
[ -z \$IP6 ] && exit
curl -X PUT "https://api.cloudflare.com/client/v4/zones/12345678901234567890/dns_records/22222222222222222222" -H "X-Auth-Email: mail@example.com" -H "X-Auth-Key: 11111111111111111111" -H "Content-Type: application/json" --data '{"type":"AAAA","name":"ipv6-ddns.example.com","content":"'"\${IP6}"'","ttl":120,"proxied":false}'
EOF
chmod +x /etc/ipv6-ddns.sh 
```



执行以下命令：

```text
/etc/ipv6-ddns.sh | python -m json.tool
```

稍等片刻。如果执行结果中出现 `"success":true` 的话，说明域名的 AAAA 记录已经更新成功。例如：

```text
{
    "errors": [],
    "messages": [],
    "result": {
        "content": "1234:5678:9012:3456:7890:1234:5678:9012",
        "created_on": "2019-06-14T22:20:51.008287Z",
        "id": "22222222222222222222",
        "locked": false,
        "meta": {
            "auto_added": false,
            "managed_by_apps": false,
            "managed_by_argo_tunnel": false
        },
        "modified_on": "2019-06-14T22:20:51.008287Z",
        "name": "ipv6-ddns.example.com",
        "proxiable": true,
        "proxied": false,
        "ttl": 120,
        "type": "AAAA",
        "zone_id": "12345678901234567890",
        "zone_name": "example.com"
    },
    "success": true    ← 更新成功
}
```



### **添加计划任务**

下面我们设定每分钟执行一次 DDNS 脚本。

执行以下命令：

```text
crontab -e
```

然后请在命令行窗口中按下 `O`（大写），将以下内容直接粘贴到命令行窗口中，再按下 `ESC` ，最后输入 `:wq` 按回车。

```text
* * * * * /etc/ipv6-ddns.sh > /dev/null 2>&1
```
