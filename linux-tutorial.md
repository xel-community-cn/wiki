<!-- TITLE: Linux Tutoria -->

<!-- SUBTITLE: A quick summary of Linux Tutoria -->

# Linux - 教程

这个教程将一步一步地指导如何在一个专用服务器或者ubuntu16.04的VPS上设置和运行Elastic节点。

* * *

## **准备一个安装客户端的服务器**

如何链接您的新服务器？

<p>如果您使用的是Windows,可以下载 Putty 客户端，下面是如何链接您服务器的介绍：</a> </p>

```text
https://mediatemple.net/community/products/dv/204404604/using-ssh-in-putty-
```

如果您使用的是Linux,通过您的控制台（Alt+Ctrl+T）输入：

```text
ssh username@youriporhostname.xyz
```

登陆后，您将开始安装Elastic节点的配套并编译:

```text
sudo apt update &&sudo apt upgrade && sudo apt dist-upgrade
```

(如果您没有安装sudo或者不是一个root账户，那么就去掉此教程中的所有sudo命令)

安装一些基础安装包：

```text
sudo apt install mc ntp fail2ban htop screen nano -y
```

安装Java

```text
sudo apt install software-properties-common -y

sudo add-apt-repository ppa:webupd8team/java

sudo apt update

sudo apt install oracle-java8-installer -y
```

（您将会被要求接收一些TOS/许可请求） 然后，需要校验下您的Java是否为正确版本：

```text
java -version
```

您将会看到下面的反馈信息:

```text
Java version "1.8.0_121"

Java(TM) SE Runtime Environment (build 1.8.0_121-b13)

Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
```

(请确保Java版本高于1.8,因为Elastic需要这样的环境)

* * *

## **安装elastic客户端**

> 请不要以root用户运行Elastic。 特别提示。

如果当前您是以root用户登陆，创建一个新账户，那么请使用下面这个新用户完成剩余的命令。 如果您使用的是一个普通用户，您可以跳过这一步。

```text
adduser elastic
```

您将会被问到一些关于用户的问题。 随便写点什么信息。 然后设置新用户的密码。 请将密码设置复杂一些 (最多16字符, 最少应包含1个大写，1个小写，1个数字)。 如果不想费劲，可以使用密码生成器，如果这样，请将密码保存在安全的地方。

```text
passwd elastic
```

注销root账户。 我们不再需要它了。

> 请记住，后面不要再登陆root账户了。 特别提示。

如果您登陆root用户并运行elastic，那么一些文件的权限将会改变，这样新的用户就不能再运行elastic了。

```text
exit
```

在此输入新创建的密码。 现在您应该在您的主目录下了。 输入下面的命令检查。

```text
pwd
```

你会看到：

```text
/home/elastic
```

启动MC，并按F7创建一个新目录。 命名为‘elastic’(不需要引号) 转到此目录，最小化MC(Ctrl+O)。

复制粘贴

```text
git clone https://github.com/xel-software/Litewallet-Mainnet.git 
```

最大化MC(Ctrl+O)并转到Litewallet-Mainnet目录。 最小化然后：

```text
./compile.sh
```

最大化，现在您需要退出MC了（F10）。 您需要手动进入 Litewallet-Mainnet 目录，因为您需要在屏幕上启动它。 所以，如果您拥有像前面提到的目录结构，您可以输入：

```text
cd /home/elastic/elastic/Litewallet-Mainnet
```

您需要在 Litewallet-Mainnet 目录下。 如果您的用户名是不同 (例如ubuntu) 类型：

```text
cd /home/ubuntu/elastic/Litewallet-Mainnet
```

希望你能明白。 如果您因某些原因丢失一些东西，可以查看“ls”命令，它将帮助您确认当前的目录文件和目录。

如果您在 Litewallet-Mainnet 目录类型中

```text
screen ./run.sh
```

该命令将从github中，更新为最近的版本，用maven 编译并启动Elastic。 检查是否所有的启动都已完成。

如果你想从屏幕返回您的服务器控制台**LEAVING Elastic running in background**按下Ctrl(长按)，然后按下A键**释放**然后按下D键。

此时您应该返回控制台，Elastic一直运行，如果您退出了服务，Elastic将还会继续运行。 如果你想返回 Elastic (例如关闭它) 输入：

```text
screen -r
```

此命令使您的窗口显示elastic实例(即使您下次登录到您的节点，仍然保持) 如果您想关闭elastic Ctrl + C。 Elastic和窗口会自动退出。

