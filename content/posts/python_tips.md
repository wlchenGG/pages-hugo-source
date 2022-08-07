---
title: "Python tips"
date: 2022-07-15T15:24:03+08:00
draft: true
---

## 环境配置

### miniconda

### pytorch

### cuda、cudnn

[PyTorch 中查看 GPU 使用情况以及一些重要函数](https://zhiqianghe.blog.csdn.net/article/details/111661128)

## python 一些功能代码

### 作图

#### 画柱状图通用方法

```python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np

label = ['G1', 'G2', 'G3', 'G4', 'G5']
first = [20, 34, 30, 35, 27]
second = [25, 32, 34, 20, 25]
third = [21, 31, 37, 21, 28]
fourth = [26, 31, 35, 27, 21]
data = [first, second, third, fourth]
sig=[0,0.1,0.2,0.3,0.5,0.7]
color1=['r','g','b','c','m','yellow'] #红,绿，蓝，青，紫，黄

def create_multi_bars(labels, datas, tick_step=1, group_gap=0.2, bar_gap=0):
    '''
    labels : x轴坐标标签序列
    datas ：数据集，二维列表，要求列表每个元素的长度必须与labels的长度一致
    tick_step ：默认x轴刻度步长为1，通过tick_step可调整x轴刻度步长。
    group_gap : 柱子组与组之间的间隙，最好为正值，否则组与组之间重叠
    bar_gap ：每组柱子之间的空隙，默认为0，每组柱子紧挨，正值每组柱子之间有间隙，负值每组柱子之间重叠
    '''
    # ticks为x轴刻度
    ticks = np.arange(len(labels)) * tick_step
    # group_num为数据的组数，即每组柱子的柱子个数
    group_num = len(datas)
    # group_width为每组柱子的总宽度，group_gap 为柱子组与组之间的间隙。
    group_width = tick_step - group_gap
    # bar_span为每组柱子之间在x轴上的距离，即柱子宽度和间隙的总和
    bar_span = group_width / group_num
    # bar_width为每个柱子的实际宽度
    bar_width = bar_span - bar_gap
    # baseline_x为每组柱子第一个柱子的基准x轴位置，随后的柱子依次递增bar_span即可
    baseline_x = ticks - (group_width - bar_span) / 2
    for index, y in enumerate(datas):
        plt.bar(baseline_x + index*bar_span, y, bar_width, color = color1[index], label = 'sigma=%.1f'%sig[index])
    plt.legend(loc = "best")
    plt.ylabel('Scores')
    plt.title('multi datasets')
    # x轴刻度标签位置与x轴刻度一致
    plt.xticks(ticks, labels)
    plt.savefig('2、3、4层神经网络下的平均损失.jpg', bbox_inches='tight')
    
create_multi_bars(label, data)
```

#### 循环画图、并保存

```python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np

a = np.array([1,1,1])
b = np.array([1,2,3])
plt.ion()
for i in range(3):
  print("第%d个:",i+1)
  fig = plt.figure()
  plt.plot([0,1,2], (i+1)*a*b)
  plt.title('图'+str(i+1))
  plt.xlabel('MSE损失值')
  plt.ylabel('累积分布')
  plt.rcParams['font.sans-serif']=['SimHei']
  plt.rcParams['axes.unicode_minus'] = False
  plt.savefig(str(i+1)+'.jpg')
  plt.show()
  plt.close()
```

参考：

很好：[Python matplotlib 入门级绘制图形 (三)-- 完善你的统计图形](https://blog.csdn.net/Janbob_Xiao/article/details/123830784)
柱状图排布：[matplotlib 之 pyplot 模块之柱状图（bar ()：多组数据并列柱状图通用简便创建方法） - mighty13 的文章 - CSDN](https://blog.csdn.net/mighty13/article/details/113873617)
图例：[python matplotlib 自定义图例位置（一） - WYoung 的文章 - 知乎](https://zhuanlan.zhihu.com/p/464335876)
透明度：[牛散村:python 如何设置柱状图参数？matplotlib 绘制柱状图参数详解 - CSDN](https://blog.csdn.net/weixin_45378258/article/details/118961719?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-118961719-null-null.pc_agg_new_rank&utm_term=Python%E7%9A%84bar%E8%AE%BE%E7%BD%AE%E6%9F%B1%E7%8A%B6%E5%9B%BE%E9%80%8F%E6%98%8E%E5%BA%A6&spm=1000.2123.3001.4430)

### 数据处理


求均值：[numpy 求平均值](https://blog.csdn.net/reyyy/article/details/108279103)
数组转置：[python 转置 - 毒鸡蛋的文章 - CSDN](https://blog.csdn.net/dujidan/article/details/121932770?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-5-121932770-null-null.pc_agg_new_rank&utm_term=python%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E8%BD%AC%E7%BD%AE&spm=1000.2123.3001.4430)
等差、等比数列：[【冰糖 Python】numpy：等差数列 等比数列](https://blog.csdn.net/xiaoxiao_ziteng/article/details/115272880)
数组分割：[numpy.argpartition () 的作用 - 江南蜡笔小新 的文章 - CSDN](https://blog.csdn.net/ftimes/article/details/119939691) 、[python (np.argpartition ()) 输出 前 n 个最大 / 小值所对应的索引](https://blog.csdn.net/weixin_41457494/article/details/105603211)
数组差/相减：[Numpy 中进行数组数据相减运算（- 号或者 np.subtract）](https://blog.csdn.net/sdgfbhgfj/article/details/123782111)

[预测评价指标：MSE,RMSE,MAE，MAPE，SMAPE](https://blog.csdn.net/qq_31244453/article/details/114665195)

### python 语法

[Python 跨文件全局变量的使用技巧](https://blog.csdn.net/liming89/article/details/122879717)
字典读/取：[python 字典快速保存于读取的方法](http://t.csdn.cn/G3n0h)


### 文件处理

[python 判断文件和文件夹是否存在、创建文件夹](https://www.cnblogs.com/hushaojun/p/4533241.html)

### 字符串处理

[python 对字符串切片、split 分割、截取数据](https://blog.csdn.net/weixin_39801202/article/details/110489045)