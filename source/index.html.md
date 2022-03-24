---
title: MicroPay Payment API

language_tabs: # must be one of https://git.io/vQNgJ

  - json

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Wavpay Systems Sdn. Bhd</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the MicroPay Payment API
---

# Introduction

Function Description:

Welcome to the MicroPay Payment API! Payment code payment means that the user displays the "payment code" in WayPay to the merchant system to scan and complete the payment directly. It is suitable for face-to-face cashier scenarios in offline places, such as supermarkets, convenience stores, restaurants, and other physical places with clear business addresses. 

# Preparation before access

Parameter application: 

Merchants need to apply for a merchant ID (mchId) and a merchant key (apikey) on the platform to open the payment interface.
Merchant ID is the unique identifier of the merchant. This parameter is required for most of merchant settlement and record query.
Merchant secret key is mainly used for signature verification of data exchange information, which is the security guarantee for data access.

Obtain a certificate:

The platform has not yet used the certificate, and it may increase in the later period. Please pay attention to the notice of the platform.

# Development Guidelines

Signature Generation:

Merchants can follow the steps below to generate a requested signature. The following process will take the java language as an example, please convert other languages by yourself
NOTE
Merchant need to have a merchant ID and a merchant key for signature generation.

```java
Construct the signature:

1) Obtain the required parameter values according to the introduction of the request api, and ensure that all parameter values are converted to string types.

2) Get the current timestamp of the system when the request was initiated, that is, the total number of milliseconds from 00:00:00 On January 1, 1970 GMT to the present, as the request. 

        1.	System.currentTimeMillis();

3) Put the request parameter and timestamp into the map in turn to obtain the main body data of the request message , as follows :
        1.    Map<String, String> params = new HashMap<String, String>();
        2.	  params.put("mchId", "202006001");
        3.	  params.put("mchOrderId", "333333333333");
        4.	  params.put("timestamp", "1646733298647");

4) Obtain the parameter name and sort the string according to the ASCLL code. After completion, connect the parameter values in the following order, filter out the null values when connecting, and use “I” to connect two adjacent data.

        1.	List<String> keys = new ArrayList(params.keySet());
        2.	  Collections.sort(keys);
        3.	  StringBuilder content = new StringBuilder();
        4.	  for (int i = 0; i < keys.size(); ++i) {
        5.	   String key = keys.get(i);
        6.	   String value = params.get(key);
        7.	   if (i == keys.size() - 1) {
        8.	    content.append(value);
        9.	   } else {
        10.	    content.append(value).append(“|”);
        11.	   }
        12.	  }

5) After the data connection is completed , add the merchant’s secret key at the end, encrypt the final result with SHA-256 16 digit ,convert it into a hexadecimal string , and convert the English to capital.

        1.	MessageDigest messageDigest;
        2.	  String encodeStr = "";
        3.	  try {
        4.	   messageDigest = MessageDigest.getInstance("SHA-256");
        5.	   messageDigest.update(str.getBytes("UTF-8"));
        6.	   encodeStr = byte2Hex(messageDigest.digest());
        7.	  } catch (Exception e) {
        8.	   e.printStackTrace();
        9.	  }
        10.	encodeStr= encodeStr.toUpperCase();

Signature Verification:

Signature verification is to perform data screening on the acquired information, remove the signature field (secureHash) from the acquired data, generate a signature for the remaining data according to the signature generation process, and compare the result with the acquired signature fail.

Signature example:
        1.	parameter：
        2.	params.put("mchId", "202006001");
        3.	  params.put("mchOrderId", "333333333333");
        4.	  params.put("timestamp", "1646733298647");
        5.	
        6.	Secret key：
        7.	  String key = "P8yAFr213xcvb41xfgkjELoOhWl";
        8.	
        9.	Sort concatenated strings：202006001|333333333333|1646733298647|P8yAFr213xcvb41xfgkjELoOhWl
        10.	sign：70B6E3540261C9BBBF232C3604BDF4CD24ACB25EED5F4EB284CC68A359672782


```


<!-- # Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside> -->

# API List

The IPs and ports of the interface addresses in the list are only examples, and the specific access addresses will be notified separately.

## Payment by user code

After the cashier uses the scanning device to read the payment code of the WayPay user, the QR code or barcode information will be transmitted to the merchant's cashier, and the merchant's cashier or the merchant's background will call this interface to initiate payment.
Note: After submitting the payment request, WayPay will return the payment result synchronously. When the returned result is "system error", the merchant's system waits for 5 seconds and then calls the [Query Order API] to check the actual transaction result confirmation of payment;



```javascript

`POST http://example.com/api/micropay/micropay`

```

> The above command returns JSON structured like this:

```json
{
    "mchId": "202006001",
    "mchOrderId": "333333333333",
    "amount": "1.00",
    "goodsInfo": "test",
    "discount": "",
    "point": "",
    "payCode":"395865319883277944",
    "timestamp": "1646733298647",
    "secureHash": "2C7698D45C68C30B769AC5CA7F76A6EA2CB1768E2B2D1AF63E31E8BA8D2B528F"
}
```

> The example of return result:

```json

