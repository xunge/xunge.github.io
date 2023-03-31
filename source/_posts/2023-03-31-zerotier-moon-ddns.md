---
title: Zerotier 搭建 Moon 节点并支持动态 IP（DDNS）
date: 2023-03-31 21:02:42
tags:
- zerotier
- ddns
categories: 知识点
---

Zerotier 搭建 Moon 节点可以减少连接延迟，但是 Moon 节点只支持静态 IP，在国内大概率只有购买 VPS，但是国内 VPS 带宽很金贵，按流量付费又有些麻烦。如果家里宽带有公网 IP 的话是最好的，但是 Moon 节点又不支持动态 IP。

本文通过脚本命令获取当前公网 IP，并与 moon.json 中的 IP 进行比对，如果不一致，则更改 moon.json 中的 IP，并重启服务。

<!-- more -->

本文是在 openwrt 里安装的 Zerotier，`luci-app-zerotier` 插件自带的 Zerotier 服务。地址为 `/etc/config/zero`。

## 搭建 moon 节点

### 生成 moon.json

```bash
cd /etc/config/zero
zerotier-idtool initmoon identity.public > moon.json
```
修改刚刚生成的配置文件 `moon.json` 中的 `stableEndpoints`：

```json
{
 "id": "xxxxx",
 "objtype": "world",
 "roots": [
  {
   "identity": "xxxx:0:eeee",
   "stableEndpoints": ["10.10.0.0/9993"]
  }
 ],
 "signingKey": "asdfasdfasdf",
 "signingKey_SECRET": "asdfasdfasdfasd",
 "updatesMustBeSignedBy": "asdfasdfasdf",
 "worldType": "moon"
}
```

此处的 `10.10.0.0` 就是公网 IP。

### 生成签名文件

```sh
zerotier-idtool genmoon moon.json
```
执行之后会生产一个 `000000xxxx.moon` 的文件

新建 `moons.d` 文件夹，将生成的 `.moon` 文件移动到 `moons.d` 文件夹中，并重启 Zerotier 服务

```sh
mkdir moons.d
mv *.moon moons.d/
/etc/init.d/zerotier restart
```

## 自动更新 Moon 节点 IP

将以下脚本放到 `/etc/config/zero/update_moon.sh` 中

```bash
#!/bin/bash
# Set the path to the moon.json file
MOON_JSON_PATH=/etc/config/zero/moon.json

# Get the current public IP address
CURRENT_IP=$(curl -4s https://checkip.amazonaws.com)
echo "Current IP address is: $CURRENT_IP"

# Get the IP address in the moon.json file
MOON_IP=$(jq -r '.roots[0].stableEndpoints[0] | split("/")[0]' $MOON_JSON_PATH)
echo "Moon IP address is: $MOON_IP"

# Check if the IP address has changed
if [ "$CURRENT_IP" != "$MOON_IP" ]; then
  echo "IP is different."
  # Update the IP address in moon.json
  sed -i 's/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+/'"$CURRENT_IP"'/g' moon.json

  # Generate the sign file
  zerotier-idtool genmoon moon.json

  # Delete exsist sign file
  rm -f moons.d/*.moon

  # Move exsist sign file
  mv *.moon moons.d/

  # Restart the zerotier service to apply the changes
  /etc/init.d/zerotier restart
  echo "Zerotier service restart."
fi
echo "IP is same."
```

使用crontab来定时执行脚本，输入以下命令来编辑当前用户的crontab文件：
```sh
crontab -e
```

然后在打开的编辑器中添加一行如下的内容，表示每天凌晨2点执行update_moon.sh脚本：

```
0 2 * * * /etc/config/zero/update_moon.sh
```

保存并退出编辑器。这会将指定的命令添加到用户的crontab中，使其每天凌晨2点自动执行。
