<!-- TITLE: ePL Advanced Tactics -->

<!-- SUBTITLE: A quick summary of ePL Advanced Tactics -->

## #ePL-进阶版

## 存储和迭代

通过下文，我们将了解下ePL的存储和迭代的工作机制。 不过在学习之前，我们强烈建议您能够先查阅理解下ePL编码教程和Xeline的使用教程。 在这些文档教程中，我们会提供一些例程。

## 我们的初始示例有什么问题？

我们的初始示例只是寻找一个介于10000到20000的质数。 但是，到目前为止，还没有办法重新使用其他节点提交的结果来改进它们。 这样的操作对于遗传算法的任务是必需的。 遗传算法通常使用类似于选择和变异等这样的自然进化过程（模式），以不停探索识别的方式来获取结果。 针对那些较大搜索空间的问题，然后进入到多代（或迭代，我们通常讲的）。 在每代的结果中，选取改进过的上一代的优良后代，使得算法最后可以获取某个最优值。

这需要在在连续迭代/繁殖之间进行某种存档和同步。 我们将使用简单的教程，方便大家更好的理解。 在本教程中，我们希望能拓展我们的质数示例，以便能够在多次迭代中找到多个质数，且每个质数都小于之前迭代中发现的质数。

为此，我们将示例进行了调整，如下：

```java
// metadata(title:My personal PRIME demo)
// metadata(pow_limit:1000)
// metadata(pow_price:0.2)
// metadata(bounty_limit:1)
// metadata(bounty_price:5)
// metadata(iterations:3)
// metadata(timeout:250)
// metadata(callback:my_own_prime_demo_callback.js)

array_uint 1000;

submit_sz 1;
submit_idx 100;

function main { verify();} 
function primetest {
    u[1] = 0;
    if (u[2] <= 1)  u[1]=0;
    else if (u[2] <= 3) u[1]=0;
    else if (u[2]%2 == 0 || u[2]%3 == 0) u[1]=0;
    else { 
       u[3]=5; u[1]=1;
       repeat(u[99],20000,20000){
          if (u[2]%u[3] == 0 || u[2]%(u[3]+2) == 0){ u[1]=0; break; }
          if(u[3]*u[3] > u[2]) break;
                  u[3]+=6;
       }
    }
}

function verify {

    // make prime test
    u[2] = m[0];
    u[1]=0;

    // take over a possible prime stored in s[] storage_idx
    u[4]=s[0];
    u[100]=u[2];


    if(m[0]>10000 && m[0]<20000 && (u[4]==0 || m[0]<u[4])) primetest(); // Bounty Is Rewarded m[0] is larger than 3294967295 and prime
    verify_bty ((m[0]>10000 && m[0]<20000) && (u[1]==1));

    // some randomness for POW function
    u[57] = m[1];
    u[56] = m[2];
    u[55] = m[3];
    u[54] = m[4];

    verify_pow (u[57], u[56], u[55], u[54]);
}
```

现在，让我们快速浏览下这些调整的区别。

```java
// metadata(iterations:3)
```

这行代码在Xeline中，表示我们现在希望三次迭代，而不止一次。 在本示例中，解决方案的数据量（每次迭代）都被保留为一个。

```java
submit_sz 1;
submit_idx 100;
```

这行代码是我们定义储存的大小，为一个无符号整数型。 当找到解决方案后，我们希望矿工能够上传submit_sz和无符号整数型变量u[submit_idx]=u[100].

位于

```java
// take over a possible prime stored in s[] storage_idx
u[4]=s[0];
u[100]=u[2]; // prepare the slot for upload
```

我们初始化了一个数组u，其中u[4]用于存放第一个和唯一的变量s[0]. 这听起来有点混乱，所有，再重复一次。 当找到解决方案后，挖矿软件将u[submit_idx]的值更新到无符号整数型变量submit_sz。 在其他矿工接收到任务时，先前已经提交（注：抢占任务的变量）将被存储的一个数组s[]中，该数组的大小与submit_sz一样（uint型）。 在我们的例子中 , 这意味着 ` u <div class=notranslate> 0 </div> ` 中的值 ( 在上面的代码剪下的质数初始化 ) 将与每个解决方案一起提交 , 并向到 ` s <div class=notranslate> 1 </div> ` 中连续迭代。 当一次迭代获取多个解决方案（结果）时，则会将其中一个值随机储存到s[0]。 然而，这个简单的示例没有多结果的情况出现。

这些行代码也可以修改为：

```java
if(m[0]>10000 && m[0]<20000 && (u[4]==0 || m[0]<u[4])) primetest();
```

在第一次迭代中，变量的值为0，在此后连续的迭代中，变量储存上一代的值。 这两行的基本功能是在没有存储的情况下运行素数测试，或者问题的质数小于上一次迭代中变量储存的数。

现在，我们唯一要考虑的事是如何在回调中使用。 由于我们有三个悬赏，我们可以遍历悬赏数组中的第一维(如前面的教程中所述)。

```java
var txt = (bounties[2][0]>>>0)+", " +(bounties[1][0]>>>0);
txt +=  ", " +  (bounties[0][0]>>>0);
console.log("Second demo (three primes): " + txt);
```

当Xeline运行此操作后，您将看到此结果。

你会发现三个素数已被发现，通过使用储存和迭代，每个素数都比前一代小。 有了以上内容的了解，您现在已经可以开始为XEL编程多重迭代的任务了。

现在，我们可以进行使用挖矿部分的学习了（miner software）。