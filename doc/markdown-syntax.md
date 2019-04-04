# MarkDown语法备忘录

## 一、标题
一个#是一级标题 两个#是二级标题 以此类推支持6级标题
标准语法 #号后跟个空格后再跟文字

```markdown
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
##### 六级标题
```
效果如下：
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
##### 六级标题

## 二、字体
markdown编辑器本身是内容写作工具，本身并不支持文字排版，但markdown是支持HTML语法的，所以我们通过内嵌html或者内嵌css来实现想要的排版布局。
    
* <font size='3'>**字体大小**</font>

示例：
```markdown
<font size='1'>hello</font>
<font size='2'>hello</font>
<font size='3'>hello</font>
```
效果：

<font size='1'>hello</font>
<font size='2'>hello</font>
<font size='3'>hello</font>

* <font size='3'>**字体颜色**</font>
示例：

```markdown
<font color='red'>hello</font>
<font color='blue'>hello</font>
<font color='green'>hello</font>
```

<font color='red'>hello</font>
<font color='blue'>hello</font>
<font color='green'>hello</font>

* <font size='3'>**背景色**</font>
示例：

```markdown
<table>
	<tr>
    	<td bgcolor='#7FFFD4'>
        	我是第一行
         </td>
    </tr>
    	<tr>
    	<td bgcolor='#FF83FA'>
        	我是第二行
         </td>
    </tr>
    	<tr>
    	<td bgcolor='#D1EEEE'>
        	我是第三行
         </td>
    </tr>
</table>
```

示例：

<table>
	<tr>
    	<td bgcolor='#7FFFD4'>
        	我是第一行
         </td>
    </tr>
    	<tr>
    	<td bgcolor='#FF83FA'>
        	我是第二行
         </td>
    </tr>
    	<tr>
    	<td bgcolor='#D1EEEE'>
        	我是第三行
         </td>
    </tr>
</table>

*  <font size='3'>**文字居中对齐**</font>

示例：

```markdown
<center>我要居中</center>
<p align='left'>居左</p>
<p align='right'>居右</p>
```
效果：

<center>我要居中</center>
<p align='left'>居左</p>
<p align='right'>居右</p>

*  <font size='3'>**加粗**</font>

```java
** 我要加粗 **
```
效果:

**我要加粗**

*  <font size='3'>**斜体**</font>

```java
*斜体*
```
效果：

*斜体*
* <font size='3'>**斜体加粗**</font>

```java
***斜体加粗***
```
效果:

***斜体加粗***
*  <font size='3'>**删除线**</font>

```
~~删除线~~
```
效果：

~~删除线~~

*  <font size='3'>**使用标准字体**</font>
```
<font face="黑体">我是黑体字</font><br/>
<font face="微软雅黑">我是微软雅黑</font><br/>
<font face="STCAIYUN">我是华文彩云</font><br/>
```
示例：

<font face="黑体">我是黑体字</font><br/>
<font face="微软雅黑">我是微软雅黑</font><br/>
<font face="STCAIYUN">我是华文彩云</font><br/>

## 三、引用

在引用的文字前加 <font size='5'>**>** </font>符号即可，嵌套引用加n个>符号即可。
```
> 这是引用的内容
> >这是引用引用的内容
> > > 这是引用引用引用的内容
```
效果：

> 这是引用的内容
> >这是引用引用的内容
> > > 这是引用引用引用的内容

## 四、分割线

三个或三个以上-或*均可
```
---
***
```
效果：

---
***

## 五、图片
```
语法：![图片alt](图片地址 "图片title")
![小猫](https://wx1.sinaimg.cn/large/006SQnpEly1g1nb2mbyenj30rs0fmh9k.jpg "可爱的小猫")
```
效果：
![小猫](https://wx1.sinaimg.cn/large/006SQnpEly1g1nb2mbyenj30rs0fmh9k.jpg "可爱的小猫")

## 六、超链接
```
[超链接名](超链接地址 "超链接title")
[我的博客](https://sdw2330976.github.io/ "梦回后厂村")
```
效果：

[我的博客](https://sdw2330976.github.io/ "梦回后厂村")

## 七、列表

* **无序列表**

```java
任意- + *均可
- 列表title
+ 列表title
* 列表title
```
效果：

- 列表title
+ 列表title
* 列表title

+ **有序列表**

```java
用数字加点 序号和内容之间用空格分隔
1. 第一条
2. 第二条
3. 第三条
```
效果：

1. 第一条
2. 第二条
3. 第三条

- **列表嵌套**

```
上一级和下一级之间敲三个空格即可
- 一级无序列表
    - 二级无序列表
    - 二级无序列表

- 一级无序列表
   1. 二级有序列表
   2. 二级有序列表
---
1. 一级有序列表
   - 二级无序列表
   - 二级无序列表

2. 一级有序列表
   - 二级无序列表
   - 二级无序列表
```
效果：

- 一级无序列表
    - 二级无序列表
    - 二级无序列表

- 一级无序列表
   1. 二级有序列表
   2. 二级有序列表
---
1. 一级有序列表
   - 二级无序列表
   - 二级无序列表

2. 一级有序列表
   - 二级无序列表
   - 二级无序列表

## 八、表格
```
表头|表头|表头
---:|:---:|---:
内容|内容|内容
内容|内容|内容
```
效果：

表头|表头|表头
---:|:---:|---:
内容|内容|内容
内容|内容|内容

## 九、代码
```
单行代码
`print "hello world"`
多行代码
(```)
funtion hello(){
 console.log("hello")
}
hello()
(```)
注：括号只是为了演示
```
效果:

`print "hello world"`

```
funtion hello(){
 console.log("hello")
}
hello()
```

## 十、流程图

```
(```flow)
begin=>start: begin
op=>operation: self-define operation
cond=>condition: Yes or No?
stop=>end
begin->op->cond
cond(yes)->stop
cond(no)->op
(&```)
注：括号只是为了演示
```
效果：

```flow
begin=>start: begin
op=>operation: self-define operation
cond=>condition: Yes or No?
stop=>end
begin->op->cond
cond(yes)->stop
cond(no)->op
&```


## 十一、插入音乐
```
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=573384240&auto=1&height=66"></iframe>
```
示例：

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=573384240&auto=1&height=66"></iframe>

## 十二、插入视频
```
<iframe width="560" height="315" src="https://www.youtube.com/watch?v=UBmvMLY7CEY" frameborder="0" allowfullscreen></iframe>
```
示例：

<iframe width="560" height="315" src="https://www.youtube.com/watch?v=UBmvMLY7CEY" frameborder="0" allowfullscreen></iframe>


## 十三、多种矩阵形式输入

+ **不带括号**
$$\begin{matrix}
1&2 \\  3&4 \\ 5&6
\end{matrix}
$$

+ **带大括号**
$$\left\{
\begin{matrix}
1&2 \\  3&4 \\ 5&6
\end{matrix}
\right\}
$$

+ **带中括号**
$$\left[
\begin{matrix}
1&2 \\  3&4 \\ 5&6
\end{matrix}
\right]
$$



























