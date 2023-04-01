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

- 很好：[Python matplotlib 入门级绘制图形 (三)-- 完善你的统计图形](https://blog.csdn.net/Janbob_Xiao/article/details/123830784)
- 柱状图排布：[matplotlib 之 pyplot 模块之柱状图（bar ()：多组数据并列柱状图通用简便创建方法） - mighty13 的文章 - CSDN](https://blog.csdn.net/mighty13/article/details/113873617)
- 图例：[python matplotlib 自定义图例位置（一） - WYoung 的文章 - 知乎](https://zhuanlan.zhihu.com/p/464335876)
- 透明度：[牛散村:python 如何设置柱状图参数？matplotlib 绘制柱状图参数详解 - CSDN](https://blog.csdn.net/weixin_45378258/article/details/118961719?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-118961719-null-null.pc_agg_new_rank&utm_term=Python%E7%9A%84bar%E8%AE%BE%E7%BD%AE%E6%9F%B1%E7%8A%B6%E5%9B%BE%E9%80%8F%E6%98%8E%E5%BA%A6&spm=1000.2123.3001.4430)

### 数据处理


- 求均值：[numpy 求平均值](https://blog.csdn.net/reyyy/article/details/108279103)
- 数组转置：[python 转置 - 毒鸡蛋的文章 - CSDN](https://blog.csdn.net/dujidan/article/details/121932770?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-5-121932770-null-null.pc_agg_new_rank&utm_term=python%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E8%BD%AC%E7%BD%AE&spm=1000.2123.3001.4430)
- 等差、等比数列：[【冰糖 Python】numpy：等差数列 等比数列](https://blog.csdn.net/xiaoxiao_ziteng/article/details/115272880)
- 数组分割：[numpy.argpartition () 的作用 - 江南蜡笔小新 的文章 - CSDN](https://blog.csdn.net/ftimes/article/details/119939691) 、[python (np.argpartition ()) 输出 前 n 个最大 / 小值所对应的索引](https://blog.csdn.net/weixin_41457494/article/details/105603211)
- 数组差/相减：[Numpy 中进行数组数据相减运算（- 号或者 np.subtract）](https://blog.csdn.net/sdgfbhgfj/article/details/123782111)

[预测评价指标：MSE,RMSE,MAE，MAPE，SMAPE](https://blog.csdn.net/qq_31244453/article/details/114665195)

### python 语法

- [Python 跨文件全局变量的使用技巧](https://blog.csdn.net/liming89/article/details/122879717)
- 字典读/取：[python 字典快速保存于读取的方法](http://t.csdn.cn/G3n0h)


### 文件处理

[python 判断文件和文件夹是否存在、创建文件夹](https://www.cnblogs.com/hushaojun/p/4533241.html)

### 字符串处理

[python 对字符串切片、split 分割、截取数据](https://blog.csdn.net/weixin_39801202/article/details/110489045)


### 编写 论文批量翻译和转换 时所用

- [免费的python谷歌翻译库 - 知乎](https://zhuanlan.zhihu.com/p/454186846)
- [4. 使用 Python 处理 PDF 文档 — Python 基础与应用 文档](https://www.osgeo.cn/python-tutorial/pdf.html#)
- [Python 使用 Google 多语言翻译 Excel 文档_python 中 lang_tgt_倾云鹤的博客 - CSDN 博客](https://blog.csdn.net/qq_38779672/article/details/117805238)
- [LaTeX 计数器 - CSDN 博客](https://blog.csdn.net/xovee/article/details/123524530)
- [转：latex cite 的几种用法_\cite latex_jueshu 的博客 - CSDN 博客](https://blog.csdn.net/jueshu/article/details/89186918)
- [LaTeX 中各种间距（矩阵表格行列间距，段落行间距，页边距）_latex 表格行间距_gsgbgxp 的博客 - CSDN 博客](https://blog.csdn.net/gsgbgxp/article/details/125705996)
- [Latex 数学公式中的矩阵 - solvit - 博客园](https://www.cnblogs.com/solvit/p/11345482.html)
- [「 LaTeX 」写论文，如何调整公式或矩阵大小_latex 公式大小_Robot_Starscream 的博客 - CSDN 博客](https://blog.csdn.net/Robot_Starscream/article/details/107001259)
- [python 科研向论文检索篇 —— 提取 PDF 文字以供全文信息检索 - CSDN 博客](https://blog.csdn.net/ruierx/article/details/123069184)
- [实用脚本！Python 提取 PDF 指定内容生成新文件！ - 知乎](https://zhuanlan.zhihu.com/p/378292176)
- [别再问如何用 python 提取 PDF 内容了！ - 知乎](https://zhuanlan.zhihu.com/p/260670061)
- [【Python】PDF 文档导出指定章节为 TXT__清风来叙的博客 - CSDN 博客](https://blog.csdn.net/XiaoYuHaoAiMin/article/details/121177159)
- [Python3 googletrans 谷歌翻译出错：‘NoneType‘ object has no attribute ‘group‘_Max@2008 的博客 - CSDN 博客](https://blog.csdn.net/xfyuanjun520/article/details/115465873)
- [Pandas 将列值转换为字符串 | D 栈 - Delft Stack](https://www.delftstack.com/zh/howto/python-pandas/pandas-convert-column-values-to-string/?utm_content=cmp-true)
- [如何获取 Dataframe 的行数和列数_dataframe 行数_lwgkzl 的博客 - CSDN 博客](https://blog.csdn.net/lwgkzl/article/details/80988126)
- [函数 reset_index ()：重置 DataFrame 索引_reset index_*Snowgrass * 的博客 - CSDN 博客](https://blog.csdn.net/qq_36535820/article/details/100599272#:~:text=%E9%80%9A%E8%BF%87reset_index%20%28%29%E6%96%B9%E6%B3%95%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E9%87%8D%E7%BD%AE%E7%B4%A2%E5%BC%95%EF%BC%8Cdrop%E5%8F%82%E6%95%B0%E4%B8%BATrue%E6%97%B6%EF%BC%8C%E7%9B%B4%E6%8E%A5%E4%B8%A2%E5%BC%83%E5%8E%9F%E6%9D%A5%E7%9A%84%E7%B4%A2%E5%BC%95%EF%BC%8C%E5%90%A6%E5%88%99%E5%8E%9F%E6%9D%A5%E7%9A%84%E7%B4%A2%E5%BC%95%E6%96%B0%E7%94%9F%E6%88%90%E4%B8%80%E5%88%97%E5%90%8D%E4%B8%BA%27index%27%E7%9A%84%E5%88%97%EF%BC%9A%20df.reset_index,%28inplace%3D%20True%2Cdrop%3D%20True%29%202%E3%80%81%E8%AE%BE%E7%BD%AE%E5%85%B6%E4%BB%96%E5%88%97%E4%B8%BA%E7%B4%A2%E5%BC%95)
- [Pyhton：创建一个空的 dataframe，逐行添加数据_chandelierds 的博客 - CSDN 博客](https://blog.csdn.net/chandelierds/article/details/107959865#:~:text=df%3D%20df.append%28your_data%2C%20ignore_index%3DTrue%29%201%20%EF%BC%8C%E4%BB%8E%E5%8E%9F%E6%9C%89%E7%9A%84,Dataframe%20%E4%B8%AD%20%E9%80%90%E8%A1%8C%20%E7%AD%9B%E9%80%89%E5%87%BA%E6%8C%87%E5%AE%9A%E7%9A%84%E8%A1%8C%EF%BC%88%E7%B1%BB%E5%9E%8B%E4%B8%BApandas%E7%9A%84Series%EF%BC%89%EF%BC%8C%E5%B9%B6%E4%BD%BF%E7%94%A8append%E6%96%B9%E6%B3%95%E8%BF%9B%E8%A1%8C%20%E6%B7%BB%E5%8A%A0%20%E3%80%82)


- [批量论文自动下载 —— 从 dblp 数据库中查找并爬取论文_小郁同学的博客 - CSDN 博客](https://blog.csdn.net/qq_42743778/article/details/120536499)
- [dblp: Search for "Low-Latency Federated Learning over Wireless Channels with Differential Privacy"](https://dblp.uni-trier.de/search?q=Low-Latency%20Federated%20Learning%20over%20Wireless%20Channels%20with%20Differential%20Privacy)
- [dblpy Failed to establish a new connection: [WinError 10060] - 搜索](https://www.bing.com/search?q=dblpy+Failed+to+establish+a+new+connection%3A+%5BWinError+10060%5D&qs=n&form=QBRE&sp=-1&lq=0&pq=dblpy+failed+to+establish+a+new+connection%3A+%5Bwinerror+10060%5D&sc=0-60&sk=&cvid=D5B185642BAD4F33A5A5251DF6B31949&ghsh=0&ghacc=0&ghpl=)
- [55 - 爬虫 TimeoutError: [WinError 10060] 由于连接方在一段时间后没有正确答复或连接的主机没有反应，连接尝试失败_ystraw_ah 的博客 - CSDN 博客](https://blog.csdn.net/qq_39451578/article/details/104216121)
- [关于（[WinError 10060] 由于连接方在一段时间后没有正确答复或连接的主机没有反应，连接尝试失败。）的解决方案。实测有效！_灵海之森的博客 - CSDN 博客](https://blog.csdn.net/qq_43814415/article/details/112907350)
- [解决 requests.get/post 报错 Failed to establish a new connection: [WinError 10060]_requests 10060_郑德帅的博客 - CSDN 博客](https://blog.csdn.net/qq_36853469/article/details/106835282)
- [httpcore._exceptions.ReadTimeout: The read operation timed out - 搜索](https://www.bing.com/search?q=httpcore._exceptions.ReadTimeout%3A+The+read+operation+timed+out&cvid=f38c87e1ef1b48d1abd1bebe163a09be&aqs=edge..69i57.1140j0j1&pglt=161&FORM=ANNTA1&PC=U531&mkt=zh-CN)
- [[已解决] Googletrans 报错，连接超时_googletrans timeout_Shawn 向陽的博客 - CSDN 博客](https://blog.csdn.net/weixin_44174352/article/details/113730727)
- [python3--googletrans 超时报错解决以及翻译工具优化（附源码）_懷淰メ的博客 - CSDN 博客](https://blog.csdn.net/a1397852386/article/details/111479024)

- [python3 函数参数（必选参数、默认参数、关键字参数、可变参数）](https://blog.csdn.net/weixin_44740756/article/details/116672849)
- [如何在 jupyter 中执行带参数的 py 文件](https://blog.csdn.net/jining11/article/details/118230213)
- [python 笔记 54-re 正则匹配替换字符串 (sub 和 subn)](https://cloud.tencent.com/developer/article/1774589)

- [python 从文件路径中提取文件名、所在文件夹](https://blog.csdn.net/chen565884393/article/details/127849397)
- [在 Python 字典中按值查找键](https://blog.csdn.net/wjj2586590669/article/details/126396851)
- [Python 学习笔记：按特定字符排序 sort_values](https://www.cnblogs.com/hider/p/15758725.html#:~:text=%E5%88%A9%E7%94%A8%20pd.sort_values%20%E5%8F%AF%E4%BB%A5%E5%AE%9E%E7%8E%B0%E5%AF%B9%E6%95%B0%E6%8D%AE%E6%A1%86%E7%9A%84%E6%8E%92%E5%BA%8F%E3%80%82%20DataFrame.sort_values%20%28by%2C%20%23%20%E6%8E%92%E5%BA%8F%E5%AD%97%E6%AE%B5%20axis%3D,ignore_index%3D%20False%2C%20%23%20%E5%BF%BD%E7%95%A5%E7%B4%A2%E5%BC%95%20key%3D%20None%29%20%23%20%E5%87%BD%E6%95%B0)

```python 
my_dict ={"John":1, "Michael":2, "Shawn":3}

list_of_key = list(my_dict.keys())
list_of_value = list(my_dict.values())
position = list_of_value.index(1)
print(list_of_key[position])
position = list_of_value.index(2)
print(list_of_key[position]) 

# 或者
list(my_dict.keys())[list(my_dict.values()).index(1)]
```

- [Python pandas 根据指定条件筛选数据](https://blog.csdn.net/p1306252/article/details/114879951)
- []()

#### 提取 one-tab 列表链接为 markdown 连接格式

```python
import requests
from bs4 import BeautifulSoup

url = 'https://www.one-tab.com/page/UtFAre9iTNSTQdDXacYRAg'

# 发送请求并获取页面内容
response = requests.get(url)
html = response.content

# 解析页面内容
soup = BeautifulSoup(html, 'html.parser')
links = soup.find_all('a')

# 提取链接和标题并输出
for link in links:
    href = link.get('href')
    if href.startswith('http'):
        title = link.text
        print(f"[{title}]({href})")
```