---
title: SSL协议配置及分析
date: 2018-12-9
tags: TLS
---
## SSL协议配置及分析

### 写一个网页和后台
编写一个简单的网页，输入账号密码，并通过php打印出来。
html代码:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="SSLtest.css">
    <title>SSLtest</title>
</head>
<body>
    <form action="SSLtest.php" method="POST">
        <div id="div1">
            <p><span>用户名</span><input type="text" name="userName" required>(必须填写)</p>
            <p><span>密码</span><input type="password" name="password" required>(必须填写)</p>
            <p><span>邮箱</span><input type="email" name="myemail"></p>
        </div>
        <div id="but">
            <input type="submit" name="submit" value="提交">
        </div>
    </form>
</body>
</html>
```
php代码:
```
<?php
$username=$_POST['userName'];
$password=$_POST['password'];
$myemail=$_POST['myemail'];
if(isset($username))
{echo '用户名:'.$username.'<br>';}
if(isset($password))
{echo  '密码:'.$password.'<br>';}
if(isset($myemail))
{echo  '邮箱:'.$myemail.'<br>';}
?>
```
### 通过服务器访问
将代码放入服务器，并访问。我的是阿里云服务器，apache+php。
访问效果如图:
![](/images/0063dFB6gy1fxuzbh9e1wj30ox0d93z6.jpg)
响应如图：
![](/images/0063dFB6gy1fxuzc7dpdwj30gn07uaag.jpg)
### 使用wireshark抓包
通过ipconfig查询到本机ip:192.168.31.164
服务器ip为:47.107.174.125
配置wireshark过滤规则:
![](/images/0063dFB6gy1fxv031rjnpj30eh00z741.jpg)
服务器向本机发送的网页:
![](/images/0063dFB6gy1fxv021fjhij31cf0obtb5.jpg)
可以看出是明文发送
通过表单提交的账号信息：
![](/images/0063dFB6gy1fxv05mj69zj317h0jdabs.jpg)
显然是明文传输，很容易就被抓包获取。
### 在服务器配置SSL
- 安装mod_ssl openssl
`yum install mod_ssl openssl`
- 生成私钥
![](/images/20181205151113.png)
- 生成证书签名请求
![](/images/20181205151311.png)
- 生成自签名证书
![](/images/20181205151436.png)
- 创建文件夹，用来存放私钥和证书
![](/images/20181205152524.png)
- 将证书和私钥转移到ssl文件夹相应位置
![](/images/20181205152533.png)
- 更新Apache ssl配置文件
路径为/etc/httpd/conf.d/ssl.conf
![](/images/20181205154702.png)
- 重启apache服务
![](/images/20181205153540.png)
重启后ssl在全站起用
### 配置ssl后测试
- 使用chrome登录测试网页
![](/images/20181205153514.png)
浏览器显示不安全，因为是自签发的证书，没有经过CA机构签发，继续访问就是了。
- 输入数据进行发送测试
![](/images/20181205154040.png)
- 服务器响应
![](/images/20181205154026.png)
- 使用wireshark抓包
![](/images/20181205154016.png)
可以看出发送数据的数据包协议为TLSv1.2,就是ssl。
下面的数据内容是加密后的，无法看到明文。
### 实验心得
http使用明文传输数据，容易被拦截，造成信息泄露。
https是在http基础上使用了TLS(SSL)进行加密传输，SSL可使用RSA非对称算法进行加密，这样只有通信的两端可以解析数据。
公共的网站可以自己生成一个私钥(key)，然后通过私钥生成一个证书签名请求(csr)，一般来说将证书签名请求发给CA机构(公共的证书签发机构)进行申请证书，也可以自己通过工具生成自签名证书。
证书签发：
- 首先撰写证书的元信息：签发人，域名，时间，有效期等，还要附加签发人的公钥。
- 通过Hash算法将信息摘要提取。
- Hash提取的摘要通过CA的私钥进行加密，生成密文。
- 将密文附在签发信息上，成为一个CA签名过的证书。
验证证书：
- 浏览器获得证书
- 解压后得到元数据和签名密文
- 用Hash算法将元数据提取摘要
- 通过CA机构的公钥解密获得加密的摘要值
- 如果两个摘要匹配，则说明这个证书是被CA验证过签发的合法证书，里面的公钥等信息是可信的
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fyd12m725jj30m80gjgnm.jpg)

自签名证书不被浏览器所信任，因为自签名证书由于可被假冒伪造等原因不安全。

操作系统中有受信任的根证书库
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fyd0r4zb3yj30lq0ffwik.jpg)
其中存放操作系统帮我们下载好的一些国际上可信的知名的证书颁发机构CA的根证书。(如果证书机构完蛋了，操作系统也会把它的根证书删除)
CA机构一般通过签发多个二级证书，再用二级证书给申请证书的人/公司签发证书。
![](https://ws1.sinaimg.cn/large/0063dFB6gy1fyd0vubvrtj30j80audhf.jpg)
被签发的证书中会含有它的签发上级的证书信息，由此可以得到一个证书链，如果我们信任了它的根证书，通过根证书的公钥解密二级证书(hash)，验证二级证书的完整性，成功则可信，如此一级级验证下去，最终确认所访问网站是可信的。

用户可以手动信任(测试用)网站。
通过TLS加密后，在中间嗅探无法直接看到明文的账号密码，安全很多啊