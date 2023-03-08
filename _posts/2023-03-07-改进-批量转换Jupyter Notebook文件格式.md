---
title: 改进-批量转换Jupyter Notebook文件格式
categories: [note]
comments: true
---


# 改进-批量转换Jupyter Notebook文件格式

## 说明

发现了一个`Jupyter Notebook`的拓展，在`Jupyter Notebook`中使用`handcalcs`库可以将`Python`代码转换为`LaTeX`公式并渲染。减去了写完代码计算数据后，还需要写`LaTeX`公式的步骤。


> **Note:** `handcalcs`项目地址[https://github.com/connorferster/handcalcs](https://github.com/connorferster/handcalcs)

## 问题

如果继续使用之前的脚本[批量转换Jupyter Notebook文件格式](https://qiulinxin.github.io/2023-02/%E6%89%B9%E9%87%8F%E8%BD%AC%E6%8D%A2Jupyter-Notebook%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)将`XXX.ipynb`批量转换为`XXX.md`，会遇到公式在`Markdown`编辑器`Typora`中不能正常显示的问题。


## 原因

使用脚本将`XXX.ipynb`转换为`XXX.md`文件后，原本在`Jupyter Notebook`中生成的`LaTeX`公式代码是以`\[`和`\]`包裹的，不会在`Markdown`编辑器`Typora`中被认定为`LaTeX`公式。


```LaTeX
\[
\begin{aligned}
m &= 2 \; 

a &= 0.500 \; 

F &= m \cdot a  = 2 \cdot 0.500 &= 1.000  
\end{aligned}
\]

```

## 办法

只需要将`\[`和`\]`替换为`$$`即可，便能在`Markdown`编辑器`Typora`中正常显示公式。


```LaTeX
$$
\begin{aligned}
m &= 2 \; 

a &= 0.500 \; 

F &= m \cdot a  = 2 \cdot 0.500 &= 1.000  
\end{aligned}
$$

```

## 改进

在原有代码的基础上进行改进，实现在将`XXX.ipynb`转换为`XXX.md`后自动将`\[`和`\]`替换为`$$`。

## 思路

在原有代码的基础上增添三个新的函数：


1. `read_text_file(file_name: str) -> str` 读取文本文件：读取已经转换为`XXX.md`的文件，以字符串的形式返回读取结果。
2. `process_text_file(text_file: str) -> str` 处理文本文件：处理由`read_text_file()`函数返回的字符串，将字符串中的相关符号进行替换，以字符串的形式返回处理结果。
3. `rewrite_file(file_name: str, treatment_text_file: str) -> None` 重新写入文件：将由`process_text_file()`函数处理过的字符串重新写入到文件夹中，这一步操作会覆盖掉原来已经存在的同名文件，不会返回任何结果。

应用上述3个新增的函数逐个处理相应的文件即可。

## 小结

### 1. 文件的读取处理

读取并处理文件的方式有两种：

```python
# 方法1
with open(file='demo.ipynb', mode='wt', encoding='utf-8') as f:
    reesult = f.read()

```


```python
# 方法2
f = open(file='demo.ipynb', mode='wt', encoding='utf-8')
result = f.read()
f.close()

```


1. 使用方法`1`的好处是在对文件的操作结束后，即离开`with`语句的缩进范围后，便会自动关闭已经打开的文件夹，不会造成当前文件正在使用的问题。
2. 方法`2`需要自己手动输入`f.close()`语句关闭已打开的文件，容易因为忘记使用`f.close()`而导致在后续移动文件时，因为文件在其他应用中被打开而无法操作。
3. 使用`open()`函数读取文件时，注意使用`encoding='utf-8'`指定编码，避免报错。
4. `f.read()`将文本以一个字符串的形式全部读入。
5. `f.readlines()`将文本逐行以列表的形式全部读入。

### 2. 字符串的`replace()`操作


```python
stra = 'demo'
strb = stra.replace('d', '')

```


```python
stra

```




    'demo'




```python
strb

```




    'emo'




```python
strb = stra.replace('a', '!')
strb

```




    'demo'



1. `replace()`操作不会对原字符串产生影响，因为在`Python`中字符串是不可变类型。
2. `replace()`操作对于被替换的对象如果不存在于字符串中，是不会报错的。利用这个特性在脚本中进行符号替换时，不需要考虑被替换的符号是否在`XXX.md`文件中。

## 代码

```python
import os
import shutil


# 获取文件名
def get_file_name(judgement='.ipynb') -> list:
    """获取文件名"""
    listdir = os.listdir()
    # 筛选出指定后缀名的文本文件
    # 并防止将`.ipynb_checkpoints`文件夹误判为`.ipynb`文件
    listdir = [
        i for i in listdir if judgement in i and '.ipynb_checkpoints' not in i]
    return listdir


# 组合文件名
def combine_file_name(listdir: list) -> str:
    """组合文件名"""
    file_name = ' '
    for i in range(len(listdir)):
        file_name = file_name + ' ' + listdir[i]
    return file_name


# 输入命令
def enter_command(file_name: str, file_type='markdown') -> None:
    """输入命令"""
    os.system('jupyter nbconvert --to ' + file_type + file_name)
    # return 'jupyter nbconvert --to ' + file_type + file_name


# 读取文本文件
def read_text_file(file_name: str) -> str:
    """读取文本文件"""
    with open(file=file_name, mode='rt', encoding='utf-8') as f:
        text_file = f.read()
        return text_file


# 处理文本文件
def process_text_file(text_file: str) -> str:
    """处理文本文件"""
    # 文本替换顺序不可更改
    text_file = text_file.replace(r'', '')
    text_file = text_file.replace(r'$$', '$$')
    treatment_text_file = text_file.replace(r'$$', '$$')
    return treatment_text_file


# 重新写入文件
def rewrite_file(file_name: str, treatment_text_file: str) -> None:
    """重新写入文件"""
    with open(file=file_name, mode='wt', encoding='utf-8') as f:
        f.write(treatment_text_file)


# 移动文件
def move_file(file_name: list, folder_name='markdown') -> None:
    """移动文件"""
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
    listdir = get_file_name(judgement='.ipynb')
    file_name = combine_file_name(listdir=listdir)

    # 完成相应转换
    enter_command(file_name=file_name)
    # print(enter_command(file_name=file_name))

    # 获取转换后的文件名
    converted_file_name = get_file_name(judgement='.md')
    # 进行文本替换
    for file_name in converted_file_name:
        text_file = read_text_file(file_name=file_name)
        treatment_text_file = process_text_file(text_file=text_file)
        rewrite_file(file_name, treatment_text_file)

    # 移动文件至目标文件夹
    # move_file_name = get_file_name(judgement='.md')
    # print(move_file_name)
    move_file(file_name=converted_file_name)

    print('All Finished!')


if __name__ == "__main__":
    main()

```
