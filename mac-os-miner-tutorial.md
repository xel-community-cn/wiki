<!-- TITLE: Mac Os Miner Tutorial -->

<!-- SUBTITLE: A quick summary of Mac Os Miner Tutorial -->

# #MacOS-Miner 教程

## 程序安装：

```text
# Make sure you have homebrew installed (if you do, skip this step):
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
​
# Make sure to install all required dependencies
brew install gmp make cmake openssl
​
# Get the latest source code (or run a git pull to update)
git clone https://github.com/xel-software/Miner-Mainnet
​
# Make sure to link the OpenSSL include directory to the right place
ln -s /usr/local/opt/openssl/include/openssl/ /usr/local/include/openssl
​
# Compile the miner
cd xel_miner
OPENSSL_ROOT_DIR=/usr/local/opt/openssl cmake .
make
```

您可以通过运行下面的命令测试它是否正常运行：

```text
xel_miner --test-vm examples/SHA256_BTC.epl
```

使用下面的示例运行挖掘：

```text
xel_miner -t 1 -o http://nodeip:17876 -P "12 words your passphrase"
```

-t 1 为CPU线程，-o 为节点地址和端口号，-p为您的12字节私钥。

## 视频教程

[视频](https://vimeo.com/265864791 ""){.vimeo}