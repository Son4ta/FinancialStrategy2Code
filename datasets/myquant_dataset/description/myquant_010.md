# 指数增强(股票)

分享

阅读 94773
 更新 2022-05-09 16:56:48

* [指数增强策略](#042069c643f068af)
  + [1. 策略原理](#c4a1bed1475f07da)
  + [2. 策略步骤](#c306d1ad1c8a5699)
  + [3. 策略代码](#69a3fc437f0227e3)
  + [4. 回测结果和稳健性分析](#e8b0a560bf110088)

# 指数增强策略

## 1. 策略原理

说到指数增强，就不得不说指数。

在进行股票投资时，有一种分类方式是将投资分为**主动型投资**和**被动型投资**。被动型投资是指完全复制指数，跟随指数的投资方式。与被动型投资相反，主动型投资是根据投资者的知识结合经验进行主动选股，不是被动跟随指数。主动型投资者期望获得超越市场的收益，被动型投资者满足于市场平均收益率水平。

指数增强是指在跟踪指数的基础上，采用一些判断基准，将不看好的股票权重调低或平仓，将看好的股票加大仓位，以提高收益率的方法。

**既然如此，我已经判断出来哪只是“好股票”，哪只是“一般”的股票，为什么不直接买入？而是要买入指数呢？**

指数增强不同于其他主动投资方式，除了注重获取超越市场的收益，还要兼顾降低组合风险，注重收益的稳定性。如果判断失误，只买入选中股票而非指数会导致投资者承受巨大亏损。

**怎样选择股票？**

和alpha对冲策略类似，指数增强仅仅是一个思路，怎样选择“好股”还需投资者结合自身经验判断。

本策略利用“动量”这一概念，认为过去5天连续上涨的股票具备继续上涨的潜力，属于强势股；过去5天连续下跌的股票未来会继续下跌，属于弱势股。

## 2. 策略步骤

第一步：选择跟踪指数，以权重大于0.35%的成分股为股票池。  
第二步：根据个股价格动量来判断是否属于优质股，即连续上涨5天则为优势股；间隔连续下跌5天则为劣质股。  
第三步：将优质股权重调高0.2，劣质股权重调低0.2。

回测时间:2017-07-01 08:00:00 到 2017-10-01 16:00:00  
回测选股股票池：沪深300成分股  
回测初始资金：1000万

## 3. 策略代码

```