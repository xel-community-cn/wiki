<!-- TITLE: Start The Xel Miner -->

<!-- SUBTITLE: A quick summary of Start The Xel Miner -->

## #启动xel miner

在这个教程中，我们要展示的是如何使用miner，以及在XEL的任务中是如何工作的。 这里只讲解如何开始挖矿（mining）,后续我们会有更多的细节讲解，比如miner的跟部分功能函数。

* * *

## 先决条件（可选）

在开始之前，您必须要决定是使用远程连接或者是使用一个本地节点。 对于Xeline，是必须要使用远程钱包的，但是由于签名是本地完成的，所以您的私钥从未离开过您的电脑，不需要担心安全问题。 与miner的情况不同：当需要提交POW证明或者候选结果的时候，您的私钥也提交给了服务器，因为到目前为止，miner还没有办法来签署内置的签名（建议您注意此处风险）。 我们希望在以后能解决这个问题。

所以，最安全的还是运行一个本地XEL节点在您的系统上。 在此之前，您必须确保在您的电脑上安装了Docker并且保证是一个全功能的Docker。 关于Docker，网上有很多的教程可以帮助你安装在不同的平台上。

下一步，您需要从GitHub上克隆一个XEL的docker库：

```text
git clone https://github.com/xel-software/Computationwallet-Mainnet-Docker.git
```

现在进入到xel_docker路径并且开启：

```text
docker build -t xel .
```

如果此处出现错误，在PATH中可能没有您的Docker执行文件。 经过几分钟的等待，您的XEL-docker图标应该就准备好了。 如果您是在macOS机器中运行linux，启动这样的节点是非常简单的。 只需要执行下面的脚本：

```text
./run_with_computation.sh
```

如果您使用的是Windows， 请直接使用Docker来启动它。 请确保在xel-docker/目录下启动下面的命令：

```text
docker run -p 17876:17876 -p 17874:17874 -p 16876:16876 -p 16874:16874 --mount type=bind,source=xel_data_dir,target=/elastic-core-maven/nxt_test_db --mount type=bind,source=conf,target=/elastic-core-maven/conf_user -i -t xel
```

现在，您需要给节点一些时间。 需要等待区块完全的同步。 也许只需去散步，喝一杯咖啡或者短暂休息的时间。

* * *

## 安装Miner

<p> 我们在ePL语言的文章中包含有miner的安装。 如果您还没有安装过，请遵循那里的安装说明。 </a></p>

* * *

## Miner的使用

当您在xel_miner执行文件的目录下时，建议您最好看一看xel_miner的帮助信息如下命令：

Linux和maxOS系统，使用：

```text
./xel_miner --help
```

Windows系统，使用：

```text
xel_miner.exe --help
```

在教程的其余部分，我们将只讲解Lunux/macOS的变量，Widows较为简单也很相似。 当您启动命令后，您会看到帮助信息：

```text
** Elastic Compute Engine **
   Miner Version: 0.9.6
   ElasticPL Version: 0.9.4
Usage: xel_miner [OPTIONS]
Options:
  -c, --config          Use JSON-formated configuration file
      --deadswitch   Hardkill the instance after x seconds
  -D, --debug                 Display debug output
      --debug-epl             Display EPL source code
  -d, --delaysleep            Sleep x seconds after submitting POW: useful for burstless debugging
   -i, --ignoremask           Debug only: ignore 0=nothing, 1=PoW, 2=Bty, 3=Both
   -h, --help                 Display this help text and exit
  -m, --mining PREF[:ID]      Mining preference for choosing work
                                profit       (Default) Estimate most profitable based on POW Reward / WCET
                                wcet         Fewest cycles required by work item
                                workid       Specify work ID
      --no-color              Don't display colored output
      --opencl                Run VM using compiled OpenCL code
      --opencl-gthreads    Max Num of Global Threads (256 - 10240, default: 1024)
      --opencl-vwidth     Vector width of local work size (1 - 256, default: calculated)
  -o, --url=URL               URL of mining server
  -p, --pass        Password for mining server
  -P, --phrase    Secret Passphrase for Elastic account
      --protocol              Display dump of protocol-level activities
  -q, --quiet                 Display minimal output
  -r, --retries            Number of times to retry if a network call fails
                              (Default: Retry indefinitely)
  -R, --retry-pause        Time to pause between retries (Default: 10 sec)
  -s, --scan-time          Max time to scan work before requesting new work (Default: 60 sec)
      --test-miner      Run the Miner using JSON formatted work in 
      --test-vm         Run the Parser / Compiler using the ElasticPL source code in 
      --test-avoidcache       Do not save metadata
      --test-block    Block-id for test run
      --test-cont-bounty      Search for bounties within test-vm environment
      --test-cont-pow         Search for proof-of-work within test-vm environment
      --test-work     Work-id for test run
      --test-limit-storage          Only allow storage sizes up to 
      --test-multiplicator <32-byte-hex>    Multiplicator for testrun: must be exactly 32 hex chars
      --test-publickey <32-byte-hex>        Publickey for testrun: must be exactly 32 hex chars
      --test-stdin                          Read storage values from stdin
      --test-target <16-byte-hex>           Target for test run: must be exactly 16 hex chars
      --test-wcet-main      Do not ignore WCET limits of main function in Test-Vm run
      --test-wcet-verify    Do not ignore WCET limits of verify function in Test-Vm run
  -t, --threads            Number of miner threads (Default: Number of CPUs)
  -u, --user        Username for mining server
  -T, --timeout            Timeout for rpc calls (Default: 30 sec)
      --validate              Validate logic in 'main' & 'verify' functions
      --verify-only           Use verify instead of main
  -v, --version               Display version information and exit
  -X  --no-renice             Do not lower the priority of miner threads
Options while mining ----------------------------------------------------------

   s +                 Display mining summary
   d +                 Toggle Debug mode
   q +                 Toggle Quite mode
```

