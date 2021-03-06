# 支付接入文档
## 1. 文档说明
本文描述99支付平台支付系统包含：扫码支付业务、报文格式，目的协助商户技术人员快速接入99支付系统，便于尽快投入使用。

## 1.1. 名词解释
商户：指已经注册成为99支付系统的平台用户，使用99支付平台实现代付功能的企业。
客户：商户放款的用户。

## 1.2. 请求方式
数据交互式，统一采用POST请求，返回数据均为JSON格式。

## 1.3. 请求地址
采用http协议，**PS：注意结尾的带/**
代付请求地址：http://支付域名/Pay/pay/
代付结果查询地址：http://支付域名/Pay/repayOrderSearch/
代收请求地址：http://支付域名/Pay/newPay/
代收结果查询地址：http://支付域名/Pay/search/

## 1.4.签名算法
本支付平台提供**MD5**签名方式，商户需要从银商后台获取密钥。商户提交的数据，必须对数据进行签名，支付平台会验证此签名。商户在接收到数据后需对数据签名进行合法性验证，以确保是支付平台发送的数据。

------------


## 2. 代付/商户代收

**简要描述：** 

- 商户需要为客户进行付款时，调用此接口生成订单，支付平台完成付款后，调用商户的回调地址通知付款结果。

**请求URL：** 

-测试环境 ` http://支付域名/Pay/pay/ `
  
**请求方式：**
- POST 

**请求参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|payType |是  |string |DF001:银行卡代付模式 ZFB:支付宝代收 YHK:银行卡代收 DYT:店员通代收|
|price |是  |int | 代付金额，单位（分）    |
|orderNO |是  |string | 商户订单中的唯一值，不可重复    |
|appid |是  |int | 系统分配的唯一商户号  |
|bankCardNo |是  |string | 收款人银行卡号  |
|bankAccountName |是  |string | 收款人银行账户名  |
|bankName |是  |string | 收款人银行名称  |
|bankOpenNode |否  |string | 收款人银行卡开户行名称  |
|orderTime |是  |date | 格式：2019-11-26 23:12:33  |
|notifyUrl |是  |string | 系统回调商户的通知地址|
|memo |否  |string | 备注信息|
|borrower |是  |string | 借款人|
|orderType |是  |int | 代付类型 商户传1|
|sign |是  |string | 数据签名|


 **创建成功(代付)**

``` 
{
    "code": 1,
    "msg": "下单成功",
    "data": {
		"sysOrderNo": "xxxxxxxxxxxx" //平台订单号
	}
}
```

 **创建成功(代收)**

``` 
{
    "code": 1,
    "msg": "操作成功",
    "sysOrderNo": "MERC12t2019121922474052tR",
    "url": "http://api11.s-game88.com/index.php?r=Index/Index/jump&modelType=2&money=30000&payInterface=9&sysOrderNO=MERC12t2019121922474052tR"
}
```

 **创建失败**

``` 
{
    "code": -1,
    "msg": "订单创建失败,代付金额错误"
}
```

 **签名方式**
``` 
sign = md5(appid + price + orderNO + orderTime + notifyUrl + "&" + appKey)
``` 

 **补充说明**
 
 - key是指商户在99支付平台所获取的唯一充值密钥。
 

------------


## 3. 代付订单查询

**简要描述：** 

- 查询代付订单。

**请求URL：** 

-测试环境 ` http://支付域名/Pay/repayOrderSearch/ `
  
**请求方式：**
- POST 

**请求参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|sysOrderNo |是  |string |平台订单号   |
|sysOrderType |是  |int | 2:代付订单 |


 **查询成功**

``` 
{
    "code": 1,
    "msg": "支付成功"
}
```

 **查询失败**

``` 
{
    "code": 40000,
    "msg": "无此订单号"
}
```

------------

## 4. 代收

**简要描述：** 

- 客户进行还款时，在商户app内调用此接口生成订单，并跳转到接口返回的还款页面，还款成功后调用商户的回调地址通知还款结果。

**请求URL：** 

-测试环境 ` http://支付域名/Pay/newPay/ `
  
**请求方式：**
- POST 

**请求参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|payType |是  |string |传1   |
|price |是  |int | 代收金额，单位（分）    |
|orderNO |是  |string | 商户订单号，不可重复    |
|appid |是  |int | 系统分配的唯一商户号  |
|orderTime |是  |date | 格式：2019-11-26 23:12:33  |
|notifyUrl |是  |string | 系统回调商户的通知地址|
|borrower |是  |string | 借款人|
|sign |是  |string | 数据签名|


 **请求成功**

``` 
{
    "code": 1,
    "msg": "操作成功",
    "url": "xxxxxxxxxxxx" //付款页面的URL
}
```

 **请求失败**

``` 
{
    "code": 30001,
    "msg": "无效操作"
}
```

 **签名方式**
``` 
sign = md5(appid + price + orderNO + orderTime + notifyUrl + "&" + appKey)
``` 

 **补充说明**
 
 - key是指商户在99支付平台所获取的唯一充值密钥。
 

------------
 
 ## 5. 代收订单查询

**简要描述：** 

- 查询代收订单。

**请求URL：** 

-测试环境 ` http://支付域名/Pay/search/ `
  
**请求方式：**
- POST 

**请求参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|sysOrderNo |是  |string |平台订单号   |


 **查询成功**

``` 
{
    "code": 1,
    "msg": "支付成功"
}
```

 **查询失败**

``` 
{
    "code": 40003,
    "msg": "待支付"
}
```

------------



## 6. 代付异步回调商户

**简要描述：** 

- 99支付系统会根据商户在请求参数中提供的notifyUrl主动通知商户，发送http请求。

**回调方式：**
- POST 

**回调参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|price |是  |int | 订单金额 单位：分|
|realPrice |是  |int | 代付金额 单位：分|
|merchantOrderNo |是  |string | 商户平台的唯一订单号  |
|sysOrderNo |是  |string | 99支付平台的唯一订单号 |
|orderStatus |是  |int | 交易状态（0交易成功，其他交易失败） |
|sign |是  |string | 数据签名|


 **回调成功返回**

``` 
SUCCESS 
```

 **回调失败返回**

``` 
FAIL
```

 **签名方式**
``` 
sign = md5(price + merchantOrderNo + sysOrderNo + "&" + appKey)
``` 

 **补充说明**
 
 - key是指商户在99支付平台所获取的唯一充值密钥。


------------

## 7. 代收异步回调商户

**简要描述：** 

- 99支付系统会根据商户在请求参数中提供的notifyUrl主动通知商户，发送http请求。

**回调方式：**
- POST 

**回调参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|price |是  |int | 订单金额 单位：分|
|rawMoney |是  |int | 代收金额 单位：分|
|orderNo |是  |string | 商户平台的唯一订单号  |
|sysOrderNo |是  |string | 99支付平台的唯一订单号 |
|orderStatus |是  |int | 交易状态（0交易成功，其他交易失败） |
|sign |是  |string | 数据签名|


 **回调成功返回**

``` 
SUCCESS 
```

 **回调失败返回**

``` 
FAIL
```

 **签名方式**
``` 
sign = md5(price + orderNo + sysOrderNo + "&" + appKey)
``` 

 **补充说明**
 
 - key是指商户在99支付平台所获取的唯一充值密钥。

------------


## 8. 代收订单查询(appid和商户订单号)

**简要描述：** 

- 查询代收订单。

**请求URL：** 

-测试环境 ` http://支付域名/Pay/searchByOutOrderNO/ `
  
**请求方式：**
- POST 

**请求参数：** 

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|appid |是  |int | 商户ID   |
|outOrderNo |是  |string | 商户订单号   |
|year |否  |int | 订单年份,默认今年   |


 **查询成功**

``` 
{
    "code": 1,
    "msg": "支付成功"
}
```

 **查询失败**

``` 
{
    "code": 40001,
    "msg": "无效参数"
}

{
    "code": 40000,
    "msg": "无此订单号"
}

{
    "code": 40002,
    "msg": "支付失败"
}

{
    "code": 40003,
    "msg": "待支付"
}
```
