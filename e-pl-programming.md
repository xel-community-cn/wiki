<!-- TITLE: ePL Programming -->

<!-- SUBTITLE: A quick summary of ePL Programming -->

## #ePL-编程篇

本教程是为零基础的软件程序员而设计的，帮助他们快速理解和应用ePL。 教程中将会有足够的知识帮助您加深对ePL的理解，方便您应用。

深入学习ePL之前，先让我们开始第一步。

* * *

## 安装要求

若要配置ePL的编程环境，你需要在电脑上先安装下面的两个软件工具。

* [1、Xel Miner](https://github.com/xel-software/Miner-Mainnet)

* 

[2、Xeline](https://github.com/xel-software/xeline)

* * *

## 一个简单的示例：寻找质数

我们看到的这个示例是用ePL语言编程的，它的功能是寻找一个介于10000-20000之间的质数。 虽然ePL语言还没有触及到现实世界，但这个很小的示例应用，可以帮助理解ePL语言是如何工作的。 首先，让我们看看整个代码，然后再逐行分析。

```java
array_uint 1000;

function main {
    verify();
} 

function primetest {
    u[1] = 0;
    if (u[2] <= 1)  u[1]=0;
    else if (u[2] <= 3) u[1]=0;
    else if (u[2]%2 == 0 || u[2]%3 == 0) u[1]=0;
    else {
        u[3]=5;
        u[1]=1;
        repeat(u[99],20000,20000){
        if (u[2]%u[3] == 0 || u[2]%(u[3]+2) == 0)
        { u[1]=0; break; }
        if(u[3]*u[3] > u[2]) break;
            u[3]+=6;
        }
    }
}

function verify {

    // make prime test
    u[2] = m[0];
    u[1]=0;
    if(m[0]>10000 && m[0]<20000) primetest();
    verify_bty ((m[0]>10000 && m[0]<20000) && (u[1]==1));

    // some randomness for POW function
    u[57] = m[1];
    u[56] = m[2];
    u[55] = m[3];
    u[54] = m[4];

    verify_pow (u[57], u[56], u[55], u[54]);
}
 
Context | Request Conte
```

ePL的编程通常先定义数组及数据类型，下面的代码展示了6种不同数据类型的数组的定义。

```java
nt i[] : signed ints (32 bit)
int u[] : unsigned ints (32 bit)
int l[] : signed longs (64 bit)
int ul[] : unsigned longs (64 bit)
int d[] : doubles
int f[] : floats
```

在您的ePL程序中需要在开头进行6个不同数据类型内存空间的分配，这是为了让VM知道所需要每类数组的元素数量及所需的内存空间大小。 在本示例中：

```java
array_int 0;
array_uint 1000;
array_long 0;
array_ulong 0;
array_double 0;
array_float 0;
```

元素为0的数组可以忽略掉，上面的示例代码已经展示过了。 数组的空间分配不需要由程序员来完成。 array_int 1000,已经定义并创建了数组I[]的元素为1000。 另外，需要注意的是，这里的变量都是全局变量，不是局部变量。 稍后，多一点耐心，您将发现这中设置并不会使ePL的编程变得困难。

ePL程序部分,你可以定义多个函数。 函数的格式需要依照下面的语法：

```java

function name {
    ...
}
```

函数没有参数也没有返回值。 不过，这并没由什么大问题，因为可以通过全局变量（之前定义的数组），来模拟参数和返回值，内部的数据将通过全局变量数组进行传递。 稍后，您就可以看到，您无需改变您的编码风格，也一样可以舒服且轻松地操作ePL语言。

在我们的示例中，我们定义了三个函数，分别是main()，verify()和test_prime()。 当然，您也可以定义更多的函数(在程序的复杂性和代码量的允许范围内)，不过，在您的程序中，必须要包含main，verify，和test_prime这三个函数。

之所以main和verify两个函数都将用到，是因为ePL语言允许进行不对称的计算验证。 为了更好地理解，其关键为节点是如何获取解决方案(结果)及网络的其余部分如何验证这些结果，以确保那些支付给XEL网络购买算力的科学家，得到的是他们想要的正确的结果。 两个函数的任务分配为当节点进行结果运算时，使用的时main函数，而对算法的结果的验证，使用的时verify()函数。

如此的分配，是又一个很好理由的：首先您可以进行复杂的程序编程，在主函数中执行更长时间的运算，同时，可以确保在节点上对运算结果只进行简单和轻量运算验证，而不会拖延太多的时间。 例如，这种不对称的运算和验证方式，在SAT求解器（SAT-SOLVER）中是可以派上用场的。 main()函数内将承载一个复杂的SAT求解逻辑，它的执行时间和算力需求会更高一些，而验证函数只是将结果分配给布尔公式并检查它是否计算为真。 如果您想查看如何完成此操作，可以随时查看我们的SAT求解示例。

> 重要警告:开发人员负责对main函数中的逻辑进行编码，并保证正确。 另外，verify内部的逻辑是正确无误，确保main函数的结果为真时，verify函数也能检测为真（稍后会有verify_bty逻辑的更多资料）。 开发人员的一个错误逻辑设置，也会导致结果通过验证，即便这个结果是错误的。 另外，更重要的是，如果没有通过校验函数的检测，主函数获取的有效结果被主网拒绝。 当然，这听起来有点抽象，在后面更深入了解教程后，这些（警告）会让您更直观的理解整个系统。 注意规避风险。

不管如何，就现在而言，就非常简单了：在我们的特定案例中，还没有必要使用不对称验证，因为找到这个质数和校验它，运算量相差无几。 为了使代码简洁易懂，因此，我们将整个的逻辑部分添加到verify()函数中，并让main()函数调用verify()函数，如下所示。

```java
function main {
    verify();
}
```

在我们开始前。 让我们先浏览下verify()函数。

```java
function verify {
    // make prime test
    u[2] = m[0];
    u[1]=0;
    if(m[0]>10000 && m[0]<20000) primetest();
    verify_bty ((m[0]>10000 && m[0]<20000) && (u[1]==1));

    // some randomness for POW function
    u[57] = m[1];
    u[56] = m[2];
    u[55] = m[3];
    u[54] = m[4];

    verify_pow (u[57], u[56], u[55], u[54]);
}
```

如何将随机性引入代码的执行中，对于您理解这些代码是很重要的。 ePL包含一个附加的数组m[12]，内部包含12个无符号整数型变量。 其中，前10个变量是伪随机的，组合后可以为您提供320位的可操作数（熵）。 第11个变量m[10]包含了迭代次数，第12个变量m[11]是当前的迭代数值。 迭代的概念会在后面的篇章讲解。

为了更好的理解这里的变化，数组m[12]的填充，可以参考C语言的内部逻辑。

```java
// hash32[] is a random hash value which is provided by the XEL protocol
// mult32[] contains a whole bunch of other information such as round and iteration #

// Randomize Inputs m[0]-m[9]
for (i = 0; i < 10; i++) { work->vm_input[i] = swap32(hash32[i % 4]);
  if (i > 4)
    work->vm_input[i] = work-> vm_input[i] ^ work->vm_input[i - 3];
}

// Set m[10] To Round Number
work->vm_input[10] = mult32[1];

// Set Inputs m[11] To Iteration Number
work->vm_input[11] = mult32[2];
```

在我们的示例中，我们将选用第一个变量m[0]用于存放质数的初始值（候选值）。 当然，您也可以选择整个的320位来为您的算法创建初始值（候选值）。 如果您的需求超过了320位，您也可以从中提取一个无限大的伪随机数流。

```java
u[2] = m[0];
u[1]=0;
```

上面的代码，表示我们将数组中的第一个元素赋值给我们之前定义过的数组变量u[0]，数组u[]，我们在程序开端就定义过了，包含1000个无符号整数型元素。 另外，我们初始化变量u[1]，并赋值为0，这里用于存放后面质数校验后的结果。

```java
if(m[0]>10000&&m[0]<20000) primetest();
```

上面的代码表示我们调用质数校验的函数。 由于我们设定的是只对10000-20000之间的质数感兴趣，所以，我们只需要调用这个函数，判断变量m[0]的值是否符合。

```java
verify_bty ((m[0]>10000&&m[0]<20000)&& (u[1]==1));
```

上面的代码是告诉后端系统您的任务/挑战想要的是什么解决方案(结果)-或者XEL赏金被触发调用的条件。 verify_bty()函数使用一个布尔表达式作为函数的参数，如果发现有适合的结果，该表达式为真。 在这个示例中，这样处理简单直接：如果m[0]的数据在10000-20000之间，m[1]将设置为1，该数值为一个有效的结果。

在后面的篇章，我们需要详细地讲解（主网中）工作量证明结算的情况。 XEL的主网中，每个任务都有两种类型的结算方式：工作量证明结算和悬赏结算。 之所以这样，是由一个非常简单的原因：通常，赏金的获取是很困难的，且需要全力以赴的工作。 假设，您想要获取一个CNF公式的输入值，将其评估为真:不过，这取决于您公式的复杂度，如果是一个双射关系，那么您将很难得到想要的数据，因为（运算后）得到的结果只有一个。 对结果/解决方案的发现者应该提供具有吸引力的XEL奖励（多来多得），但是，我们也更希望通过高频小额的结算，来激发参与者的活力。 所以，XEL引入了工作量证明这个概念来实现这一点。 参与者提交得一些结果/解决方案也许并不能用于解决您得问题，但是，同样也可以获取一些小额的XEL，使他们能够持续的参与结果/解决方案的提交（此处为工作量证明结算的解释，译者注）。 可以预见，悬赏结算的金额会被设置的明显高于工作量证明。 但是， 如果您将悬赏结算的金额设置的很小，如此，主网上其他的任务会显得更有利可图，那么您将吸引不到什么算力。 顺便说一些，在重定位机制顶部工作的工作量包，与比特币种的块难度重定位类似：网络的处理能力越高，找到这样的工作证明结果的难度越大，平均而言，整个网络中的工作证明包的数量当前被设置为10分钟一次。

> 需要指出的是，为verify_pow()函数提供足够的“证据”是开发人员的责任，这些证据表明参与者已经处理了您的问题，证据存放于如前文说过的4个无符号整数型变量中。 特别提示。

在我们的示例中，我们是这样定义的：

```java
// some randomness for POW function
u[57] = m[1];
u[56] = m[2];
u[55] = m[3];
u[54] = m[4];

verify_pow (u[57], u[56], u[55], u[54]);
```

当前，虽然它对我们的演示是非常适用的，但是，这在实际的项目应用中，却是一种不好的方式。 如上代码，我们选定了4个整数型变量，用于指定工作量结算中需要用到的已完成标志。 在这种情况下，对那些只想提供工作量运算但对赏金不感兴趣的参与者，可以省略整个程序逻辑，且只需要检测m[1]即可。 m[4]用于查看他们是否找到了结果/解决方案（POW）。 这就是他们有一个巨大的优势，及时对您的任务没有什么贡献，也能获取一些赏金（POW的小额奖赏，译者注）。 这4个变量的值，大部分的程序逻辑，将依赖于这些值分别进行“某种方式”执行。 然后，只有那些运行整个代码的人才能获取赏金结算的奖励。 即便是这些参与中并不发布什么任务悬赏，当然，也没有什么理由来要求他们，只要完成了任务，那么他们就将获取任务对应的奖金。

在最后一步，我们来看看实际的质数校验。

```java
function primetest {
    u[1] = 0;
    if (u[2] <= 1)  u[1]=0;
    else if (u[2] <= 3) u[1]=0;
    else if (u[2]%2 == 0 || u[2]%3 == 0) u[1]=0;
    else {
        u[3]=5;
        u[1]=1;
        repeat(u[99],20000,20000){
        if (u[2]%u[3] == 0 || u[2]%(u[3]+2) == 0)
        { u[1]=0; break; }
        if(u[3]*u[3] > u[2]) break;
            u[3]+=6;
        }
    }
}
```

上面这些您看到的代码，是一种简单的预筛选算法用于质数的检测，其中m[2]作为输入值（m[2]内部为前文已经储存过的质数待选值），如果值为质数，则设置m[1]=1，如果不是，则设置m[1]=0。 这些值可以视作函数的返回值(用的全局变量，前文有讲，实际函数无返回值，译者注)。

另外您可以注意到了，其中有一个很奇怪，且之前没见过的循环结构：

```java
repeat(u[99],20000,20000){
...
}
```

在深入研究ePL循环的特性之前，我们先了解下是什么使得这些循环如此的怪异。 如之前所言，我们希望的是一种可编程语言，它需提前知道何时停止代码的运行，以及任务将使用多少的内存，来避免那些（做恶者）提交没有终止循环的任务，发起的各种DOS攻击。 那么，我们为什么没有用传统的循环语法来完成呢? 因为，传统的循环语法存在所谓的停机问题（halting-problem）。 这个停机问题是程序的不确定性，程序是否终止还是一直不停的死循环下去。

当然，我们也可以用这样的C语言的循环结构：

```java
for(int i=0; i<1000: ++i){
   ...
}
```

但是，对于下面这个简单的程序结构，就显得不太适合了，如：

```java
int entropy = rand();
for(int i=0; i<1000) ++i;
   ...
}
```

这里，上面的for循环的终止条件，就取决于entropy的值。 当然，在这个简单的示例中，我们是可以看到，这个代码运行后会发生什么。 如果entropy<10000，那么这个循环就将结束了。 如果10000<=entropy<=50000，那么这个循环将进行1000次，如果entropy>50000，那么这个循环将进行500次。 对于一般情况，还没有有效的办法（准确地）通过算法判断C程序是否终止，已经何时终止。

为了消除这样的问题，ePL语言引入了一个新的循环语法：

```java
repeat(index,iterations,max_iterations){
...
}
```

其中，index参数为数组槽，iterations是可以是一个常数或者一个您选定的数组槽，max_iterations参数为最大的迭代次数，当到达这个值后，循环退出。 在编译器内部，经转换为C语言，循环格式转变如下:

```java
repeat(u[99],20000,20000){
...
}
```

转换为：

```java
<br />int counter = 0; // immutable by user's code
u[99]=0;
for(int i=0; i<20000; ++i){
    ...
    counter++;
    u[99] = counter;
    if(counter==20000) break;
}
```

循环中的局部变量counter不能由ePL代码本身更改，当技术达到您设定的最大迭代次数后，无论您的代码在执行什么，循环都将会终止退出。 当解析并检查您代码的复杂度是否在特定范围内时，将假定循环只按照您设定的最大迭代数运行。 在这里，一定要确保设定一个足够大的最大迭代数，否则，循环将过早的终止退出。

至此，通过以上的了解，基本上您已经可以编写您的第一个ePL程序了。 现在，让我们测试下我们的示例。

* * *

## 编译与测试

为了测试程序语法的正确性，您可以使用xel_miner。 假设您的程序文件名为find_prime.epl，可以使用如下命令。

```python
./xel_miner --validate --test-avoidcache --test-vm find_prime.epl
```

执行代码的第一次运行。 其中，--validate标志用于指示xel_miner查找使用在任何时候都不能保证为零的未初始化的变量。 如果您想避免这样的处理。 此外，如果程序没有通过这些检测，主网将不会接收这个程序。 另外--test-vm标志用于指示xel_miner不启动挖矿，而是在您的计算机上运行本地的ePL文件（并检查其准确性）。

如果一切顺利，您将看到如下输出：

```text
** Elastic Compute Engine **
   Miner Version: 0.9.6
   ElasticPL Version: 0.9.4
[16:31:29] DEBUG: Skipping recompilation to be blazing fast
[16:31:29] DEBUG: TestVM: block id '123456789'
[16:31:29] DEBUG: TestVM: work id '987654321'
[16:31:29] DEBUG: WCET main tester status: OFF
[16:31:29] DEBUG: WCET verify tester status: OFF
[16:31:29] DEBUG: TestVM: multiplicator '0000......0000'
[16:31:29] DEBUG: TestVM: pubkey '0000......0000'
[16:31:29] DEBUG: TestVM: target '0FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
[16:31:29] DEBUG: storage size: 0
[16:31:29] DEBUG: Library 'job_987654321' Loaded
[16:31:29] DEBUG: Bounty Found: false
[16:31:29] DEBUG: POW Found: false
[16:31:29] DEBUG: POW Hash: 4E95CBA872172C0596BE7CB482703EE4
[16:31:29] DEBUG: Compiler Test Complete
[16:31:29] Exiting xel_miner
[16:31:29] Cleaning up
```

如上则待变一切顺利，编译没有反馈任何错误，这意味着您代码在语法上时正确的-但没有反馈更多信息关于它是否正在做您想要做的事情；这里我们后期会再做测试。

但首先，您可能更感兴趣的时您的程序编译成C语言的样子。您可以通过运行来查看。

```text
cat work/job_987654321.h
```

输出应该是这样的：

```java
void main_987654321(uint32_t *, uint32_t, uint32_t *, uint32_t *, uint32_t *);
void primetest_987654321();
void verify_987654321(uint32_t *, uint32_t, uint32_t *, uint32_t *, uint32_t *);

void main_987654321(uint32_t *bounty_found, uint32_t verify_pow, uint32_t *pow_found, uint32_t *target, uint32_t *hash) {
    verify_987654321(bounty_found, verify_pow, pow_found, target, hash);
}

void primetest_987654321() {
    u[1] = 0;
    if ((u[2]) <= (1)) {
        u[1] = 0;
    }
    else {
        if ((u[2]) <= (3)) {
            u[1] = 0;
        }
        else {
            if ((((((2) != 0) ? (u[2]) % (2) : 0)) == (0)) || (((((3) != 0) ? (u[2]) % (3) : 0)) == (0))) {
                u[1] = 0;
            }
            else {
                u[3] = 5;
                u[1] = 1;
                int loop87;
                for (loop87 = 0; loop87 < (20000); loop87++) { if (loop87>= 20000) break;
                    u[99] = loop87;
                    if ((((((u[3]) != 0) ? (u[2]) % (u[3]) : 0)) == (0)) || ((((((u[3]) + (2)) != 0) ? (u[2]) % ((u[3]) + (2)) : 0)) == (0))) {
                        u[1] = 0;
                        break;
                    }
                    if (((u[3]) * (u[3])) >(u[2])) {
                        break;
                    }
                    u[3] += 6;
                }
            }
        }
    }
}

void verify_987654321(uint32_t *bounty_found, uint32_t verify_pow, uint32_t *pow_found, uint32_t *target, uint32_t *hash) {
    u[2] = m[0];
    u[1] = 0;
    if (((m[0])> (10000))&&((m[0])<(20000))) { primetest_987654321(); } *bounty_found = (uint32_t)((((m[0]) >(10000))&&((m[0])< (20000))) &&((u[1]) == (1)) != 0 ?
 
Context | Request Context 1 : 0);
    u[57] = m[1];
    u[56] = m[2];
    u[55] = m[3];
    u[54] = m[4];
    if (verify_pow == 1)
        *pow_found = check_pow(u[57],u[56],u[55],u[54], &m[0], &target[0], &hash[0]);
    else
        *pow_found = 0;
}
```

在这里，我们不再详细的解读每一行的代码。 相反的，我们希望能像您展示您的ePL程序如何转换成C程序，帮助您理解您的ePL程序最终转换的代码情况，这在您之后项目开发遇到问题时，可以从容应对。 现在，我们刚刚验证了您代码在语法上是正确的，但不能保证您代码是否能够获取想要的正确结果。

您可以尝试通过调用xel_miner进行（挖矿）运算测试，代码如下（重点：其中-test-avoidvache应一直使用，用于禁止缓存，否则会导致测试过程中出现问题）：

```java
./xel_miner --test-avoidcache --test-cont-bounty --test-vm prime.epl
```

根据待解问题的复杂度，您需要等待一段时间，直到获取第一个反馈。 基于测试的目的，建议您减少verify_bty(...)函数中的参数值，在后期的调用中可以更快的获取结果。 在我们的示例中，结果应该很快显示出来，如下:

```text
** Elastic Compute Engine **
   Miner Version: 0.9.6
   ElasticPL Version: 0.9.4
[20:08:09] DEBUG: Skipping recompilation to be blazing fast
[20:08:09] DEBUG: TestVM: block id '123456789'
[20:08:09] DEBUG: TestVM: work id '987654321'
[20:08:09] DEBUG: WCET main tester status: OFF
[20:08:09] DEBUG: WCET verify tester status: OFF
[20:08:09] DEBUG: TestVM: multiplicator '0000....0000'
[20:08:09] DEBUG: TestVM: pubkey '0000....0000'
[20:08:09] DEBUG: TestVM: target '0FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF'
[20:08:09] DEBUG: storage size: 0
[20:08:09] DEBUG: Library 'job_987654321' Loaded
[20:08:09]>>STARTING CONTINUOUS TEST, THIS MAY TAKE VERY LONG DEPENDING ON YOUR PROBLEM &<<;
[20:08:17] ran 5000000 iterations, still working ...
[20:08:18] ********************************
[20:08:18] FOUND A SOLUTION TO YOUR PROBLEM
[20:08:18]  we will dump all 12 input ints
[20:08:18] ********************************
[20:08:18] m[0] =   18503
[20:08:18] m[1] =   3294169169
[20:08:18] m[2] =   2082860469
[20:08:18] m[3] =   2676999779
[20:08:18] m[4] =   18503
[20:08:18] m[5] =   3095193060
[20:08:18] m[6] =   3819580374
[20:08:18] m[7] =   2676985380
[20:08:18] m[8] =   3095178659
[20:08:18] m[9] =   670245767
[20:08:18] m[10]    =   0
[20:08:18] m[11]    =   1
[20:08:18] DEBUG: Bounty Found: true
[20:08:18] DEBUG: POW Found: false
[20:08:18] DEBUG: POW Hash: 6F71E050C9A3B5A4B8A5686CB47EB17E
[20:08:18] DEBUG: Compiler Test Complete
[20:08:18] Exiting xel_miner
[20:08:18] Cleaning up
 
Context | Request Context
```

从这行开始：

```text
[20:08:18] DEBUG: Bounty Found: true
```

您将看到，这里确定已经发现了一个赏金奖励（结合上文的赏金结算标志，译者注）。 由于我们使用了一个简单的方式来定义待解问题的候选解决方案（结果），因此您将可以立即看到结果：

```text
[20:08:18] m[0] =   18503
```

查看结果值，它是介于10000-20000之间的质数。

好吧，简单的示例之后，您是否已经准备好使用我们的XEL网络，运行您的第一个ePL程序。