# 小白加速器破解过程
#6.18 
很多人问body怎么解密的 这里说一下方法:
原本是最后一张图的内容, 这里解释一下:
从头走的话这样这样做:
搜body, 有很多, 找到下面不远有一个postmessage 函数的body
暂停到这个上面, 
随便往上拉一下堆栈, 就可以看到明显的数据
数据跟随到左边 就可以看到解密后的数据
并没有做解密研究, 其实有加密解密内容和密匙 可以研究一下的, 比较懒, 就没看
![TIM截图20190618155527.jpg](https://i.loli.net/2019/06/18/5d08999a35ef529589.jpg)
# 5.30 
nHHpgf23ac3RSC
# 5.16 更新:
他老换密码 = =, 如果以后还如此, 不再更新密码(总是换就没什么意义了)
密码:www.xbvpn.com
推荐直接od 搜"服务到期提醒" 有2个地方
把上面的je改为jmp, 就可以屏蔽掉到期提醒
![TIM截图20190516151012.jpg](https://i.loli.net/2019/05/16/5cdd0d19bd29023378.jpg)

# 5.15 更新:
  仅更新json 文件.realse 中请自行替换
# 前言
[看又有人需要](https://www.52pojie.cn/thread-951633-1-1.html)
会编程, 但是反编译基本刚入门

# 思路
+ 1. 原贴说可以修改时间(软件通过判断本地时间是否达到服务器的用户到期时间来管理用户)达到目的
+ 1.1 通过修改服务器的返回内容来修改时间
+ 1.2 通过修改获取本地时间
+ 1.3 通过本地时间和网络时间的比较
以上上个地方任意修改一处即可

# 步骤
+ 首先无壳,  省事儿

+ 1.像这种带网络验证的... 搜http吧



+ 3.搜到两个关键函数
```
https://api.nuoyacup.com/zhifu/api/login.php
https://api.nuoyacup.com/zhifu/api/getoneuserendtime.php

```
明显一个是登录的
一个是获取时长的
![TIM截图20190509192814.jpg](https://i.loli.net/2019/05/09/5cd411b1894b5.jpg)
![TIM截图20190509193807.jpg](https://i.loli.net/2019/05/09/5cd411b1a09dc.jpg)
+ 3.1 跟踪上面的两个函数, 可以获取到参数, 以及在堆栈里面获取到具体传递的参数
![TIM截图20190509193746.jpg](https://i.loli.net/2019/05/09/5cd411b176e18.jpg)
```
https://api.nuoyacup.com/zhifu/api/login.php?user_name=XB6830504733&user_pass=9Ilb8iC7BE6/8Cn5Ok/CBQ==
https://api.nuoyacup.com/zhifu/api/getoneuserendtime.php?user_name=XB6830504733&user_pass=2okzi5

```
+ 3.1.1 分别打断点, 发现未走到```getoneuserendtime```时已经获取到了时间, 肯定是```login```的时候获取了时间, 然后```getoneuserendtime```这里是一个心跳, 实时跟新用户的时间(od中显示3000ms)

![TIM截图20190509194522.jpg](https://i.loli.net/2019/05/09/5cd413a5794e8.jpg)
![TIM截图20190509194408.jpg](https://i.loli.net/2019/05/09/5cd413a583249.jpg)
![TIM截图20190509194504.jpg](https://i.loli.net/2019/05/09/5cd413a570c60.jpg)

+ 3.1.1.1 图一的地址, 明显是密码加密了(原密码为图二) , 复制到浏览器中发现返回的是"没有该用户"..
+ 3.1.1.2 由于是.Net的估计.Net的url经过了特殊字符的处理, 继续跟踪找到原密码加密  图三 经过url编码解密之后得到上面的这个
```
https://api.nuoyacup.com/zhifu/api/login.php?user_name=XB6830504733&user_pass=9Ilb8iC7BE6/8Cn5Ok/CBQ==
```
+ 3.1.1.3 上面是url编码后的, 如果想找可以接着在堆栈找到原始的加密密码:
![TIM截图20190509194538.jpg](https://i.loli.net/2019/05/09/5cd4137fee64d.jpg)
+ 3.2 获取到上面连接的结果(直接敲的浏览器中)
```
https://api.nuoyacup.com/zhifu/api/login.php?user_name=XB6830504733&user_pass=9Ilb8iC7BE6/8Cn5Ok/CBQ==
```
+ 3.2.1 结果如下:
```
{"state":1,"msg":"\u7528\u6237\u5b58\u5728","body":"pp......","data":{"userID":238830}}
```
+ 3.2.2 明显body里面的内容加密了, 跟踪堆栈
![TIM截图20190509194212.jpg](https://i.loli.net/2019/05/09/5cd41671ad5d6.jpg)
![TIM截图20190509194226.jpg](https://i.loli.net/2019/05/09/5cd41671ae234.jpg)
![TIM截图20190509194242.jpg](https://i.loli.net/2019/05/09/5cd41671c1de9.jpg)

+ 3.2.3 找到了软件解密body之后或设置一些值:
![TIM截图20190509192755.jpg](https://i.loli.net/2019/05/09/5cd416dd0bb30.jpg)
![TIM截图20190509193943.jpg](https://i.loli.net/2019/05/09/5cd416a41d4f7.jpg)

+ 3.2.4 解密body后 本来接下来想修改body里面的日期, 然后修正一下```getoneuserendtime```的日期
# 然后发现没有必要了!!!!! 他居然把账号密码放在了Body里面!!!!!!!!!(正确的方式是放在服务器上进行控制)
![TIM截图20190511114646.jpg](https://i.loli.net/2019/05/11/5cd665a9cf73e.jpg)
+ 3.2.5 账号密码如下: 典型的shadowsocks

![TIM截图20190509200654.jpg](https://i.loli.net/2019/05/09/5cd417ea8b5cf.jpg)
+ 3.2.6 测试成功


账号密码放附件里面(居然有85个服务器 = = )





