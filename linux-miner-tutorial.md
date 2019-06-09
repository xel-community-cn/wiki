<!-- TITLE: Linux Miner Tutorial -->

<!-- SUBTITLE: A quick summary of Linux Miner Tutorial -->

# Linux - miner 教程

## 程序安装

```text
#安装所有需要的配套
sudo apt-get install git libgmp-dev libcurl4-openssl-dev libssl-dev build-essential cmake

# 编译
make
```

您可以通过运行下面的命令测试它是否正常运行：

```text
sudo ./xel_miner --test-vm examples/SHA256_BTC.epl
```

使用下面的示例进行挖矿：

```text
sudo ./xel_miner -t 1 -o http://nodeip:17876 -P "12 words your passphrase"
```

-t cpu线程 -o 节点地址与端口 -P "私钥"

## 视频教程

[视频](https://vimeo.com/265864834 ""){.vimeo}