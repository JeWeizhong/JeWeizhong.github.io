---
title: 用python3的matplotlib模块画条形图
categories: 
  - python3
tags:
  - 编程语言
  - python3
  - 可视化
date: 2018-05-13
---

文章来源：https://www.kesci.com/apps/home/project/59ed8d7418ec724555a9b4c0

### 示例一
```

# -*- coding: UTF-8 -*-

import matplotlib.pyplot as plt

GDP = [12406.8,13908.57,9386.87,9143.64]
# 中文乱码的处理
plt.rcParams['font.sans-serif'] =['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 绘图
plt.bar(range(4), GDP, align = 'center',color='steelblue', alpha = 0.8)
# 添加轴标签
plt.ylabel('GDP')
# 添加标题
plt.title('四个直辖市GDP大比拼')
# 添加刻度标签
plt.xticks(range(4),['北京市','上海市','天津市','重庆市'])
# 设置Y轴的刻度范围
plt.ylim([5000,15000])

# 为每个条形图添加数值标签
for x,y in enumerate(GDP):
    plt.text(x,y+100,'%s' %round(y,1),ha='center')  # round函数 ，返回y保留1位小数的四舍五入的值
            # 坐标，文字，对齐方式
# 显示图形
plt.show() 
```
![示例一](\myphoto\Figure_1.png)

---


### 示例二

```
# 导入绘图模块
import matplotlib.pyplot as plt
import numpy as np
# 构建数据
Y2016 = [15600,12700,11300,4270,3620]
Y2017 = [17400,14800,12000,5200,4020]
labels = ['北京','上海','香港','深圳','广州']
bar_width = 0.35

# 中文乱码的处理
plt.rcParams['font.sans-serif'] =['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 绘图
plt.bar(np.arange(5), Y2016, label = '2016', color = 'steelblue', alpha = 0.8, width = bar_width)
plt.bar(np.arange(5)+bar_width, Y2017, label = '2017', color = 'indianred', alpha = 0.8, width = bar_width)
# 添加轴标签
plt.xlabel('Top5城市')
plt.ylabel('家庭数量')
# 添加标题
plt.title('亿万财富家庭数Top5城市分布')
# 添加刻度标签
plt.xticks(np.arange(5)+bar_width,labels)
# 设置Y轴的刻度范围
plt.ylim([2500, 19000])

# 为每个条形图添加数值标签
for x2016,y2016 in enumerate(Y2016):
    plt.text(x2016-0.17, y2016+200, '%s' %y2016)

for x2017,y2017 in enumerate(Y2017):
    plt.text(x2017+0.17, y2017+100, '%s' %y2017)
# 显示图例
plt.legend()
# 显示图形
plt.show()
```
![示例二](\myphoto\Figure_1.png)