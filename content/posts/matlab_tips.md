---
title: "Matlab 用过用法用例"
date: 2022-04-06T09:37:31+08:00
draft: true
categories:
  - 编程
tags:
  - Matlab 
---

记录使用 Matlab 过程中的一些用法，以及用例。

<!--more-->

## 数组

数组

元胞数组：用来存放不同种类数据的数组，用花括号`{}`括起来.

### 数组创建

### 数组索引

`,`——用于分隔数组不同维度，如`A(1,2)`

`:`——用于指定索引范围，如`A(:)`，`A(:2)`，`A(2:)`，`A(1:2)`

两者混合使用：`A(:,:)`，`A(:,:2)`，`A(1:2,1:2)`

注意：其中`A(:)`和`B(:,:)`将数组A和B分别展开成1维和2维，前提是A和B分别得是高于1维和2维的数组。事实上：1）在索引数组时，只要有一个维度给出了具体的索引范围，则不管有没有给出所有维度的索引，都仅仅取已给出索引维度的元素（参以下代码里的c5,c6,c7命令）；2）而如果所有给出索引的维度都只是`:`，那么就会按维度顺序保留索引维度数目的数组结构，而之后未被索引的维度则全部展开到前面索引的维度上（参以下代码里的c2,c3,c4命令）。

比如：
```matlab

K>> a = rand(2,2,2) %c1
a(:,:,1) =
    0.7537    0.5678
    0.3804    0.0759
a(:,:,2) =
    0.0540    0.7792
    0.5308    0.9340
K>> a(:)' %c2 只有一个冒号时，会将整个3维数组展开成一个1维数组，消掉的是后两个维度
ans =
    0.7537    0.3804    0.5678    0.0759    0.0540    0.5308    0.7792    0.9340
K>> a(:,:) %c3 只有两个冒号时，会将整个3维数组展开成一个2维数组，消掉的是最后一个维度
ans =
    0.7537    0.5678    0.0540    0.7792
    0.3804    0.0759    0.5308    0.9340
K>> a(:,:,:) %c4 不会展开任何维度，保持数组的原样输出
ans(:,:,1) =
    0.7537    0.5678
    0.3804    0.0759
ans(:,:,2) =
    0.0540    0.7792
    0.5308    0.9340
K>> a(:,:,1) %c5 将输出数组的第1个page
ans =
    0.7537    0.5678
    0.3804    0.0759
K>> a(:,1,:) %c6 
ans(:,:,1) =
    0.7537
    0.3804
ans(:,:,2) =
    0.0540
    0.5308
K>> a(:,1) %c7
ans =
    0.7537
    0.3804
```

可以通过条件语句获取符合条件的元素在数组中的索引：`[row, col] = find(A=value)` 或者 `[row, col] = find(A=value)`

比如：

```matlab
K>> b = rand(2,3) 
b =
    0.8308    0.5497    0.2858
    0.5853    0.9172    0.7572

K>> b = b + 100*(b>0.5) % 实现将数组中大于0.5的数都加100，其中b>0.5是一个判断语句，返回一个和b同样大小的数组，数组元素为1（若对应位置b的元素大于0.5），反之，元素为0。
b =
  100.8308  100.5497    0.2858
  100.5853  100.9172  100.7572
```

### 逐页取各矩阵的对角元素

参：https://218.93.127.64/thread-572473-1-1.html?s_tid=RelatedContent

```matlab
temp4 = arrayfun(@(ii) diag(temp2(:,:,ii)), [1:1:size(temp2,3)],'UniformOutput',false);
temp4 = vertcat([temp4{:}]);
```

### 矩阵对角线元素赋值为0

参：https://www.ilovematlab.cn/thread-100938-1-1.html

```matlab
B(logical(eye(N)))=0
% 或
B+diag(-diag(B));
% 或
tril(B,-1)+triu(B,1);
% 或 2页矩阵的对角元都赋0
B(logical(cat(10,eye(10),eye(10)))) = 0;
```

### 删除矩阵中指定行列

参：http://www.4k8k.xyz/article/weixin_46398948/120308903

先通过数组索引指明要删除的行列，然后直接赋值为空数组即可。

### 随机创建具有 $k$ 条链路的 $n \times n$ 邻接矩阵

```matlab
function [nodes1, nodes2] = createTopo(nServer,nLink)
    % 网络拓扑随机生成
    mtx = rand(nServer, nServer);
    mtx = triu(mtx,1);
    mtx_a = mtx(:);
    [~,idx] = maxk(mtx_a, nLink);
    [nodes1, nodes2] = ind2sub([nServer,nServer], idx);

    v = ones(length(nodes1));
    adjMatrix = full(sparse(nodes1,nodes2,v,nServer,nServer)); %此时得到的邻接矩阵为上三角矩阵
    adjMatrix = adjMatrix + adjMatrix';
end
```

### 查找数组中特定元素

参：https://ww2.mathworks.cn/help/matlab/matlab_prog/find-array-elements-that-meet-a-condition.html

通过对数组应用条件来筛选数组元素。
其中条件通过具有logitic类型值的逻辑表达式表示，如`A>2`

直接对数组用表达式，返回的是符合条件的元素在数组中的`逻辑索引`数组，其和原数组结构相同。

使用`find`函数，如`find(A>2)`，同样可以获取满足`A>2`条件的元素的索引，只不过其索引是`线性索引`，即元素在将数组逐列连接后的一维数组中的索引。

而如果要获取对应的元素值，则可以直接对数组用以上两种索引取值即可：`A(A>2)`,`A(find(A>2))`

`ismember(A,B)`：按位判断A中元素是否在B中存在，返回和A等长的`logitic`数组。~~获取B中元素在A中的索引~~

### 同时给多个变量赋值（deal）

参：https://blog.csdn.net/weixin_43465015/article/details/89859006 —— matlab 同时给多个变量赋值（deal）


## Matlab 面向对象编程

### 属性值验证🔺

参：
https://ww2.mathworks.cn/help/matlab/matlab_oop/validate-property-values.html
https://ww2.mathworks.cn/help/matlab/matlab_oop/property-validator-functions.html

<div align="center" ><img src="https://img1.wlcheng.cc/images_for_blogs/20220407140550.png" alt="20220407140550" width="75%" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/><br><div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #777; font-size: 90%; padding: 1px;">属性验证语法格式</div></div>

`Size`：
`Class`：
`Functions`：

### 句柄传递与值传递



### 静态变量

`Static`静态变量可以直接通过类名访问，且其对于任何类对象都是共用的。

Matlab中不支持`Static`了，转而支持不能改变值的`Constant`常量值，其初始化定义后就不能再修改。

<font color=#FF0000>可以通过定义静态函数，在静态函数中定义持久变量（`persistent`）实现类似的作用。</font>

```matlab
classdef StoreData
   methods (Static)
      function out = setgetVar(data)
         persistent Var;
         if nargin
            Var = data;
         end
         out = Var;
      end
   end
end
```

### 运算符重载

参：https://ww2.mathworks.cn/help/matlab/matlab_oop/implementing-operators-for-your-class.html

### 对象数组 🔴
