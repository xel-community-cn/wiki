<!-- TITLE: Windows Miner Tutorial -->

<!-- SUBTITLE: A quick summary of Windows Miner Tutorial -->

## #Windows- miner 安装教程

## 从文件安装

```text
#下载最新(仅试验性CPU)文件版本：
https://github.com/xel-software/xel_miner_releases/releases
```

当您解压存档后，请转到文件夹xel_miner-0.9.6/xel_miner 在这里，您会找到xel_miner.exe的执行文件。 您可以通过运行下面的命令测试它是否正常运行：

```text
xel_miner.exe --test-vm examples\SHA256_BTC.epl
```

如果您遇到错误，可能是您已经在系统上（PATH）安装了一个MinGW。 这种情况下，可以尝试通过简单的方式清除PATH变量：

```text
set PATH=
```

需要运行挖矿(mining)可以使用cmd然后前往miner的目录：

```text
cd c:/xel_miner-0.9.6/xel_miner
```

使用下面的示例进行挖矿设置（mining）：

```text
xel_miner.exe -t 1 -o http://nodeip:17876 -P "12 words your passphrase"
```

-t 1 是cpu线程 -o 节点地址与端口 -p 为您的12字节私钥

## 视频教程

[视频](https://vimeo.com/265864726 ""){.vimeo}