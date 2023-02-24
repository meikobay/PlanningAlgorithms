# PlanningAlgorithms
---
CSDN专栏|[自动驾驶规划入门（已完结）](https://blog.csdn.net/u013468614/category_11897666.html)
---
> “他们利用各种办法包围成群的野马，逼迫马群向山顶上跑，野马跑到悬崖边收不住脚，就成群地跌下悬崖，跌得血肉狼藉，这样，人们就获得了大量的野马。”上面这段文字描述了原始人打猎的场景。我们只需要稍加思索便能发现以上描述背后藏着原始人精妙的计划。智能体（人类或某些其他生物）从空间的变化中抽象出“时间”，有了时间观念后，便同时拥有了前瞻（计划未来）后瞩（回忆过去）的能力，其中，回忆过去也是为了更好的预测与计划未来。
> 
![在这里插入图片描述](https://img-blog.csdnimg.cn/bc72a9fffe234251a276b2b6ce22e20a.png#pic_center)
## 一、什么是规划
当你被师长问及新的一年你有什么计划时，当你被面试官问及你的职业规划时，你的回答是名词性质的规划（<font color='green'>plan</font>）——一份制定好的计划、策划书或方案。

当你面对一个任务时，思索得到解决方案的过程则是动词的规划(<font color='blue'>planning</font>)——制定、拟定一份计划或方案的整个过程。

相对于作为产出物的名词性质的规划，我们更加关注作为一种能力的规划过程，也即动词性质的规划。博客中所述的规划则默认为是动词性质的规划，而用“规划结果”表示名词性质的规划。
![在这里插入图片描述](https://img-blog.csdnimg.cn/cade35a3a5ac40879b268536a54dec0b.png#pic_center)
## 二、自主移动机器人领域中的规划
在不同的研究领域，规划的具体内容与内涵都有区别。而我们本专栏只针对机器人领域的规划理论与方法进行介绍。

在自主移动机器人领域，一个最基本的任务是：将人类的高阶任务转换成指示机器人如何移动的低阶描述。例如，自动驾驶任务就是将乘客或货物安全、省时、节能地从起点运送到目的地。运动规划(motion planning)、路径规划(path planning)与轨迹规划(trajectory planning)便是用于解决这类任务。
![在这里插入图片描述](https://img-blog.csdnimg.cn/55ca9348774e4369bfbfae61ba9ff1b6.png#pic_center)

《Planning Algorithms》[^1]中有对运动规划与轨迹规划的描述，现引出如下：

>**运动规划**：Robot motion planning usually ignores dynamics and other differential constraints and focuses primarily on the translations androtations required to move the piano. Recent work, however, does consider other aspects, such as uncertainties, differential constraints, modeling errors, and optimality.
**轨迹规划**：Trajectory planning usually refers to the problem of taking the solution from a robot motion planning algorithm and determining how to move along the solution in a way that respects the mechanical limitations of the robot.

书中的意思是轨迹规划是运动规划的下层(如下图所示)，运动规划的结果通常忽略动力学与运动学约束，得到机器人（或机械臂）完成任务过程所需要的位置与姿态序列。然而，轨迹规划则需要考虑更加细致动力学与运动学约束，使机器人（或机械臂）能够按照运动规划的位姿序列移动以完成任务。
![gd ddddddd](https://img-blog.csdnimg.cn/16e391a609ac46d4be33c6bceba01f0d.png#pic_center)

但是，书中在描述运动规划的最后加了一句：最近，不少运动规划方法开始考虑不确定性、微分约束（例如：一阶微分——速度、二阶微分——加速度等等）、建模误差以及最优性等其它方面。在这种情况下，运动规划与轨迹规划所做的事情就变得一样了。此时，轨迹规划则是被运动规划所包含：

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbc9ab1d7a9d4e5c9bd7996e2ccb494f.png#pic_center)
渐渐地，运动规划有了小弟——轨迹规划去描述更细的规划任务，那么运动规划中除了轨迹规划还剩一块空白仍需要用运动规划去描述，这时候我们就会觉得有点怪，因为运动规划的描述域时而大时而小容易让人困惑。此时，路径规划就出现用来替代最初运动规划的位置，而运动规划则完全抽身到更高的概念。
![在这里插入图片描述](https://img-blog.csdnimg.cn/10aa1e01649b45c1933d7121bdafa028.png#pic_center)
为了统一说法，本人延用如下定义：

- **路径规划**（path planning）：就是在给定位置A与位置B之间为机器人找到一条符合约束条件的路径，这个约束可以是碰撞、路径最短、能耗最小等；
- **轨迹规划**（trajectory planning）以路径规划的结果作为参考路径，给出使机器人从位置A到达位置B，且满足机器人运动学与动力学约束时空轨迹；
- **运动规划**（motion planning）由上层路径规划和下层轨迹规划组成。

> **决策与规划的关系**：在心理学与传统人工智能领域，广义的决策是包含规划的，而自动驾驶领域里的行为决策（我们称之为狭义的决策）则是作为作为规划的前提，为规划提供依据与目标，规划则是对决策的具体落实与展开。还有一种说法可供参考：<font color='red'>决策把规划问题约束成一个凸问题。</font>

## 三、自主移动机器人领域中的规划算法
本专栏中会循序渐进地介绍自主移动机器人领域的经典规划算法，每篇博客会从背景、原理、示例代码演示、重要知识点、总结等五个方面对一个规划算法进行全方位的介绍。

要有序的对如此大量的规划算法进行介绍，对其进行分类是首要的事情。

>第二小节根据任务对规划进行分层是一种对规划方法进行分类的方式，但是无益于我们去了解众多的规划算法之间的本质异同。因为，<font color='red'>**同一类型的规划算法，按这样的目标、约束以及成本设计可以用于路径规划，按另外一种设计便可能可以用于轨迹规划了。**</font>

本专栏的规划部分（本专栏还有一小部分是关于自主移动机器人控制）对规划算法按下图进行分类，并依据此图有序前进。根据下图所示，自主移动机器人规划算法主要包括图搜索、采样、优化等三大类方法。本专栏也会介绍一些占比较小部分的规划方法，如基于势场函数的方法、生物启发式类方法以及学习类方法。本专栏最后还对自主移动机器人规划算法中依赖的各种类型的运动基元开设专题对之进行介绍。


![在这里插入图片描述](https://img-blog.csdnimg.cn/193485cb3ba14532a76bfefef07b1d7e.png#pic_center)




学习完本专栏自主移动机器人规划部分，你应该：
 
 - 更加深刻地体会到规划是什么；
 - 熟悉各类自主移动机器人规划算法的核心思想、原理、优缺点；
 - 明白各类规划算法之间的本质异同；
 - 能够动手实现各规划算法；
 - 可以搭建一套自主移动机器人规划系统。

## 四、已经完成章节
### 4.1 基于图搜索的规划算法
- [基于图搜索的规划算法之Dijkstra](https://windses.blog.csdn.net/article/details/125882452)
- [基于图搜索的规划算法之 A* 家族（一）：A* 算法](https://windses.blog.csdn.net/article/details/126024013)
- [基于图搜索的规划算法之 A* 家族（二）：双向 A* 算法](https://blog.csdn.net/u013468614/article/details/126085190?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之 A* 家族（三）：Anytime A* 算法](https://windses.blog.csdn.net/article/details/126196424)
- [基于图搜索的规划算法之 A* 家族（四）：Lifelong Planning A* 算法](https://windses.blog.csdn.net/article/details/126344705)
- [基于图搜索的规划算法之 A* 家族（五）：D* 算法](https://windses.blog.csdn.net/article/details/126356080)
- [基于图搜索的规划算法之 A* 家族（六）：D* Lite 算法](https://blog.csdn.net/u013468614/article/details/126578060?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之 A* 家族（七）：Field D* 算法](https://windses.blog.csdn.net/article/details/126652932)
- [基于图搜索的规划算法之 A* 家族（八）：Theta* 算法](https://blog.csdn.net/u013468614/article/details/126860201?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之 A* 家族（九）：Hybrid A* 算法](https://blog.csdn.net/u013468614/article/details/127040519?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之 A* 家族（十）：时空 A* 算法](https://blog.csdn.net/u013468614/article/details/127178132?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之 A* 家族（十一）：总结](https://blog.csdn.net/u013468614/article/details/127272352?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之可视图法](https://blog.csdn.net/u013468614/article/details/127379218?spm=1001.2014.3001.5501)
- [基于图搜索的规划算法之Voronoi图法](https://windses.blog.csdn.net/article/details/127454489?spm=1001.2014.3001.5502)
### 4.2 基于采样的规划算法
- [基于采样的规划算法之概率路图（PRM）法](https://windses.blog.csdn.net/article/details/127643442)
- [基于采样的规划算法之RRT家族（一）：快速探索随机树（RRT）](https://windses.blog.csdn.net/article/details/127716172)
- [基于采样的规划算法之RRT家族（二）：RRT-Connect](https://windses.blog.csdn.net/article/details/127797445)
- [基于采样的规划算法之RRT家族（三）：RRT*](https://windses.blog.csdn.net/article/details/128058386)
- [基于采样的规划算法之RRT家族（四）：Informed RRT*](https://windses.blog.csdn.net/article/details/128227660)
- [基于采样的规划算法之RRT家族（五）：时空 RRT](https://windses.blog.csdn.net/article/details/128309038)
- [基于采样的规划算法之RRT家族（六）：总结](https://windses.blog.csdn.net/article/details/128413984)
- [基于采样的规划算法之动态规划方法](https://windses.blog.csdn.net/article/details/128515996)
- [基于采样的规划算法之动态窗口法（DWA）](https://windses.blog.csdn.net/article/details/128585386)

### 4.3 [基于连续优化的规划算法：以二次规划为例](https://windses.blog.csdn.net/article/details/128679625)
### 4.4 运动基元
- [运动基元（一）：Dubin‘s曲线【part1】](https://windses.blog.csdn.net/article/details/128749754)
- [运动基元（一）：Dubin‘s曲线【part2】](https://windses.blog.csdn.net/article/details/128758944)
- [运动基元（一）：Dubin‘s曲线【part3】](https://windses.blog.csdn.net/article/details/128766465)
- [运动基元（二）：贝塞尔曲线](https://windses.blog.csdn.net/article/details/128877012)
- [运动基元（三）：多项式曲线](https://windses.blog.csdn.net/article/details/129037343)


## 结语
人类在生活与工作中，规划无处不在。我们规划从家到公司的路线，我们有时也会规划从当前到未来的人生路线。自主移动机器人的终极目标就是替代人类的外出/出行任务：从起点如何高效、安全（有时还需要考虑舒适性）地到达目的地。在给出合适的目标、约束以及成本函数后，规划算法能够使移动机器人像人类一样给出一份出行计划（很多时候是一个由粗到精，由远及近、由静到动的多层规划结果）。

如果说使用和创造工具等是智能体智慧的主要表现形式，那么规划（计划）便是以智慧为音符在空间上写就的与时间相关的乐章（这个时间不一定是指规划的每一步包含显式的时间节点，规划步骤的先后同样也是一种时间的概念）。请将这作为阅读本专栏时的旁白，时时朗诵。

## 参考文献
[^1]:Lavalle S M . Planning Algorithms[M]. Cambridge University Press, 2006.
