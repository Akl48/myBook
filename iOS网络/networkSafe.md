# 数据安全

```objc
数据安全的原则
    1.在网络上"不允许"传输用户隐私数据的"明文"
    2.在本地"不允许"保存用户隐私数据的"明文"
```

## Base64编码

HTTP将Base64编码用于基本的身份认证。

可以将用户的任何输入转化成只包含特定字符的安全格式，服务于网络通信过程。

1. 原文可能是各种数据类型：文本|图片|音频|视频……

2. 在加密之前，统一格式(接口):把所有的数据类型转化成同一种类型(文本文件)，再加密处理。
   1. 原文--(base64加密)—》文本文件--》(算法)—》密文

3. 解密之后要将文本文件转化为他原来的文件
   1. 密文—》(算法)—》文本文件—》(base64解密)—》原文

进行编码之后文件体积会增加33%

### 在终端进行编码与解码

`base64 Server.js -o k.txt`

会生成一个名为k.txt的文件

`base64 k.txt -o 123.js -D`

会解码为一个叫123.js的文件

### 在OC进行base64进行编码

```objective-c
- (NSString *)base64Encoding:(NSString *)string{
//    将字符串转化成二进制数据
    NSData *data = [string dataUsingEncoding:NSUTF8StringEncoding];
//    将二进制数据进过base64编码 返回字符串
    return [data base64EncodedStringWithOptions:kNilOptions];
//    将二进制数据进过base64编码 返回NSData
//    [data base64EncodedDataWithOptions:kNilOptions];
}
```

### OC进行base64解码

```objective-c
- (NSString *)base64Dencoding:(NSString *)string{
//    先对数据进行base64解析
    NSData *data = [[NSData alloc]initWithBase64EncodedString:string options:kNilOptions];

    return [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
}
```

### 对字符串的编码

`echo -n "b"|base64`

#### 对字符串的解码

`echo -n "QQ==" |base64 -D`

## 散列函数

在注册的时候对密码进行加密，传入到后台。

登录的时候也是比较加密的密码确认是否是相同的。

### 单向散列函数

1. 对任意长度的消息散列得到散列值是定长的

2. 散列计算速度快，非常高效

3. 消息不同，则散列值一定不同

4. 消息相同，则散列值一定相同

5. 具备单向性，无法逆推计算

#### MD5

有的APP也不提供找回密码，是因为在数据库中存放的是加密之后的密码

* 对输入信息生成唯一的128位散列值(32个字符)

* 明文不同，则散列值一定不同

* 明文相同，则散列值一定相同

* 根据输出值，不能得到原始的明文，即其过程不可逆

由于MD5可能会被破解

##### 升级MD5

1. 添加salt 在明文的固定位置插入随机串，然后再进行MD5 需要足够**复杂而且长**
2. 先对明文进行MD5，然后对加密得到的MD5串的字符进行乱序
3. 先对明文字符串进行乱序处理，然后对得到的串进行加密

总之都有方法的

#### 消息验证码

1. HMAC-MAD5
2. HMAC-SHA1

先通过网络请求获取验证码 先通过认证码进行加密，再对加密之后的密文再进行散列计算

### 对称加密

对称密码中使用相同的密钥 **加密和解密的过程是可逆的**

#### 分组密码

密码算法可以分为分组密码和流密码两种

1. **分组密码每次只能处理特定长度的一组数据的一类密码算法，一个分组的比特数量就称之为分组长度**
   1. des>>3des 每次只能加密64位的明文生成64位的密文
   2. aes 高级加密标准 分组长度有128 ，192 ，256
   3. 两者都要选择分组模式 aes-128-cbc des-ecb
2. 流密码:对数据流进行连续的处理的一类算法。

#### 分组模式

ecb分组模式

又称为电子密码本模式

* 使用ECB加密的时候，相同的明文分组会被转化为相同的密文分组
* 类似于一个巨大的明文分组-》密文分组的对照表

cbc分组模式

* 在CBC中首先将明文分组和前一个密文进行异或运算，再加密
* 而第一个分组会有一个初始向量iv用来和他异或加密

### 非对称加密

1.非对称加密的特点

1. 使用公钥加密，使用私钥解密
2. 公钥是公开的，私钥保密
3. 加密处理安全，但是**性能极差**

### 数字证书

1. 简单说明
    证书和驾照很相似，里面记有姓名、组织、地址等个人信息，以及属于此人的公钥，并有认证机构施加数字签名,只要看到公钥证书，我们就可以知道认证机构认证该公钥的确属于此人
2. 数字证书的内容
    1. 公钥
    2. 认证机构的数字签名(CA|自签名)
3. 证书的生成步骤
    1. 生成私钥 openssl genrsa -out private.pem 1024
    2. 创建证书请求 openssl req -new -key private.pem -out rsacert.csr
    3. 生成证书并签名，有效期10年 openssl x509 -req -days 3650 -in rsacert.csr -signkey private.pem -out rsacert.crt
    4. 将 PEM 格式文件转换成 DER 格式 openssl x509 -outform der -in rsacert.crt -out rsacert.der
    5. 导出P12文件 openssl pkcs12 -export -out p.p12 -inkey private.pem -in rsacert.crt

4. iOS开发中的注意点
    1. 在iOS开发中，不能直接使用 PEM 格式的证书，因为其内部进行了Base64编码，应该使用的是DER的证书，是二进制格式的
    2. OpenSSL默认生成的都是PEM格式的证书
