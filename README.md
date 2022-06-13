# incomechart
一个统计工资单的表格，支持各OS。

喜欢把厂里的工资单收集整理起来。但是在不同厂里打工的工资条格式不一样，甚至厂里的系统升级也导致工资条格式不一样。于是诞生了这个项目：将工资单的原始格式保留到表格，通过用户自己的设置生成统一个的表。最好再来个分析功能，如可视化等。

Excel只支持VBC，写起来太累。[LibreOffice](https://www.libreoffice.org/)做为替代品，功能强大且支持其他语言的宏。我就用Python写了个项目。


## 安装
1. [LibreOffice](https://www.libreoffice.org/)当然是要装的。
2. 可能会要求安装java（反正windows要求了，Mac和Ubuntu没看到要求，没可能是自带吧）
然后直接下载`example.ods`文件就能用了。注意打开的时候提示什么宏安全要放行。

## 使用
`example.ods`里面的数据对应这样一个人物设定：2019年3月入职“丐帮科技有限公司”（简称“丐帮”），工作到2020年4月离职，同月（2020年4月）入职“昆仑派”至今。但是昆仑派由于工资单系统升级，在2022年2月进行了排版调整。

打开`example.ods`可以一共看到6个sheet:`Overview`,`丐帮`,`昆仑派1`,`昆仑派2`,`SheetFormatSetting`和`debug`。它们被分为4类：

### 1.原始工资单
其中3个sheet:“丐帮”，“昆仑派1”和“昆仑派2”就是原始工资单。昆仑派有2个sheet是因为系统升级，工资单的格式不一样。你可以根据自己的需求继续增加，什么昆仑派3啊，明教1啊等等。每个月发了工资条就复制粘贴过来即可。

### 2. 工资单格式设定(SheetFormatSetting)
打开可以看到一个设置表，如下

SheetName | paydate | paydateformat |银行实发| 税前 |
------    | ------  | ------        |------ |------|
丐帮      | a       | %Y.%m         |AG     | J    |
昆仑派1   | a       | %Y%m          |u      | M    |
昆仑派2   | a       |%Y-%m-%d       |D      |E     |

黄色字段（SheetName，paydate）是固定的，不能更改。绿色字段（银行实发，税前）是自己设定的，可以继续在后面添加。

以`丐帮`这一行为例：打开原始数据可以看到发薪日（paydate）的数据在A列，所以`paydate`下面填写`a`，大小写无所谓。然后要告诉程序这个日期的格式是什么样子的。按照Python中datetime的[规则](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes),形如2019.03的日期定义为`%Y.%m`。接下来是自定义的部分。我认为比较重要的需要统计的量，第一个我叫做`银行实发`,去`丐帮`sheet查看该数值填写在AG列，所以填入`AG`。当然我觉得税前收入也很重要，也定义了`税前`。你可以继续在后面添加列。

另外可以看到这个sheet下还有乱七八糟其他的信息，只要这些信息不是挨着表格的就行。你自己也能做一些笔记。重点是保证需要读取的信息表是顶头的，且最右边一列和最下面一行是空的。

### 3. 统计界面(Overview)
内容如下  

paydate |	12MonthAverage(银行实发) |	银行实发 |	税前 |
--------|-------------------------|----------|-------|
2019-03 |	None                    |	200      |	456  |
2019-04	| None                    |	200      |	456  |
...     |  ...                    | ...      |  ...  |

同理，黄色的字段不要改，绿色的字段是跟`SheetFormatSetting`中对应的，数据从原始工资单里面提取后自动填写到这里。看到右边有几个按钮。
- `autoFill`: 这个是最重要的，自动填写数据。数据填写之后才能用其他功能。以下其他的按钮都是其他的统计功能，之后都会再做调整。
- `mean12`: 计算一个12个月量的平均值我觉得是挺重要的，数据会填入第二列(你点一下试试)
- `Plot`：可视化的统计。这个还是测试功能，需要安装`matplotlib`。目前只在Windows上安装成功了，Mac 和Ubuntu上安装不正确报错。出错的原因大约是`numpy`安装的路径有些问题。Windows版的LibreOffice自带一个python，可以安装独立的py库。Linux上用自带的Python，导致路径乱七八糟。不过应该可以解决，以后再想办法。如果你用的Windows且想用这个功能，参考下面的方法安装`matplotlib`。如果运行成功，结果会保存到桌面。

### 4. 开发测试`debug`
测试用的。对你没啥用，可删掉。

## Windows 安装`matplotlib`
1. 找到LibreOffice的安装目录下的`program`,我电脑上的路径是`C:\Program Files\LibreOffice\program`。以下都在该目录下操作
2. 将网页[get-pip](https://bootstrap.pypa.io/get-pip.py)上的内容复制粘贴保存到该目录下文件`get-pip.py`.
3. 打开cmd，cd 到`program`目录下。跑`python.exe get-pip.py`，等待pip安装完成。
4. 继续跑`python.exe -m pip install matplotlib`，等待matplotlib安装完成。
