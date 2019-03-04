## CSS基础
### HTML、CSS、JavaScript关系
* HTML是网页内容的载体  
* CSS样式是表现（外观控制）  
* JavaScript是行为，用来实现网页特效效果  
### 什么是CSS
* CSS `层叠样式表` （Cascading Style Sheets）  
* 用于定义HTML内容在浏览器内的显示`样式`  
### 为什么学习CSS
* CSS简化HTML相关标签，网页体积小，下载快  
* 解决内容与表现分离的问题  
* 更好的维护网页，提高工作效率  
###  CSS内容  
* CSS基础语法  
* CSS使用方法  
* CSS选择器  
* CSS继承和层叠  
* CSS优先级  
* CSS命名规范  
### CSS样式规则  
CSS规则由两部分构成：`选择器`，`声明`  
```css
h1 { color : red; font-size : 14px;}

h1: 选择器  给谁设置  
{ color : red} : 声明，每个声明有一个属性和一个值构成，属性和值之间由`:`隔开，声明后一般由`;`分隔  
```
### CSS引用
写在`<head></head>`标签内:  
```html
<style type = "text/css">
    css样式...
    h1 { color : red; font-size : 14px;}

    h1,h2,h3 {font-size : 30px}
    /* 注：14px 数字和单位之间不能有空格，否则样式不生效 */
</style>
```
### CSS注释  
* html注释：`<!-- 注释语句 -->  `
* CSS注释：`/* 注释语句 */ `
## CSS使用方法  
### 如何引入CSS样式  
* 行内样式（内联样式）
* 内部样式表（嵌入样式）
* 外部样式表（Link链入）
* 导入式  
### CSS行内样式
在开始标签内添加style样式属性  
```
    <p style="color:red;"> 内容 </p>
```
### CSS内部样式
内部样式（嵌入样式），把CSS样式代码写在：
```html
    <style type="text/css">
    样式...
        p { color : blue;}

    /* 某些低版本浏览器无法识别<style>标签,就使用HTML注释将样式包裹起来，
    不识别也不会将样式代码显示在HTML页面上
    高版本浏览器如果识别<style>标签，就会忽略html注释，读取样式内容
    */
        <!--
        p { color : blue;}
        -->
    </style>
    
    说明：<style>要放在<head>标签之间
```
### CSS外部样式
* 外部样式表，把CSS样式代码写在独立的文件中  
* 扩展名： css文件名.css  
* 引入外部文件  
```html
<head>
    <link href="xxx.css" rel="stylesheet" type="text/css"/>
</head>
```
### CSS导入式
* @import "外部CSS样式文件地址"  
* @import url(外部CSS样式文件地址)  
说明：@import 写在`<style>`标签内最开始  
### CSS使用方法区别
|类别|引入方法|位置|加载|  
|:-:|:-:|:-:|:-:|  
|行内样式|开始标签内style|html文件内|同时|
|内部样式|\<head>中\<style>内|html文件内|同时|
|外部样式|\<head>中\<link>引用|css样式文件与html文件分离|页面加载时，同时加载css样式|
|导入式 @import|在样式代码最开始出|css样式文件与html文件分离|在读取完html文件之后加载|
### 使用链入外部样式的好处  
1. CSS和HTML分离  
2. 多个文件可以使用同一个样式文件  
3. 多文件引用同一个CSS文件，CSS只需下载一次  
## CSS使用方法优先级  
对于同一个`元素`，同时使用这些方法来设置CSS样式，哪种方法才能真正的起作用呢？  
### 优先级  
` 行内样式 > 内部样式 > 外部样式 `  
说明:  
1. 链入外部样式表与内部样式表之间的优先级取决于所处位置的先后  
2. 最后定义的优先级最高（`就近原则`）  
## CSS选择器  
* 标签选择器  
* 类选择器  
* ID选择器    
* 全局选择器  
* 群组选择器  
* 后代选择器  
### 标签选择器  
以HTML标签作为选择器  
### 类选择器  
1. 为HTML标签添加class属性：  
    ```html
    <h1 class="red">标题内容</h1>
    ```
2. 通过类选择器来为具有此class属性的元素设置css样式  
    ```css
    .red { color : red;}
    ```
### ID选择器  
1. 为HTML标签添加ID属性：  
    ```html
    <h1 id="h1">标题内容</h1>
    ```
2. 通过类选择器来为具有此class属性的元素设置css样式  
    ```css
    #h1 { color : red;}
    ```
