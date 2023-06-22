---
title: Matplotlib 使用入门
categories: [note]
comments: true
---

# Matplotlib 使用入门

## 编码风格

具体使用 `Matplotlib` 时可以选用的编码风格：

1. 面向对象式（`object-oriented (OO) style`）：创建`Figures` 和 `Axes` 实例，使用相应的方法
2. 函数式（`pyplot-style`）：通过 `pyplot` 隐式的创建`Figures` 和 `Axes`，使用 `pyplot` 函数绘图

官方建议使用 `OO style` 编码风格，特别是在一些 **复杂** 的绘图和准备作为脚本和函数重复使用的 **大型项目** 上。

> In general, we suggest using the OO style, particularly for complicated plots, and functions and scripts that are intended to be reused as part of a larger project.

使用 `pyplot style` 编码风格，对于快速交互的工作是十分方便的。

>  However, the pyplot style can be very convenient for quick interactive work.

```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt

```

```python
# OO-style 编码风格
x = np.linspace(0, 2, 100)  # Sample data.

# Note that even in the OO-style, we use `.pyplot.figure` to create the Figure.
# fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained', dpi=120)
ax.plot(x, x, label='linear')  # Plot some data on the axes.
ax.plot(x, x**2, label='quadratic')  # Plot more data on the axes...
ax.plot(x, x**3, label='cubic')  # ... and some more.
ax.set_xlabel('x label')  # Add an x-label to the axes.
ax.set_ylabel('y label')  # Add a y-label to the axes.
ax.set_title("Simple Plot")  # Add a title to the axes.
ax.legend()  # Add a legend.

fig.savefig('img_01.png')

```
​    
<!-- ![png](2023_06_21_SummaryMatplotlib_files/2023_06_21_SummaryMatplotlib_8_0.png) -->
​    
<img src="{{ '/assets/2023-06-22/img_01.png' | relative_url }}">

```python
# pyplot-style 编码风格
x = np.linspace(0, 2, 100)  # Sample data.

# plt.figure(figsize=(5, 2.7), layout='constrained')
plt.figure(figsize=(5, 2.7), layout='constrained', dpi=120)
plt.plot(x, x, label='linear')  # Plot some data on the (implicit) axes.
plt.plot(x, x**2, label='quadratic')  # etc.
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend()

plt.savefig('img_02.png')

```
​    
<!-- ![png](2023_06_21_SummaryMatplotlib_files/2023_06_21_SummaryMatplotlib_9_0.png) -->
​    
<img src="{{ '/assets/2023-06-22/img_02.png' | relative_url }}">

##  `Matplotlib Figure` 的组成

<!-- ![](img/img-01.jpeg) -->

<!-- <img src="img/img-01.jpeg" style="zoom:35%;" /> -->

<img src="{{ '/assets/2023-06-22/img-01.jpeg' | relative_url }}">

### `Figure`

可以理解为绘图的那张 **白纸**，图上的一切元素都是在 `figure` 上展现的。

> The whole figure. The Figure keeps track of all the child Axes, a group of 'special' Artists (titles, figure legends, colorbars, etc), and even nested subfigures.

### `Axes`

可以理解为在 `Figure` **白纸** 上绘图时所建立的 **直角坐标系**，可以是 **两个轴** 也可以是 **三个轴**

`Axes` 是附加到 `Figure` 的 `Artist`，包含用于绘制数据的区域（用来绘制图表的那个区域），会包含两个轴 `Axis` 对象

> **Note:**
> 
> 注意区分 `Axes` 和 `Axis` 间的区别

`Axis` 提供 `ticks`（刻度） `tick labels`（刻度标签）对 `Axes` 中展示的数据进行操作，如缩放等。


> An Axes is an Artist attached to a Figure that contains a region for plotting data, and usually includes two (or three in the case of 3D) Axis objects (be aware of the difference between Axes and Axis) that provide ticks and tick labels to provide scales for the data in the Axes. Each Axes also has a title (set via set_title()), an x-label (set via set_xlabel()), and a y-label (set via set_ylabel()).

`Axes` 类及其成员函数是使用 `OOP` 接口的主要入口点，并且在其上定义了大多数绘图方法（例如，上面所示的 ax.plot() 使用了绘图方法）

> **Note:**
> 
> 所以官方会推荐使用`OO style` 编码风格，能够对图表进行更精细的控制，同时支持更多的绘图方法 

> The Axes class and its member functions are the primary entry point to working with the OOP interface, and have most of the plotting methods defined on them (e.g. ax.plot(), shown above, uses the plot method)

### `Axis`

就是 `Axes` **直角坐标系** 上的坐标轴，坐标轴上的元素由它来控制

### `Artist`

`Fighure` 白纸上所有可见的 **元素**，甚至 `Fighure` 也是属于 `Artist` 这个大类的

> Basically, everything visible on the Figure is an Artist (even Figure, Axes, and Axis objects). This includes Text objects, Line2D objects, collections objects, Patch objects, etc. When the Figure is rendered, all of the Artists are drawn to the canvas. Most Artists are tied to an Axes; such an Artist cannot be shared by multiple Axes, or moved from one to another.

## 绘制多幅图表

在同一张白纸 `Figure` 上绘制多张图表

1. 最基础的用法是使用 `plt.subplots()`
2. 另一种绘制复杂图表的方法是 `subplot_mosaic`


```python
# plt.subplots()
fig, axs = plt.subplots(nrows=2, ncols=1, dpi=120, layout='constrained')
axs[0].set_title('up')
axs[1].set_title('low')

fig.savefig('img_03.png')

```
​    
<!-- ![png](2023_06_21_SummaryMatplotlib_files/2023_06_21_SummaryMatplotlib_24_0.png) -->
​    
<img src="{{ '/assets/2023-06-22/img_03.png' | relative_url }}">

```python
# subplot_mosaic
fig, axd = plt.subplot_mosaic(layout='constrained', dpi=120,
                              mosaic=[['upleft', 'right'],
                                      ['lowleft', 'right']])
axd['upleft'].set_title('upleft')
axd['lowleft'].set_title('lowleft')
axd['right'].set_title('right')

fig.savefig('img_04.png')

```
​    
<!-- ![png](2023_06_21_SummaryMatplotlib_files/2023_06_21_SummaryMatplotlib_25_0.png) -->
​  
<img src="{{ '/assets/2023-06-22/img_04.png' | relative_url }}">

## 参考资料

[1]	Quick start guide — Matplotlib 3.7.1 documentation[EB/OL]. [2023-06-22]. [https://matplotlib.org/stable/tutorials/introductory/quick_start.html](https://matplotlib.org/stable/tutorials/introductory/quick_start.html).

