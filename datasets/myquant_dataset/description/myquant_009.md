# 网格交易(期货)

分享

阅读 174440
 更新 2022-05-09 16:56:48

* [网格交易法（期货）](#a3a8216fc187c669)
  + [1. 原理](#4d68e06c45acd2a2)
    - [什么是网格交易法？](#0826fd0b2e938ab7)
    - [怎样设计网格？](#148bf6919cad712c)
    - [网格交易法的盈利情况](#e9beafd820a1ba4a)
    - [核心](#67477ceb50f7c2e9)
  + [2. 策略思路](#cf0127fe1bcb3311)
    - [策略难点：](#4a606bba6ea7c206)
  + [3. 策略代码](#69a3fc437f0227e3)
  + [4. 回测结果和稳健性分析](#e8b0a560bf110088)

# 网格交易法（期货）

## 1. 原理

### 什么是网格交易法？

网格交易法是一种利用行情震荡进行获利的策略。在标的价格不断震荡的过程中，对标的价格绘制网格，在市场价格触碰到某个网格线时进行加减仓操作尽可能获利。

网格交易法属于左侧交易的一种。与右侧交易不同，网格交易法并非跟随行情，追涨杀跌，而是逆势而为，在价格下跌时买入，价格上涨时卖出。

### 怎样设计网格？

投资者可以随意设置网格的宽度和数量。既可以设置为等宽度，也可以设置为不等宽度的。设置等宽度网格可能会导致买点卖点过早，收益率较低。设置不等宽度网格能够避免这个问题，但如果行情出现不利变动，可能会错失买卖机会。

### 网格交易法的盈利情况

***在行情震荡上涨时：***  
![](/uploads/202010/attach_1642640f8e2ec613.png)

假设格子之间的差为1元钱，每变化一个格子相应的买入或卖出1手，则通过网格交易当前账户的净收益为6元，持空仓4手，持仓均价为12.5元。

***行情震荡下跌时：***  
![](/uploads/202010/attach_164264143d608515.png)

同理可知，净收益为8元，持4手多仓，平均成本为7.5元。

可以看到，无论行情上涨还是下跌，已平仓的部分均为正收益，未平仓的部分需要等下一个信号出现再触发交易。

即使网格交易能够获得较为稳定的收益，但也存在一定的风险。如果行情呈现大涨或大跌趋势，会导致不断开仓，增加风险敞口。这也是为什么网格交易更适用震荡行情，不合适趋势性行情。

### 核心

网格交易主要包括以下几个核心要点：

**- 挑选的标的最好是价格变化较大，交易较为活跃**  
网格交易是基于行情震荡进行获利的策略，如果标的不活跃，价格波动不大，很难触发交易。  
**- 选出网格的压力位和阻力位**  
确定适当的压力位和阻力位，使价格大部分时间能够在压力位和阻力位之间波动。如果压力位和阻力位设置范围过大，会导致难以触发交易；如果压力位和阻力位设置范围过小，则会频繁触发交易。  
**- 设置网格的宽度和数量**  
设定多少个网格以及网格的宽度可根据投资者自身喜好自行确定。

## 2. 策略思路

第一步：确定价格中枢、压力位和阻力位  
第二步：确定网格的数量和间隔  
第三步：当价格触碰到网格线时，若高于买入价，则每上升一格卖出m手；若低于买入价，则每下跌一格买入m手。

回测标的：SHFE.rb1901  
回测时间：2018-07-01 到 2018-10-01  
回测初始资金：10万  
**注意：若修改回测期，需要修改对应的回测标的。**

### 策略难点：

* 怎样记录价格是否突破网格线？

解决方法：有些人可能会想到用当前价格与网格线对应的价格进行比较，但这样操作比较麻烦，步骤繁琐。这里采用区域判断方式。根据网格线划分网格区域为1、2、3、4、5、6.利用pandas库提供的cut函数，将当前价格所处的网格区域表示出来。当网格区域发生变化，说明价格突破了一个网格线。

* 如何避免出现4区-5区开仓一次,5区-4区又平仓一次这种“假突破”？

解决方法：4-5开仓一次和5-4平仓一次实际上突破的是一根线，此时的形态是价格沿着这根线上下波动。只有第一次穿过这条线时才是真正的交易信号，其他的并没有形成突破。因此我们需要一个变量储存每一次交易时网格区域的变化形态(按照从大到小的顺序)，比如5-4可以记为[4,5],4-5记为[4,5]。当新的记录=旧的记录时，信号失效。

## 3. 策略代码

```