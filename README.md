# BBR Plus / BBRv2 / Cake 加速配置

本文介绍各种 TCP 加速算法，选择最适合你的方案。

## 标准 BBR（内核 4.9+）

```bash
# 开启 BBR
cat >> /etc/sysctl.conf << 'EOF'
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
EOF
sysctl -p
```

## BBR Plus（推荐）

BBR Plus 是 BBR 的优化版，在高丢包环境下表现更好：

```bash
wget -O tcp_bbr_powered.sh https://github.com/ylx2016/Linux-NetSpeed/raw/master/tcp.sh
chmod +x tcp_bbr_powered.sh
./tcp_bbr_powered.sh
```

## BBRv2

BBR v2 在 BBR 基础上优化了丢包重传：

```bash
cat >> /etc/sysctl.conf << 'EOF'
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr2
EOF
sysctl -p
```

## Cake 队列管理

Cake 是更智能的队列管理算法：

```bash
tc qdisc del dev eth0 root
tc qdisc add dev eth0 root cake bandwidth 1gbit nat no-split-gso
```

## 加速效果对比

| 算法 | 适用场景 | 优点 | 缺点 |
|------|---------|------|------|
| BBR | 低丢包 | Google开发，稳定 | 高丢包效果一般 |
| BBR Plus | 中等丢包 | 优化重传 | 需要换内核 |
| BBRv2 | 高丢包 | 最新优化 | 需要新内核 |

## 常见问题

**BBR Plus 安装后无法启动？** 检查 grub 引导顺序，确保新内核优先启动。

---

推荐工具：

- [Clash for Windows](https://clashforwindows.site/)
- [ClashMI](https://clashmi.site/)
- [FlClash](https://flclash.us/)
