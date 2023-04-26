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
  - <br>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the 1BitPay API
---

# Introduction

Welcome to 1BitPay API, you can use our API to manage transactions, automate signatures, manage assets, etc., and more functions will come soon.

# Quick Start

## Step by step

1. Create an [API Key](#api-key), [IP whitelist](#ip-whitelist) can be set as needed.
2. Learn about [API Authentication](#api-authentication) and [General Info](#general-info).
3. Use [Sandbox](#sandbox) Environment for testing
4. Access the interface of [Get Rate](#get-rate) and [Create Order](#create-order).
5. [Download the PK Shard](#download-the-pk-shard), Learn about the rules of the MPC Co-Singer [Signature Algorithm](#signature-algorithm).
6. Read the [list to be signed](#get-list-of-pending-signatures) regularly, 2 minutes/time is recommended, and sign in time according to the actual [situation](#sign).
7. And regularly [collect funds](#fund-collection), it is recommended to 5 minutes / time
8. After the test is completed, contact us to release the live.
9. <a href="https://demo.1bitpay.io" target="_blank">Demo</a>


## Create an API Key

Please log in to the merchant system, open "ApiKey" in the left navigation, click Add, and create an API Key. After the API Key is successfully created, you can use the API Key to access the 1BitPay API.

## IP whitelist

If you set a whitelist when creating the API Key, when calling the 1BitPay API, only requests from the IP whitelist addresses you set are allowed.

## API Authentication 

- Merge [public parameters](#publicparas) with business parameters, remove Sign parameters, and empty parameters.
- After sorting the Keys of the parameter set according to ASCII, connect them with "=".
- Splice the concatenated string with the merchant's API Secret parameter.
- Make the generated string into 32-bit md5 (lowercase) to generate the parameter Sign.

## Authentication example

- For example business parameters:

{
   "orderNo": "Or12898771811",
   "name":"John Li"
}

- Merge business parameters with public parameters and remove Sign to get the following results:

{
   "orderNo": "Or12898771811",
   "name": "John Li",
   "TimeStamp": 1566781991111,
   "Nonce": "dnasja1N",
   "MerchantNo": "meraojiasdoa123",
   "Lang": "en",
   "SignType": "1",
   "ApiKey": "asdhuasdaosd"
}

- After the Keys of the parameters are sorted by ASCII, the result obtained by connecting them with "=" is as follows:

   ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111

- Assuming that API Secret=merasdasd, the result obtained in the previous step is stitched together with the API Secret to get the result as follows:

   ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111merasdasd

- Perform md5 on the result obtained in the previous step to get the Sign result as follows:

   md5(ApiKey=asdhuasdaosd&Lang=en&MerchantNo=meraojiasdoa123&name=John Li&Nonce=dnasja1N&orderNo=Or12898771811&SignType=1&TimeStamp=1566781991111merasdasd)

- Note that the last step is 32-bit lowercase md5
  

## General Info

### Base URL *

  https://api.1bitpay.io

### <span id="publicparas">Public Parameters</span>

The public request parameters are the request parameters that each interface needs to use. Each request needs to carry these parameters in order to initiate the request normally.The initial letters of the public request parameters are `uppercase`,In this way, it is distinguished from ordinary interface request parameters, and public parameters need to be placed in the Header.

Parameters | Type | Description
--------- | ----------- | -----------
Nonce | Int  | Random combination of 6 characters or numbers
TimeStamp | Int| 13-digit millisecond-level timestamp
MerchantNo| String | Merchant ID
SignType|Int|The signature type to take. 1: MD5; 2: HASH; currently only supports MD5
Lang|Strng | language. en: English; zh: Chinese
Sign|String | Signature value, see signature rule description for specific rules
ApiKey|String| Merchant API Key

### Public Response

The public response parameters are as follows, code is the status code, data is the business response parameter, and message is the response result


Parameters | Type | Description
--------- | ----------- | -----------
code | Int  | 200 Success, See status description for details
message | String| Success
data|Object| The specific business will display different data structures, please refer to the specific business for details


### Sandbox Url

  https://sandbox.1bitpay.io

  <a href="https://demo.1bitpay.io" target="_blank">Demo</a>

# C2C 

## Get Rate

### HTTP Request

POST `/api/otc/rate`

### Request Method

- Method: POST 
- Content-Type: application/json
  
### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| cryptoCurrency  | String |Y|Transaction currency
| legalCurrency   | String |Y|Payment currency

> Request example:

```json
{
  "cryptoCurrency":"USDT",
  "legalCurrency":"CNY"
}
```


### Response Parameters

The data parameter is as follows:

Parameters | Type | Description
--------- | ----------- | -----------
buyRate | Decimal  | The latest rate for buy orders
sellRate | Decimal | The latest rate for sell orders


> The above command returns JSON structured like this:

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

## Create Order

### HTTP Request

POST `/api/otc/create`

### Request Method

- Method: POST 
- Content-Type: application/json

### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| userName        | String              |Y|User name
| areaCode        | String              |Y|User phone area code
| phone           | String              |Y|User phone number
| syncUrl         | String              |N|Synchronous callback address
| asyncUrl        | String              |N|Asynchronous callback address
| cryptoAmount    | Decimal              |Y|Buy or sell quantity
| cryptoCurrency  | String                |Y|Transaction currency
| legalCurrency   | String                |Y|Payment currency
| idCardType      | Int                   |Y| Type of certificate. 1: ID card; 2: Passport
| idCard          | String                |Y|ID number
| kyc             | Int                   |Y|KYC level, currently KYC level pass 2
| merchantOrderNo | String                |Y|Merchant order number
| orderType       | Int                   |Y|Order Type. 1: buy ; 2: sell 
| payWay          | Int                   |Y|payment method. 1: bank card; 2: Alipay; 3: WeChat payment. User sell order currently only supports 1: bank card
| bank            | String                |N| Account opening bank for user's receiving payment, required for selling orders
| bankAccount     | String                |N|The user's receiving account, which is required for selling orders
| bankBranch      | String                |N|Account opening sub-branch for user collection
|h5               |Bool                   |Y|Cash register display type. true: mobile ; false: PC 

> Request example:

```json
{
  "userName":"Chen",
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


### Response Parameters

The data parameter is as follows:

Parameters | Type | Description
--------- | ----------- | -----------
url | String  | Cash register address
isNew | Bool| Is it a new order 
orderNo|String| Order number

>  The above command returns JSON structured like this:

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

## Order Callback

### HTTP Request：

POST `asyncUrl` defined by the [Create Order](#create-order)

### Request Method
- Method: POST 
- Content-Type: application/json

### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| status        | Int              |Y| Order status -2: Matching failed -1: Canceled 0: Initialized 1: Completed 2: In progress 3-8: Processing 9: Completed
| dealAmount    | Decimal          |Y|buy or sell amount
| merchantOrderNo | String         |Y|Merchant order number
| orderNo | String                 |Y|1BitPay order number
| orderType       | Int            |Y|Order Type. 1: buy; 2: sell
| signature       | String         |Y|Signature: See [API Authentication](#api-authentication) for details

> Request example:

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


### Response Parameters

The data parameter is as follows:

Parameters | Type | Description
--------- | ----------- | -----------
code | Int  | 200 Success, other codes are considered failure
message | String| Success 

>  The above command returns JSON structured like this:

```json
{
  "code": 200,
  "message": "Success"
}
```

<aside class="notice">
  Callback rules: If 1BitPay receives a response from the merchant with a code of 200, the callback will be considered successful; otherwise, the callback will be considered a failure, and the callback will be repeated within 12 hours, and no more callbacks will be made after 12 hours. The interval between repeated callbacks is:
   Minute 2, Minute 5, Minute 10, Minute 30, Minute 60, Minute 90, Hour 2, Hour 3, Hour 4, Hour 5, Hour 6, Hour 7th hour, 8th hour, 9th hour, 10th hour, 11th hour, 12th hour.
</aside>


# MPC Co-Signer

## Overview

  The full name of MPC is [Secure Multi-Party Computation](https://en.wikipedia.org/wiki/Secure_multi-party_computation). Through MPC, the private key is available and invisible throughout the entire life cycle from generation, use, and storage. 1BitPay uses MPC technology to transform the traditional single private key into distributed private key sharding, which can effectively avoid the single-point risk brought by a single private key, realize the joint management of funds by multiple people in the team, and support social recovery. For detailed private key management scheme, please refer to [Private key management](https://support.1bitpay.io/mpcwallet/key-management).

  <aside class="notice">
  MPC Co-Signer can allow automatic transaction approval and signing transactions through API, replacing manual approval with mobile devices. It is very suitable for frequent transaction scenarios and realizes automatic approval and signature.
  </aside>

## Download the PK Shard

  According to 1BitPay [Private key management](https://support.1bitpay.io/mpcwallet/key-management), after the user creates a wallet, the private key is divided into three parts, and the fragment 1 is stored in the user Device, PK Shard 2 is backed up to iCloud or Google Drive, PK Shard 3 is saved in 1BitPay SGX trusted server, when you need to use the MPC Co-Signer function, you need to apply for downloading the private key PK Shard in the merchant background -> ApiKey module, After the APP confirms the approval, it is allowed to download the encrypted private key fragment 1 stored on the user device.


  <aside class="warning">
  Although private key sharding is not a complete private key, it is also an important part of asset management. It is recommended to be operated by a dedicated person and deployed in a secure environment.
  </aside>

## Signature Algorithm 

The parameters involved in the signature are as follows:

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| id             | Int       |Y|Pending signature list id
| from           | String    |N|Pending signature list fromAddress: Transfer-out address
| to             | String    |N|Pending signature list toAddress: transfer address
| value          | String    |N|Pending signature list amount: Transfer amount
| chainId        | String    |N|Pending signature list chainId: chainId
| status         | String    |Y|Sign status. 1: approved; 2: rejected

### Signature steps:

- Obtain the merchant MPC private key and download it from the merchant background
- Encrypt the string of the parameter JSON structure with the MPC private key.
- The signature parameter data generated in the previous step is passed to the apprvoe interface together with the business data for transaction signature


### Signature example:

- For example, the json structure of the data signObject whose id is 1 is as follows:

signObject:{
  "id":1,
  "from":"TGGh3cGp9P21Ebvg9JitjHoeJaKqrg3bRg",
  "to":"TFMQrPdFWuPzFRXb42sxB22ABCVL6xSopV",
  "value":"4.1",
  "chainId":"56",
  "status":1
}

- Convert the json structure in the previous step to json string to get signObjectString

signObjectString = toString(signObj)

- At this time, use the private key file to encrypt the signObjectString string to obtain data

data = "HUSDISJDSNDJSJDKSDJSIDJISOADIASLJDALSIDJISALDHAUSIDHA\ASDUAKSD|ADSADAdasdYGYASDHASDJASID"

<aside class="notice">
Explanation
<br>
&emsp;&emsp;1. The password of the private key is "1bitpay" + the merchant's ID (Obtain from Merchant System --> System Settings)
<br>
&emsp;&emsp;2. The encryption type adopts PKCS12
<br>
&emsp;&emsp;3. UTF-8 encoding is used for encryption
</aside>


```python
import base64
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives.serialization import pkcs12
pfx_password = "1bitpay" + Merchant ID
srcSouse = "data to be encrypted"
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

InputStream inputStream = new FileInputStream("/1bitpay.pfx"); // Convert private key file to file stream
KeyStore  store = KeyStore.getInstance("PKCS12", "BC"); // Define the key store
store.load(inputStream, pfxPassword.toCharArray()); // Load the private key, pfxPassword = "1bitpay" + merchant ID
PrivateKey privateKey = (PrivateKey) store.getKey("1BitPay", pfxPassword.toCharArray()) // Get the private key object
Cipher cipher = Cipher.getInstance(privateKey.getAlgorithm()); // Get the private key Cipher object
cipher.init(Cipher.ENCRYPT_MODE, privateKey);  // Initialize Cipher
byte[] bytes = cipher.doFinal(srcSouse.getBytes("UTF-8")); // start encryption
String data = encryptBASE64(bytes); // base64 encoding to get the signature string
```

## Get list of pending signatures 

### HTTP Request

POST `/api/transaction/pending`

### Request Method
- Method: POST 
- Content-Type: application/json
  
### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| pageNum        | Int                 |Y|Page number
| pageSize        | Int                |Y|Page size

> Request example:

```json
{
  "pageNum":1,
  "pageSize":20
}
```

###  Response Parameters

The data structure is as follows:

Parameters | Type | Description
--------- | ----------- | -----------
total | Int  | The total amount
list | List| Pending signature list

The list structure is as follows:

Parameters | Type | Description
--------- | ----------- | -----------
id | Int  | ID
fromAddress | String| from Address
toAddress | String| to Address
amount|Decimal|Amount of the transaction
chainId|String|Chain Id

>  The above command returns JSON structured like this:

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
 
## Sign 

### HTTP Request

POST `/api/transaction/approve`

### Request Method
- Method: POST 
- Content-Type: application/json
  
### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| id             | Int       |Y|Pending signature list id
| from           | String    |N|Pending signature list fromAddress: Transfer-out address
| to             | String    |N|Pending signature list toAddress: transfer address
| value          | String    |N|Pending signature list amount: Transfer amount
| chainId        | String    |N|Pending signature list chainId: chainId
| status         | String    |Y|Sign status. 1: approved; 2: rejected
|data            |String     |Y|Signature data, use private key shard for signing, refer to [Signature Algorithm](#signature-algorithm).
> Request example:

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


>  The above command returns JSON structured like this:

```json
{
  "code": 200,
  "message": "Success"
}
```

## Fund collection

<aside class="notice">
 Note: It is recommended that merchants call this interface every 5 minutes for fund collection, otherwise normal transactions may be affected
</aside>


### HTTP Request

POST `/api/transaction/assets/collect`

### Request Method

- Method: POST 
- Content-Type: application/json
 
### Query Parameters

Parameters | Type | Required | Description
--------- | ----------- |  ----------- | -----------
| isMain          | Int    |Y|Whether the collection is the main chain currency. 1: yes; 0: no
| data            |String  |Y|Signature data, use private key shard for signing, refer to [Signature Algorithm](#signature-algorithm). The encrypted parameters here are:{"isMain":1}

> Request example:

```json
{
  "isMain":1,
  "data":"HDOASJDISADLASDSAODASIDJAISLDJIASDU|ASDUADSASDIASDASDASDASLDASDJASDADOAAS"
}
```


>  The above command returns JSON structured like this:

```json
{
  "code": 200,
  "message": "Success"
}
```
 

# MPC WaaS

MPC WaaS, coming soon！