现在 (一旦 Elastic关闭) 再次启动它时，你就可以更新Elastic到最新版本。

```text
cd /home/elastic/elastic/Litewallet-Mainnet

git pull

./compile.sh

screen ./run.sh
```

目前为止，你拥有了完整的节点运行！ 您现在已经在给网络贡献了，如果您只是想贡献block/txs，就不需要再了解更多了。 请记得更新您的节点，当新版本显示的时候。

但是，您也可能希望使用浏览器登录你的钱包，例如forging（或仅仅为了查看是否运行，查看块和txs 等）。 这就需要创建一个SSL文件，以保护自己免受攻击。

首先，您需要停止您的 Elastic 节点(见上文)，并进行一些配置更改，以便能够远程访问它。

下一步，我们需要生成 SSL 证书

```text
cd /home/elastic/elastic/Litewallet-Mainnet
```

* * *

```text
keytool -genkeypair -keyalg RSA -keysize 2048 -validity 3650 -keystore keystore
```

在这里将要求您填写一些基本信息（填满就行）密码同样如此。 记住请在此处设置一些强大的密码 (最大 32 随机字符, 最好使用密码生成器), 在这里粘贴并保存到安全的地方。 当然最好是一分钟内可以完毕的密码。 启动 MC，并转到您的 Elastic-XEL-Litewallet/conf 目录。 当您在 conf 目录中需要最小化MC (Ctrl + O) 可以输入：

```text
touch nxt.properties
```

这将是您自定义elastic默认属性的文件。 如果您想要看到所有这些属性, 可以查看 nxt-default.properties文件。 您可以通过 mc 来去这个目录，标记文件点击F4 查看其内容 (请您在此选择默认编辑器，很容易)。 当你停止阅读并想要退出 时可以按下Ctrl + X (选择N，不保存任何更改)。 记住，你不能更改nxt-default.properties的内容，因为不论你改变什么，都需要更新你的elastic。 这也是为什么我们先创建一个新的配置文件的目录。 回到 conf/nxt.properties，在MC中选择该文件，然后编辑它 (F4)。 您将看到一个空文件。 请黏贴下面的内容：

```text
# Hosts from which to allow http/json API requests, if enabled. Set to * to
​
# allow all. Can also specify networks in CIDR notation, e.g. 192.168.1.0/24.
nxt.allowedBotHosts=*
​
# Host interface on which to listen for http/json API request, default localhost
​
# only. Set to 0.0.0.0 to allow the API server to accept requests from all
​
# network interfaces, including IPv6.
nxt.apiServerHost=0.0.0.0
​
# Password that should be provided when executing protected (administrative) API
​
# requests.
# Please choose a decent password here. Preferably, use a password generator.
# Password protection is disabled and password is not needed when the API server
​
# only listens on the localhost interface, i.e. when 
​
# nxt.apiServerHost=127.0.0.1.
​
nxt.adminPassword=generateSomeAdminAPIPasswordAndPasteHere
​
# Enable SSL for the API server (also need to set nxt.keyStorePath and
​
# nxt.keyStorePassword).
# Non-SSL connections will be disabled if nxt.apiServerSSLPort is equal to
​
# nxt.apiServerPort.
# Otherwise, both SSL and non-SSL connections will be accepted.
nxt.apiSSL=true
​
# keystore file and password, required if uiSSL or apiSSL are enabled.
nxt.keyStorePath=keystore
​
nxt.keyStorePassword=passwordYouProvidedDuringSSLCertGeneration
```

你可以看到，管理员密码是“generateSomeAdminAPIPassedAndrPastere”。 再生成另一个密码。 您不需要保存这个密码，因为您并不需要它。 当你想要为这个世界贡献节点时，是必须要设置的。

输入存档密码“passwordYouProvidedDuringSSLCertGeneration”。 用SSL环节生成的密码替换它。

通过点击Ctrl + X 来保存您的更改 (如果您想要保存、选择Y。如果您使用不同语言安装了Ubuntu, 它可能是一个对应于“是”的词)，然后你应该返回 到MC。 退出MC (F10)。

## **运行节点**

```text
cd /home/elastic/elastic/Litewallet-Mainnet
```

```text
screen ./run.sh
```

如果你想从屏幕返回您的服务器控制台**LEAVING Elastic running in background**按下Ctrl(长按)，然后按下A键**释放**然后按下D键。

允许您的钱包打开您的浏览器，并前往：

```text
https://your_server_ip_address_or_hostname:17876
```