### 群组选择器  
集体统一设置样式：  
```css
    p,h1,h2,h3 {font-size : 30px}
```
**注意：** css是忽略大小写的，但是class和id的值区分大小写  
### 全局选择器  
通配符选择器  
所有标签设置样式：  
```css
* {color:red;...}
```
### 后代选择器  
若元素如下,设置`<em>`标签样式  
```html
<h1 class="head1"><em>CSS标题</em></h1>
```
```css
h1 em{color:red;}

p a em{...} /* p标签中a标签中的em */
#p1 em{...} /* id为p1的标签中的em */ 
p.red a em{...} /* class为red的p标签中的a标签中的em */
```
### 伪类  
- 伪类选择器定义特殊状态下的样式  
- 无法用标签、id、class及其它属性实现  
#### 链接伪类  
链接的4种状态：激活状态、已访问状态、未访问状态和鼠标悬停状态  
|伪类|说明|  
|:-:|:-:|
|:link|未访问的链接|  
|:visited|已访问的链接|  
|:hover|鼠标悬停状态|  
|:active|激活的链接|  
```html
<a href="www.baidu.com" target="_blank">CSS使用方法</a>
```
```css
    a:link {color : blue;} /* 未访问状态 */
    a:visited {color : gray;} /* 已访问状态 */
    a:hover {color : green;} /* 鼠标悬浮状态 */
    a:active {color : orange;} /* 激活状态 */
```
##### 伪类 :hover 和 :active  
1. `:hover`用于访问的鼠标经过某个元素时  
2. `:active`用于一个元素被激活时（即按下鼠标之后放开鼠标之前的时间）  
##### 链接伪类的顺序  
`:link` > `:visited` > `:hover` > `:active`  
**说明**  
1. `a:hover`必须置于`a:link`和`a:visited`之后，才有效  
2. `a:active`必须置于`a:hover`之后，才有效  
3. 伪类名称对大小写不敏感  
## CSS继承和层叠  
继承好处：  
1. 父元素设置样式，子元素可以继承`部分属性`  
2. 减少CSS代码  
### CSS层叠  
- 可以定义多个样式  
- 不冲突时，多个样式可层叠为一个  
- 冲突时，按不同样式规则优先级来应用样式  
### CSS优先级  
1. `id选择器` > `class选择器` > `标签选择器`  
2. 同类样式，多次引用，样式表中后定义的优先级高（就近原则）  
### CSS优先级规则  
同一样式表中：  
1. 权值相同  就近原则（离被设置元素越近，优先级越高）  
2. 权值不同  根据权值来判断CSS样式，哪种CSS样式权值高，就使用哪种样式  
#### 选择器权值  
- 标签选择器：权值为1  
- 类选择器和伪类：权值为10  
- ID选择器：权值为100  
- 通配符选择器：权值为0  
- 行内样式：权值为1000 
#### 权值规则  
- 统计不同选择器的个数  
- 每类选择器的个数乘以相应的权值  
- 把所有的值相加得出选择器的权值  
```
#main div.warning h2 {...}

id:1(#main)   class:1(.warning)  标签:2 (div h2)
1*100           1*10                2*1
权值 112
```
#### !important规则
- 可调整样式规则的优先级  
- 添加在样式规则之后，中间用空格隔开  
```css
div {color:red !important;}
```
### 总结  
- !important声明高  
- css使用方法的优先级  
    `行内样式` > `内部样式` > `外部样式`  
    注：link链入外部样式和style内部样式优先级，取决于先后顺序  
- 样式表中优先级  
    `id选择器` > `class选择器` > `标签选择器` > `通配符`  
    <table align="center">
        <tr bgcolor="#03A2D6" align="center">
            <td><font color="white">权值相同</font></td>
            <td><font color="white">权值不同</font></td>
        </tr>
        <tr align="center">
            <td>就近原则</td>
            <td>使用权值高的</td>
        </tr>
    </table>
## CSS样式命名
### CSS样式命名规则  
- 采用英文字母、数字以及"-"和"_"命名  
- 以小写字母开头，不能以数字和"-"、"_"开头  
- 命名形式：单字，连字符，下划线和驼峰  
- 使用有意义命名  
### 常用的CSS样式命名  
1）**页面结构**  
<table>
    <tr>
        <td>页头：header</td><td></td>
        <td>导航：nav</td><td></td>
    </tr>
    <tr>
        <td>页面主体：main</td><td></td>
        <td>侧栏：sidebar</td><td></td>
    </tr>
    <tr>
        <td>页尾：footer</td><td></td>
        <td>栏目：column</td><td></td>
    </tr>
    <tr>
        <td>内容：content/container</td><td></td>
        <td>页面外围控制：wrapper</td><td></td>
    </tr>
    <tr>
        <td>容器：container</td><td></td>
        <td>左右中：left right center</td><td></td>
    </tr>
</table>

2）**导航**  
<table>
    <tr>
        <td>导航：nav</td><td></td>
        <td>左导航：leftsidebar</td><td></td>
    </tr>
    <tr>
        <td>主导航：mainnav</td><td></td>
        <td>右导航：rightsidebar</td><td></td>
    </tr>
    <tr>
        <td>子导航：subnav</td><td></td>
        <td>菜单：menu</td><td></td>
    </tr>
    <tr>
        <td>顶导航：topnav</td><td></td>
        <td>子菜单：submenu</td><td></td>
    </tr>
    <tr>
        <td>边导航：sidebar</td><td></td>
        <td>标题：title</td><td></td>
    </tr>
    <tr>
        <td colspan="2"></td>
        <td>摘要：summary</td><td></td>
    </tr>
</table>

3）**功能**  
<table>
    <tr>
        <td>标志：logo</td><td></td>
        <td>注册：register</td><td></td>
    </tr>
    <tr>
        <td>广告：banner</td><td></td>
        <td>搜索：search</td><td></td>
    </tr>
    <tr>
        <td>登陆：login</td><td></td>
        <td>功能区：shop</td><td></td>
    </tr>
    <tr>
        <td>登录条：loginbar</td><td></td>
        <td>标题：title</td><td></td>
    </tr>
</table>

### id和class使用  
- id不要滥用，谨慎使用  
- 适当使用class  
