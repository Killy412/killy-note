| 提供者   | 首选DNS           | 备用DNS           |
| -------- | ----------------- | ----------------- |
| 北京联通 | `202.106.0.20`    | `202.106.196.115` |
| 南方电信 | `180.153.225.136` |                   |
| 阿里     | `223.5.5.5`       | `223.6.6.6`       |
| 百度     | `180.76.76.76`    |                   |
| 腾讯     | `119.29.29.29`    | `182.254.116.116` |
| 360      | `101.226.4.5`     |                   |
| 114      | `114.114.114.114` | `114.114.115.115` |
| google   | 8.8.8.8           | 8.8.4.4           |

## 安装[ resolvconf ](https://note.qidong.name/2020/05/resolvconf/#安装resolvconf)

默认情况下，Deepin并没有安装`resolvconf`，这可能是问题的关键。

```sh
sudo apt install resolvconf
```

一般直接使用的是`/etc/resolv.conf`文件。 安装了`resolvconf`后，不仅多了对应的可执行文件，还新增了`/etc/resolvconf/`这个配置目录。 其中，主要有三个配置。

- `/etc/resolvconf/resolv.conf.d/base`：在自动生成的`resolv.conf`为空时，替换它。
- `/etc/resolvconf/resolv.conf.d/head`：在自动生成的`resolv.conf`前添加。
- `/etc/resolvconf/resolv.conf.d/tail`：在自动生成的`resolv.conf`后添加。

一般使用的是`head`。 在其中添加以下内容（具体的IP则根据自己的实测情况选择）：

```sh
nameserver 114.114.114.114
nameserver 8.8.8.8
```

修改后，执行以下命令。 它会立即更新`resolv.conf`文件，即刻生效。

```sh
sudo resolvconf -u
```