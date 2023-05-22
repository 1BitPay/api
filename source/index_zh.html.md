---
title: API Reference

language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
  - json
  - java
  - python

toc_footers:
  - <div id="language-selector">
    <a href="index.html">English</a> |
    <a href="index_zh.html">简体中文</a>
   </div>
includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the 1BitPay API
---

# 简介

欢迎使用1BitPay API，您可以使用我们的API管理交易，自动化签名，管理资产等等，更多的功能马上到来。

# Quick Start

## 步骤

1. 创建 [API Key](#api-key)，根据需要可以设置[IP白名单](#ip)。
2. 了解[鉴权规则](#17790fda8b)及[基本信息](#b122f813d5)。
3. 使用[沙盒环境](#sandbox)进行测试。
4. 对接[获取汇率](#774ef7eb57)及[创建订单](#5ac84906d9)接口，响应[订单回调](#e9a612a743)
5. [下载私钥分片](#45d6c048a4)，了解 MPC Co-Singer [签名算法](#7b81b8ce22)的规则。
6. 定时读取[待签名的列表](#f7c93cd51a)，建议2分钟/次，并根据实际情况及时进行[签名](#8ba46c43fe)。
7. 并定时进行[资金归集](#e83639625f)，建议5分钟/次。
8. 测试完成后，联系我们正式上线。
9. <a href="https://demo.1bitpay.io" target="_blank">演示</a>

## 创建API Key

请登录商户后台, 左侧导航打开 "ApiKey"，点击新增，创建API Key ，API Key创建成功后，可以使用API Key访问 1BitPay API。

## IP白名单

如果创建 API Key 时设置了白名单，则在调用 1BitPay API 时，只允许从您设置的 IP 白名单地址发起请求。


## 鉴权规则 
- 把[公共参数](#publicparas)与业务参数合并，去除Sign参数，以及空的参数。
- 把参数集合的Key按照ASCII排序以后用“=”连接。
- 把连接好的串后面拼接商户的API Secret参数。
- 把生成的字符串做32位md5（小写）即可生成参数Sign。

## 签名示例
- 例如业务参数:

{
  "orderNo":"Or12898771811",
  "name":"John Li"
}

- 将业务参数与公共参数合并并去除Sign得到结果如下：

{
  "orderNo":"Or12898771811",
  "name":"John Li",
  "TimeStamp":1566781991111,
  "Nonce":"dnasja1N",
  "MerchantNo":"meraojiasdoa123",
  "Lang":"en",
  "SignType":"1",
  "ApiKey":"asdhuasdaosd"
}

- 将参数的Key按ASCII排序以后用“=”连接得到的结果如下：

  ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111

- 假设API Secret=merasdasd,将上一步得到的结果后拼接API Secret得到结果如下：

  ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111merasdasd

- 将上一步得到的结果进行md5即可得到Sign结果如下：

  md5(ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111merasdasd)

- 注意最后一步为32位小写md5

## 基本信息

### Base URL

  https://api.1bitpay.io


### <span id="publicparas">公共参数</span>

公共请求参数是每个接口都需要使用到的请求参数，每次请求均需要携带这些参数, 才能正常发起请求。公共请求参数的首字母均为`大写`，以此区分于普通接口请求参数，并且公共参数需要放入到Header中。

参数名 | 类型 | 描述
--------- | ----------- | -----------
Nonce | Int  | 随机6位字符或数字组合
TimeStamp | Int| 13位毫秒级时间戳
MerchantNo| String | 商户编号
SignType|Int|采取的签名类型。1:MD5；2:HASH；目前仅支持MD5
Lang|Strng | 语言。 en：英文；zh：中文
Sign|String | 签名值，具体规则详见签名规则描述
ApiKey|String|商户API Key

### 公共响应
公共响应参数如下所示，code为状态码，data为业务响应参数，message为响应结果


参数名 | 类型 | 描述
--------- | ----------- | -----------
code | Int  | 200 成功，详见状态描述
message | String| Success
data|Object| 具体根绝业务会展现不同的数据结构，详见具体业务即可



### <span id="sandbox">沙盒环境域名</span> 

  https://sandbox.1bitpay.io

  <a href="https://demo.1bitpay.io" target="_blank">演示</a>

# C2C

## 获取平台汇率

### 请求地址
POST `/api/otc/rate`

### 请求方式

- Method: POST 
- Content-Type: application/json
  
### 请求参数


参数名 | 类型 | 必要性 | 描述
--------- | ----------- |  ----------- | -----------
| cryptoCurrency  | String |Y|交易币种
| legalCurrency   | String |Y|付款币种

> 请求示例:

```json
{
  "cryptoCurrency":"USDT",
  "legalCurrency":"CNY"
}
```


### 响应参数
data参数如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
buyRate | Decimal  | 买单最新汇率
sellRate | Decimal| 卖单最新汇率 


> 响应示例:

```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "buyRate":"6.47",
    "sellRate":"6.9"
  }
}
```


## 创建订单

<aside class="notice">
 注意：cryptoAmount 和 legalAmount 传一个就可以，如两个都填，legalAmount无效，另外legalAmount不支持小数
</aside>

### 请求地址

POST `/api/otc/create`

### 请求方式
- Method: POST 
- Content-Type: application/json

### 请求参数

参数名 | 类型 | <div style="width:50px">必要性</div> | 描述
--------- | ----------- |  ----------- | -----------
| userName        | String              |Y|用户姓名
| areaCode        | String              |Y|用户手机区号
| phone           | String              |Y|用户手机号码
| syncUrl         | String              |N|同步回调地址
| asyncUrl        | String              |N|异步回调地址
| cryptoAmount    | Decimal              |Y|购买或者出售数量
|legalAmount | Int|购买或者出售的金额，这里不支持小数
| cryptoCurrency  | String                |Y|交易币种
| legalCurrency   | String                |Y|付款币种
| idCardType      | Int                   |Y| 证件类型。1：身份证；2：护照
| idCard          | String                |Y|证件号码
| kyc             | Int                   |Y|KYC级别，目前KYC级别传递2
| merchantOrderNo | String                |Y|商户订单号
| orderType       | Int                   |Y|订单类型。1：买单；2:卖单
| payWay          | Int                   |Y|支付方式。1：银行卡；2:支付宝；3:微信支付。用户卖单目前仅支持 1:银行卡
| bank            | String                |N| 用户收款开户行，卖单必填
| bankAccount     | String                |N|用户收款账户，卖单必填
| bankBranch      | String                |N|用户收款开户支行
|h5               |Bool                   |Y|收银台展现类型。true：移动端适用 ；false：PC端适用

> 请求示例:

```json
{
  "userName":"陈先生",
  "areaCode":"+86",
  "phone":"18848820305",
  "syncUrl":"https://example.com",
  "asyncUrl":"https://example.com",
  "cryptoAmount":"10",
  "cryptoCurrency":"USDT",
  "legalCurrency":"CNY",
  "idCardType":1,
  "idCard":"412627288918989891",
  "merchantNo":"mer1c92b0319ef5b794",
  "merchantOrderNo":"1231222112d8",
  "orderType":1,
  "payWay":"1",
  "bank":"建设银行",
  "bankAccount":"6217229282829299111",
  "bankBranch":"建设支行",
  "h5":false
}
```


### 响应参数

data参数如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
url | String  | 收银台地址
isNew | Bool| 是否为新订单 
orderNo|String| 订单号

>  响应示例:

```json
{
  "code": 200,
  "message": "Success",
  "data": {
      "url": "http://sanbox.1bitpay.io/1bitpay-open-api-h5/otc.html?t=02d457b0ced0576a69282054911585aa&o=335793449653514240&l=zh",
      "isNew": true,
      "orderNo": "335793449653514240"
  }
}
```

## 批量卖单

<aside class="notice">
 注意：cryptoAmount 和 legalAmount 传一个就可以，如两个都填，legalAmount无效，另外legalAmount不支持小数
</aside>

### 请求地址：

POST `/api/otc/batch/sell`

### 请求方式
- Method: POST 
- Content-Type: application/json

### 请求参数：

参数名 | 类型 | <div style="width:50px">必要性</div> | 描述
--------- | ----------- |  ----------- | -----------
| userName        | String              |Y|用户姓名
| areaCode        | String              |Y|用户手机区号
| phone           | String              |Y|用户手机号码
| syncUrl         | String              |N|同步回调地址
| asyncUrl        | String              |N|异步回调地址
| cryptoAmount    | Decimal              |N|出售数量
| legalAmount     | Int                  |N|出售金额，这里不支持小数
| cryptoCurrency  | String                |Y|交易币种
| legalCurrency   | String                |Y|付款币种
| idCardType      | Int                   |Y| 证件类型。1：身份证；2：护照
| idCard          | String                |Y|证件号码
| merchantOrderNo | String                |Y|商户订单号
| bank            | String                |Y| 用户收款开户行
| bankAccount     | String                |Y|用户收款账户
| bankBranch      | String                |Y|用户收款开户支行

> 请求示例:

```json
[
  {
    "userName":"陈先生",
    "areaCode":"+86",
    "phone":"18848820305",
    "syncUrl":"https://example.com",
    "asyncUrl":"https://example.com",
    "cryptoAmount":"10",
    "cryptoCurrency":"USDT",
    "legalCurrency":"CNY",
    "idCardType":1,
    "idCard":"412627288918989891",
    "merchantNo":"mer1c92b0319ef5b794",
    "merchantOrderNo":"1231222112d8",
    "bank":"建设银行",
    "bankAccount":"6217229282829299111",
    "bankBranch":"建设支行"
  },
  {
    "userName":"陈先生",
    "areaCode":"+86",
    "phone":"18848820305",
    "syncUrl":"https://example.com",
    "asyncUrl":"https://example.com",
    "cryptoAmount":"10",
    "cryptoCurrency":"USDT",
    "legalCurrency":"CNY",
    "idCardType":1,
    "idCard":"412627288918989891",
    "merchantNo":"mer1c92b0319ef5b794",
    "merchantOrderNo":"1231222112d8",
    "payWay":"1",
    "bank":"建设银行",
    "bankAccount":"6217229282829299111",
    "bankBranch":"建设支行"
  }
]
```


### 响应参数

data参数如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
batchNo|String| 批次号
merchantOrderNos|List | 商户号
>  响应示例:

```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "batchNo":"76181192882",
    "merchantOrderNos":[
      "335793449653514241",
      "335793449653514242"
    ]
  }
  
}
```


## 订单回调

### 请求地址

POST [创建订单](#5ac84906d9)接口定义的`asyncUrl`

### 请求方式
- Method: POST 
- Content-Type: application/json

### 请求参数

参数名 | 类型 | <div style="width:50px">必要性</div> | 描述
--------- | ----------- |  ----------- | -----------
| status        | Int              |Y|订单状态， -2：撮合失败；-1：已取消；9：已完成
| dealAmount    | Decimal          |Y|购买或者出售数量
| merchantOrderNo | String         |Y|商户订单号
| orderNo | String                 |Y|1BitPay 订单号
| orderType       | Int            |Y|订单类型。1：买单；2:卖单
| signature       | String         |Y|签名：详见 [鉴权规则](#17790fda8b)

> 请求示例:

```json
{
  "status":1,
  "dealAmount":10.11,
  "orderNo":"1231222112d8123",
  "merchantOrderNo":"1231222112d8",
  "orderType":1,
  "signature":"12312asdjaisldajlsdkasasdajksdjkasjdkas"
}
```


### 响应参数

data参数如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
code | Int  | 200 成功，其他状态码均认为失败
message | String| Success 

>  响应示例:

```json
{
  "code": 200,
  "message": "Success"
}
```

<aside class="notice">
  回调规则：1BitPay 收到商户返回 code 为 "200" 的响应则认为回调成功，否则认为回调失败，并在12小时内重复回调，超过12小时将不再回调，重复回调间隔时间为：
  第2分钟，第5分钟，第10分钟，第30分钟，第60分钟，第90分钟，第2个小时，第3个小时，第4个小时，第5个小时，第6个小时，第7个小时，第8个小时，第9个小时，第10个小时，第11个小时，第12个小时。
</aside>


# MPC Co-Signer

## 概述
  MPC 全称为 Security Multi-Party Computation（[安全多方计算](https://zh.wikipedia.org/wiki/安全多方计算)）。通过 MPC 可以做到从生成、使用、存储整个生命周期中，私钥可用不可见。1BitPay 使用 MPC 技术将传统的单私钥变成了分布式的私钥分片，可以有效避免单私钥带来的单点风险，实现团队多人共同管理资金，并支持社交恢复。详细的私钥管理方案可参考 [私钥管理方案](https://support.1bitpay.io/v/zh/mpcwalletcn/key-management-cn).

  <aside class="notice">
  MPC Co-Signer 可以允许通过API自动实现交易批准和签署交易，取代使用移动设备手动审批，非常适合频繁交易的场景，实现自动化审批及签名。
  </aside>

## 下载私钥分片

  根据1BitPay[私钥管理方案](https://support.1bitpay.io/v/zh/mpcwalletcn/key-management-cn)，用户创建钱包后，私钥分为三部分，分片1保存在用户设备，分片2备份到iCloud 或 Google Drive，分片3保存在1BitPay SGX可信服务器，当您需要使用 MPC Co-Signer功能时，需要在商户后台 -> ApiKey模块，申请下载私钥分片，APP确认审批后，允许下载加密后的存储在用户设备上的私钥分片1。

  <aside class="warning">
  私钥分片虽然不是完整的私钥，但是也是资产管理重要的一部分，建议专人操作，并部署在安全环境。
  </aside>

## 签名算法

参与签名的参数如下：

参数名 | 类型 | 必要性 | 描述
--------- | ----------- |  ----------- | -----------
| id             | Int       |Y|待签名列表id
| from           | String    |N|待签名列表fromAddress：转出地址
| to             | String    |N|待签名列表toAddress：转入地址
| value          | String    |N|待签名列表amount： 转入金额
| chainId        | String    |N|待签名列表chainId: 链id
| status         | String    |Y|签名状态. 1：通过；2:拒绝

### 签名步骤：

- 获取商户MPC私钥，从商户后台下载即可
- 用MPC私钥对参数的JSON结构的字符串进行加密。
- 将上一步中生成的即为签名参数data，同业务数据一并传入到apprvoe接口进行交易签名即可


### 签名示例：

- 例如id为1的待签名数据signObject的json结构如下:

signObject:{
  "id":1,
  "from":"TGGh3cGp9P21Ebvg9JitjHoeJaKqrg3bRg",
  "to":"TFMQrPdFWuPzFRXb42sxB22ABCVL6xSopV",
  "value":"4.1",
  "chainId":"56",
  "status":1
}

- 将上一步的json结构转json字符串得到signObjectString

signObjectString = toString(signObj)

- 此时用私钥文件对signObjectString字符串加密得到data

data = "HUSDISJDSNDJSJDKSDJSIDJISOADIASLJDALSIDJISALDHAUSIDHA\ASDUAKSD|ADSADAdasdYGYASDHASDJASID"

<aside class="notice">
说明：
<br>
&emsp;&emsp;1、私钥的密码为"1bitpay"+商户的编号(商户系统 --> 系统设置 获取)
<br>
&emsp;&emsp;2、加密类型采用PKCS12
<br>
&emsp;&emsp;3、加密时均采用UTF-8编码
</aside>


```python
import base64
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives.serialization import pkcs12
pfx_password = "1bitpay" + 商户编号
srcSouse = "待加密的数据"
with open("/1bitpay.pfx", "rb") as f:
  pfx, *_ = pkcs12.load_key_and_certificates(f.read(), pfx_password.encode())
cipher = Cipher(algorithms.TripleDES(pfx[:24]), modes.ECB()).encryptor()
bytes = cipher.update(srcSouse.encode()) + cipher.finalize()
data = base64.b64encode(pfx.sign(bytes, padding.PKCS1v15(), pfx.private_bytes(
    encoding=serialization.Encoding.DER,
    format=serialization.PrivateFormat.PKCS8,
    encryption_algorithm=serialization.NoEncryption(),
))).decode()
```

```java
import java.io.InputStream
import java.crypto.Cipher;
import java.security.KeyStore;
import java.security.PrivateKey;
import sun.misc.BASE64Encoder;

InputStream inputStream = new FileInputStream("/1bitpay.pfx"); // 将私钥文件转文件流
KeyStore  store = KeyStore.getInstance("PKCS12", "BC"); // 定义密钥存储
store.load(inputStream, pfxPassword.toCharArray()); // 加载私钥，pfxPassword = “1bitpay”+商户编号
PrivateKey privateKey = (PrivateKey) store.getKey("1BitPay", pfxPassword.toCharArray()) // 获取私钥对象
Cipher cipher = Cipher.getInstance(privateKey.getAlgorithm()); // 获取私钥Cipher对象
cipher.init(Cipher.ENCRYPT_MODE, privateKey);  // 初始化Cipher
byte[] bytes = cipher.doFinal(srcSouse.getBytes("UTF-8")); // 开始加密
String data = encryptBASE64(bytes); // base64编码即得签名串
```


## 获得待签名列表 

### 请求地址

POST `/api/transaction/pending`

### 请求方式
- Method: POST 
- Content-Type: application/json
  
### 请求参数

参数名 | 类型 | 必要性 | 描述
--------- | ----------- |  ----------- | -----------
| pageNum        | Int                 |Y|当前页码
| pageSize        | Int                |Y|每页数量

> 请求示例:

```json
{
  "pageNum":1,
  "pageSize":20
}
```

### 响应参数

data 结构如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
total | Int  | 总数量
list | List| 待签名list
list 结构如下：

参数名 | 类型 | 描述
--------- | ----------- | -----------
id | Int  | 唯一标识
fromAddress | String| 转入地址
toAddress | String| 转出地址
amount|Decimal|交易金额
chainId|String|链ID

>  响应示例:

```json
{
  "code": 200,
  "message": "Success",
  "data": {
     total:25,
     list:[
      {
        "id":1,
        "fromAddress":"TGGh3cGp9P21Ebvg9JitjHoeJaKqrg3bRg",
        "toAddress":"TFMQrPdFWuPzFRXb42sxB22ABCVL6xSopV",
        "amount":"4.1",
        "chainId":"56"
      }
     ]
  }
}
```
 
## 签名 

### 请求地址

POST `/api/transaction/approve`

### 请求方式
- Method: POST 
- Content-Type: application/json
  
### 请求参数

参数名 | 类型 | 必要性 | 描述
--------- | ----------- |  ----------- | -----------
| id          | Int       |Y|待签名列表id
| from        | String    |N|待签名列表fromAddress，转出地址
| to          | String    |N|待签名列表toAddress，转入地址
| value       | String    |N|待签名列表amount，转入金额
| chainId     | String    |N|待签名列表chainId，链id
| status      | String    |Y|签名状态. 1：通过；2:拒绝
| data         |String    |Y|签名数据，使用私钥分片进行签名，参考[签名算法](#7b81b8ce22)

> 请求示例:

```json
{
  "id":1,
  "from":"TGGh3cGp9P21Ebvg9JitjHoeJaKqrg3bRg",
  "to":"TFMQrPdFWuPzFRXb42sxB22ABCVL6xSopV",
  "value":"67.2",
  "chainId":"20",
  "status":1,
  "data":"dahsudiasdoaasidoasdaosdasd9as8d9a0s8d90as8d9a0s8d09asduashdkasdjaksdajksdasjdhakjdha"
}
```


>  响应示例:

```json
{
  "code": 200,
  "message": "Success"
}
```

## 资金归集

<aside class="notice">
 注意：建议商户每5分钟调用一次该接口进行资金归集，否则可能影响正常交易
</aside>


### 请求地址

POST `/api/transaction/assets/collect`

### 请求方式

- Method: POST 
- Content-Type: application/json
 
### 请求参数

参数名 | 类型 | <div style="width:50px">必要性</div> | 描述
--------- | ----------- |  ----------- | -----------
| isMain          | Int    |Y|归集的是否是主链币。1：是；0：否
| data            |String  |Y|签名数据，使用私钥分片进行签名，参考[签名算法](#7b81b8ce22)。这里加密的业务参数为:{"isMain":1}
 
> 请求示例:

```json
{
  "isMain":1,
  "data":"HDOASJDISADLASDSAODASIDJAISLDJIASDU|ASDUADSASDIASDASDASDASLDASDJASDADOAAS"
}
```


>  响应示例:

```json
{
  "code": 200,
  "message": "Success"
}
```

# MPC 钱包

MPC SaaS 钱包，马上到来！