这里将不会对每一个选项都做讲解，更多的时对那些用于miner在XEL网络执行任务的选项。 首先，我们要查看下线程的配置参数：

```text
-t, --threads            Number of miner threads (Default: Number of CPUs)
```

此参数允许您配置运行xel_miner的电脑运行多少个线程。 使用的线程越多，xel_miner更容易找到解决方案（结果）。 另外，如果您设置的线程数太多，那么电脑将毫无保留地运行任务（并不建议您这样做）。

我们下一个需要关注的是url配置：

```text
-o, --url=URL               URL of mining server
```

这里的配置可以将miner设置为远程节点或者本地节点。 两种方式都是可以的，但是需要记住远程的URL当前会上传您的私钥到服务器（后期会解决）。 如果您不信任远程节点，那么请使用本地节点。 URL的格式总是以http://hostname:port的方式呈现，其中hostname是主机的IP或者FQDN，其中port是端口号，端口号设置为17876是主网，设置为16876是测试网（请注意区别）。

下面是第三个重要参数私钥标志：

```text
-P, --phrase    Secret Passphrase for Elastic account
```

`这个参数是必须要设置的。`

这个账户的私钥是用于支付提交任务的手续费，和提交工作结果后获取报酬。 填写私钥后，请用双引号标注下。

`请确保您的账户内拥有一些xel，否则您将无法提交任何结果至主网（fee）。`

出于测试目的，您可以使用12字节的Xeline助记符（用双引号括起来），这样方便从水龙头获得一些测试XEL。

现在，在这个教程里面，我们想要启动一个miner，只运行一个线程，使用本地节点在测试网络上挖掘（mining）。 因此，我们要这样设置：

```text
./xel_miner -t 1 -o http://localhost:16876 -P "our twelve word passphrase goes here"
```

此时此刻，您的miner已经在运行并解决POW计算包中，当它找到了结果，就会提交给算法它的悬赏/结果。 让我们看看下面的小的异常。

您可能已经注意到这些行：

```text
[19:15:27] CPU0: ***** POW limit reached for this block, pausing until next block *****
[19:15:43] CPU0: ***** Bounty limit reached for this block, pausing until next block *****
```

第一行意味着下一个区块到来之前，已经没有必要再继续提交了，因为该区块未确认的转账缓存，已经达到了该区块所允许的POW最大值。 我们已经知道，重定向算法试图校准目标值，以便每分钟找到10个工作量提交（平均）。（We already know, that the retargeting algorithm tries to calibrate the target value so that 10 proof-of-work submissions (on average) are found per minute.） 这是针对网络中所有的任务，不单单只是某一个。 但是，作为DOS的预防措施，每一个区块都有一个硬顶，最大只能有25个POW提交。 一旦达到这个数字，在下一个区块到来前，不会再接受其他的POW提交。

悬赏限制与上面相似。 在这种情况下，假如我们有这样一个任务，每次迭代都有一份悬赏，然后运行了三次迭代。 由于一次迭代对应着至少一个完整的区块，因此不需要为每个块提交多于一个的赏金。 所以，一旦发现了悬赏（在这种情况下，这可能更多地用于其他任务，注：此处应该为结果提交并拿到奖励），我们必须要等待到下一个区块中的迭代计数增加一个。

`并不需要您一直监测控制台的输出。 如果您启动了xeline后，您可以在左侧边栏看到一些基础的挖掘统计。`

现在，您已经掌握并可以准备开始挖掘了--不论是您自己为了测试或者换取更多的XEL。 （注：此文翻译内容诸多不确定，仅供参考）