{
    "code": 0,
    "msg": "success",
    "data": {
        "merchantOrderId": "333333333333",
        "wavpayOrderId": "4202203101714486756706",
        "mchId": "202006001",
        "merchantName": "LPR Parking System",
        "tradeTime": "2022-03-10 17:14:49",
        "payTime": "2022-03-10 17:56:23",
        "paySence": " MICROPAY",
        "tradeStatus": "PAID",
        "amount": "1.00",
        "goodsInfo": null,
        "timestamp": "1646985721445",
        "secureHash": "40B41942BC9708F89BB18D752AA9E6145A92ADC932853488BA8242DA69A9D52B"
    }
}
```

This endpoint for payment by user code.

### HTTP Request

`POST http://example.com/api/micropay/micropay`

### Query Parameters

Name | YES/NO | Description
--------- | ------- | -----------
mchId	| YES	| Merchant id
mchOrderId | YES | Merchant order id
amount | YES | amount
goodsInfo | YES | Product info no empty string is passed 
discount | YES | Discount ,no empty string
point |YES | Point ,no empty string passed
payCode | YES | Payment code
timestamp | YES| Request timestamp
secureHash | YES | sign

### Return result description

Name | YES/NO | Description
--------- | ------- | -----------
code | YES | Result code, 0 : success
msg | YES | Result message
data | YES | Result data

### Data result set description

Name | YES/NO | Description
--------- | ------- | -----------
merchantOrderId | YES | Merchant Order ID
wavpayOrderId |	YES |	Wavpay Order ID
mchId |	YES	| Merchant ID
merchantName |	YES	| Merchant name
tradeTime |	YES	| Trade time
payTime |	YES	| Payment time
paySence |	YES	| Type of Pay：MICROPAY
tradeStatus |	YES	| Order Status：NEW, PAID, PENDING, FAILED, REFUND_IN_PROCESS, REFUNDED, REFUND_FAILED
amount |	YES	| amount
goodsInfo	| YES	| Product info no empty string is passed
timestamp	| YES	| Request timestamp
secureHash	| YES	| Sign


## Query Order

Merchants can actively query the order status through the query order interface to complete the next business logic. The order status can be inquired through the merchant's order number.
Need to call the query interface：

• When there is an abnormality in the merchant's background, network, server, etc., the merchant's system does not receive the payment notification in the end.

• After calling the payment interface, it returns a system error or unknown transaction status.

• Before calling the order closing API, you need to confirm the payment status.


```javascript


`POST http://example.com/api/micropay/queryBill`

```

> The above command returns JSON structured like this:

```json
{
    "mchId": "202006001",
    "mchOrderId": "333333333333",
    "timestamp": "1646733298647",
    "secureHash": "40B41942BC9708F89BB18D752AA9E6145A92ADC932853488BA8242DA69A9D52B"
}


```
> The example of return result:

```json

{
    "code": 0,
    "msg": "success",
    "data": {
        "merchantOrderId": "333333333333",
        "wavpayOrderId": "4202203101714486756706",
        "mchId": "202006001",
        "merchantName": "LPR Parking System",
        "tradeTime": "2022-03-10 17:14:49",
        "payTime": "2022-03-10 17:56:23",
        "paySence": " MICROPAY",
        "tradeStatus": "PAID",
        "amount": "1.00",
        "goodsInfo": null,
        "timestamp": "1646985721445",
        "secureHash": "40B41942BC9708F89BB18D752AA9E6145A92ADC932853488BA8242DA69A9D52B"
    }
}

```

### HTTP Request

`POST http://example.com/api/micropay/queryBill`

### Request Description

Name | YES/NO | Description
--------- | ------- | -----------
mchId	| YES	| Merchant id
mchOrderId | YES | Merchant order id
timestamp | YES| Request timestamp
secureHash | YES | sign

### Return result description

Name | YES/NO | Description
--------- | ------- | -----------
code | YES | Result code, 0 : success
msg | YES | Result message
data | YES | Result data

### Data result set description

Name | YES/NO | Description
--------- | ------- | -----------
merchantOrderId | YES | Merchant Order ID
wavpayOrderId |	YES |	Wavpay Order ID
mchId |	YES	| Merchant ID
merchantName |	YES	| Merchant name
tradeTime |	YES	| Trade time
payTime |	YES	| Payment time
paySence |	YES	| Type of Pay：MICROPAY
tradeStatus |	YES	| Order Status：NEW, PAID, PENDING, FAILED, REFUND_IN_PROCESS, REFUNDED, REFUND_FAILED
amount |	YES	| amount
goodsInfo	| YES	| Product info no empty string is passed
timestamp	| YES	| Request timestamp
secureHash	| YES	| Sign

