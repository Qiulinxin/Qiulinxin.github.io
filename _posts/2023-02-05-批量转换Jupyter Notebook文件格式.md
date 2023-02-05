---
title: 批量转换Jupyter Notebook文件格式
categories: [note]
comments: true
---

# 批量转换Jupyter Notebook文件格式


## 用途

1. 批量转换`Jupyter Notebook`文件格式
2. 将转换后的文件移动至相应文件夹



## 背景

写这个脚本的背景还是之前的课程设计，因为在做课程设计时我是先在电脑上用`Jupyter Notebook`打草稿，等全部课程设计完成后再手写到纸上。用`Jupyter Notebook`很方便做笔记和打草稿，配合转换为`Markdown`文件后能够得到更好的阅读体验。在完成课程设计的过程中，没有想到用代码来实现批量转换文件格式的工作，傻傻的用手动操作的方法一个一个的转换了全部文件。好在一共才6个文件，只用了一两分钟的时间就解决了问题。

为以后类似的工作做准备，同时也为了把自己从重复低级的工作中解放出来，把时间用在更有价值的事上，决定找一下别的方法。在网上查到的方法是可以用`Python`的第三方库`nbconvert`实现将`Jupyter Notebook`文件转换为其他格式的文件，支持的文件格式有：

- [HTML](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-html),
- [LaTeX](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-latex),
- [PDF](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-pdf),
- [WebPDF](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-webpdf),
- [Reveal.js HTML slideshow](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-revealjs),
- [Markdown](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-markdown),
- [Ascii](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-ascii),
- [reStructuredText](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-rst),
- [executable script](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-script),
- [notebook](https://nbconvert.readthedocs.io/en/latest/usage.html#convert-notebook).

并且支持两种使用方法，作为命令行工具使用（[Using as a command line tool](https://nbconvert.readthedocs.io/en/latest/usage.html)）和使用`nbconvert`作为库（[Using nbconvert as a library](https://nbconvert.readthedocs.io/en/latest/nbconvert_library.html)）。



## 方法

这里我使用的是第一种方法，作为命令行工具使用，大致思路如下：

1. 获取所有`Jupyter Notebook`文件的名称
2. 使用`Python`调用命令行转换文件
3. 创建文件夹
4. 将文件移动至文件夹

## 代码



```python
import os
import shutil


# 获取文件名
def get_file_name(judgement='.ipynb') -> list:
    listdir = os.listdir()
    # 筛选出Jupyter Notebook文件
    listdir = [
        i for i in listdir if judgement in i and '.ipynb_checkpoints' not in i]
    return listdir


# 组合文件名
def combine_file_name(listdir: list) -> str:
    file_name = ' '
    for i in range(len(listdir)):
        file_name = file_name + ' ' + listdir[i]
    return file_name


# 输入命令
def enter_command(file_name: str, file_type='markdown') -> None:
    os.system('jupyter nbconvert --to ' + file_type + file_name)
    # return 'jupyter nbconvert --to ' + file_type + file_name


# 移动文件
def move_file(file_name: list, folder_name='markdown') -> None:
    # 若目标文件夹已存在
    # 则删掉整个文件夹及目录下所有文件
    # 重新创建空文件夹
    # 若目标文件夹不存在
    # 直接创建空文件夹
    if os.path.exists(folder_name):
        shutil.rmtree(folder_name)
        os.mkdir(folder_name)
    else:
        os.mkdir(folder_name)

    # 移动文件至目标文件夹
    for i in range(len(file_name)):
        shutil.move(src=file_name[i], dst=folder_name)


# 主函数
def main():
    # 读取并处理文件名称
    listdir = get_file_name()
    file_name = combine_file_name(listdir=listdir)

    # 完成相应转换
    enter_command(file_name=file_name)
    # print(enter_command(file_name=file_name))

    # 移动文件至目标文件夹
    move_file_name = get_file_name(judgement='.md')
    # print(move_file_name)
    move_file(file_name=move_file_name)

    print('All Finished!')


if __name__ == "__main__":
    main()

```

## 小结

1.使用推导式能够简化代码，如下是实现函数`get_file_name()`的两种写法。



```python
import os
import shutil


# 获取文件名-使用推导式
def get_file_name_1(judgement='.ipynb') -> list:
    listdir = os.listdir()
    # 筛选出Jupyter Notebook文件
    listdir = [
        i for i in listdir if judgement in i and '.ipynb_checkpoints' not in i]
    return listdir


# 获取文件名-不使用推导式
def get_file_name_2(judegement='.ipynb') -> list:
    listdir = []
    for i in os.listdir():
        # 筛选出Jupyter Notebook文件
        if judegement in i and '.ipynb_checkpoints' not in i:
            listdir.append(i)
    return listdir


print('获取文件名-使用推导式:')
print(get_file_name_1())

print('获取文件名-不使用推导式:')
print(get_file_name_2())

```

    获取文件名-使用推导式:
    ['demo.ipynb']
    获取文件名-不使用推导式:
    ['demo.ipynb']


2.关于以下标准库和内置函数的不同功能

- `os`提供了一种使用与操作系统相关的功能的便捷式途径。
- `open()`读写一个文件
- `os.path`操作文件路径
- `fileinput`读取通过命令行给出的所有文件中的所有行
- `tempfile`创建临时文件和目录
- `shutil`高级文件和目录处理

[来自官方文档：https://docs.python.org/zh-cn/3.9/library/os.html](https://docs.python.org/zh-cn/3.9/library/os.html)

3.一些基本方法

- 读取当前文件夹下所有文件`os.listdir()`，返回一个列表，列表中文件名称的排序是无序的
- 创建空文件夹`os.mkdir('folder_name')`
- 调用系统命令行/在子`shell`中执行命令（字符串）。`os.system('command')`
- 判断文件夹是否存在`os.path.exists(folder_name)`
- 删掉整个文件夹及目录下所有文件`shutil.rmtree('folder_name')`
- 移动文件位置`shutil.move(src, dst)`

