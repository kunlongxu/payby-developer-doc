## Order cancellation



If the payment result is not returned for a long time, you can use this interface to cancel the order. However, PayBy only supports cancellations when the order is in the `CREATED` status, and the order status will be updated to `FAILURE` after PayBy processed the cancellation request successfully.

If the order status has been updated to `PAID_SUCCESS` or `SETTLED` when the cancellation was initiated, PayBy will reject this  request. It is recommended to use `Revoke` or `Refund` interface.  



### Interface URL

Staging URL: https://uat.test2pay.com/sgs/api/acquire2/cancelOrder

Production URL: https://api.payby.com/sgs/api/acquire2/cancelOrder



### Request 

#### Http Header

---

**Content-Language**    <font color = '#7d8793'>String</font> 

The language in which the response message will be used, currently only English is supported.

Example value: en

Maximum length: `10`



**Content-Type**    <font color = '#7d8793'>String</font>  <font color = '#f19938'>Required</font>

The media type. Required for operations with a request body. The value is `application/<format>`, where `format` is `json`.

Example value: application/json



**sign**   <font color = ' #7d8793'>String</font>   <font color = '#f19938'>Required</font>

Requests should be signed using private-key cryptography. This allows the payment gateway to verify that an incoming request is really from your application.



**Partner-Id**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

The merchant id of your account. 

Example value: 200001200101

Maximum length: `12`



#### Http Body

---

**requestTime**   <font color = ' #7d8793'>Timestamp</font>   <font color = '#f19938'>Required</font>

If the request time is more than 15 minutes away from the current time, the request will be rejected. This parameter is used to prevent repeated requests for orders that should have been cancelled due to timeouts.

Example value: 1581493898000



**bizContent**   <font color = ' #7d8793'>Object</font>  

You can use either `merchantOrderNo`  or `orderNo` to specify an order. But you can't use the two parameters at the same time.

- **merchantOrderNo**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

  The merchant's referece number of the request. Used to track every request.

  Example value: M965739182419

  Maximum length: `64`



- **orderNo**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

  The PayBy's unique identify number of the order. 

  Example value: 131658300517875854



#### Request sample

```json
Http Header  
{
    "Content-Language": "en",
    "Content-Type": "application/json",
    "sign": "MH8c9++jlzo0WBLJxNcbXxtyuAsE0EJzYtgnOsLDTrkG9rnF+tAf1NCDnJxUla0gQDQA5hjxMHKv42eZYrC/QR4cRvqSsnaUjtfOKbWcPEi4s6EcV/dHN0xPvDh5SPr9yAqrR44JRWzdXtG4P3zre57RbvXrhxZpXAkrwO0nXI4vMuKl1WsKSU0sdVqQx+v5cKXo8rK/iGGypLvH3VDya7m4ESo/GIvVanwyvItOZk/bY+L4KeZ+uuRNTXStYeBc4j4Oq1OrbYb0DwvDOijYl1qUt1pkZb5FWMfj8il0HQvbfhmMWRPsWeUObKJJfHJZNiwdV4SAWoHGtivw3/vu6A==",
    "Partner-Id": "200000000888"
}

Http Body
{
    "requestTime": 1581422748669,
    "bizContent": {
        "merchantOrderNo": "M172475858661"
    }
}
```





### Response

#### Http Header

---

**sign**   <font color = ' #7d8793'>String</font>   <font color = '#f19938'>Required</font>

When Payby sends response, Payby will use its own private key to sign the message, and the merchant uses Payby 's public key to verify the signature. If the verification is passed, it proves that the response was sent by Payby and not faked by others.



#### Http Body

---

**head** 

- **applyStatus**   <font color = ' #7d8793'>Enum</font>    <font color = '#f19938'>Required</font>

  The result of the request. The possible values are:

  `SUCCESS `- Application successful.
  `FAIL` - Application failed. Check the `code`  and `msg` for exact reason.
  `ERROR` - Application error. The signature verification failed. Please check whether the private key used for the signature and the public key uploaded on the PayBy portal are one key pair.



- **code**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

  Response Codes. View the [resconse codes](#response code) section to know all the possible results.

  Example value: 0

  

- **msg**   <font color = ' #7d8793'>String</font>   

  Description of this code. View the [resconse codes](#response code) section to know all the possible results.

  

- **traceCode**   <font color = ' #7d8793'>String</font>   

  No special meaning, PayBy internally used to locate the error.

  

**body** 

> ??????  <font color = '#f19938'>**Notice **</font>
>
>  <font color = 'black'>Body is returned only when `applystatus` = `success`, and `code` = `0`.
> If `applystatus` = `error` or `failed`;  or `applystatus` = `success`, `code` !=`0` , that indicates an error. Please check errors and try again.</font>



- **acquireOrder**   <font color = ' #7d8793'>Object</font>

  The attributes are:

     - **requestTime**   <font color = ' #7d8793'>Timestamp</font>    <font color = '#f19938'>Required</font>

       Request time passed by the merchant when placing the order.

       Example value: 1581493898000

  

  - **merchantOrderNo**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    The merchant's referece number of the request. Used to track every request.

    Example value: M965739182419

    Maximum length: `64`

    

     - **orderNo**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

       The PayBy's unique identify number of the order. 

       Example value: 131658300517875854

       

     - **status**   <font color = ' #7d8793'>Enum</font>    <font color = '#f19938'>Required</font>

       The possible values are:

       `CREATED`. The order has been created.

       `PAID_SUCCESS`.  The order has been successfully paid.

       `SETTLED`. The order has been paid and the fund has been settled to merchant's account.

       `FAILURE`. The order has been cancelled or expired.

  

  - **paymentInfo**   <font color = ' #7d8793'>Object</font>   

    - **paidAmount**  <font color = ' #7d8793'>Money</font>  <font color = '#f19938'>Required</font>

      The amount actually paid by the user. If a discount is used, it will be different from the order amount.

    

    - **paidTime**  <font color = ' #7d8793'>TimeStamp</font>  <font color = '#f19938'>Required</font>

      Payer's successful payment time

      Example value: 1581493898000

    

    - **payerMid**  <font color = ' #7d8793'>String</font>  

      If the payer uses a BOTIM or Pay By wallet for payment, `payerMid` represents the payer's member ID in the wallet.

      Example value: 200001200101

    

    - **payerFeeAmount**  <font color = ' #7d8793'>Money</font>  

      If the order transaction fee is set to be charged from the payer, `payerFeeAmount` represents the actual amount of the transaction fee.

      

    - **payeeFeeAmount**  <font color = ' #7d8793'>Money</font>

      If the order transaction fee is set to be charged from the payee, `payeeFeeAmount` represents the actual amount of the transaction fee.

      

    - **payChannel**  <font color = ' #7d8793'>String</font>  <font color = '#f19938'>Required</font>

      The payment channel used by the payer. The possible values are`BANKCARD`, `INSTALLMENT`, `EWALLET`, etc.

    

    - **settlementAmount**  <font color = ' #7d8793'>Money</font>  <font color = '#f19938'>Required</font>

      The actual funds the payee can receive after deducting transactions fees and amount for other reasons.

    

    - **cardInfo**  

      This object may be returned only for `DIRECTPAY` payment scene.The attibutes are:

      ><font color = 'black'>**brand**</font>    <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>
      >
      >The card issuer. The possible values are: 
      >
      >`MASTERCARD`
      >`VISA`
      >`AE`
      >`DISCOVER`
      >`JC`
      >
      >
      >
      ><font color = 'black'>**cardId**</font>    <font color = ' #7d8793'>String</font>
      >
      >If the payer's card information was requested to be saved for future use., payby will return the card's ID.
      >
      >Example value: 31658300
      >
      >
      >
      ><font color = 'black'>**last4**</font>    <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>
      >
      >Last 4 digits of card number.
      >
      >Example value: 6345
      >
      >
      >
      ><font color = 'black'>**cardType**</font>    <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>
      >
      >The possible values are:
      >
      >`DC`. Debit Card
      >
      >`CC`. Credit Card
      >
      >
      >
      ><font color = 'black'>**expMonth**</font>    <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>
      >
      >Two -digit number, representing the card expiry month.
      >
      >Example value: 01
      >
      >
      >
      ><font color = 'black'>**expYear**</font>    <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>
      >
      >Two -digit number, representing last two digits of the card expiry year.
      >
      >Example value: 22

      

  - **product**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    The product name related to the payment scene parameter used in the order. This product name is only used for PayBy internal classification.

    Example value: Basic Payment Gateway

    

     - **totalAmount**   <font color = ' #7d8793'>Money</font>    <font color = '#f19938'>Required</font>

       The order amout intended to collect from the payer.

  

  - **payeeMid**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    The payee's member ID in PayBy.

    Example value: 200001200101

    

     - **expiredTime**   <font color = ' #7d8793'>TimeStamp</font>    <font color = '#f19938'>Required</font>

       The order expiration time, after which the payment cannot be completed.

       Example value: 1581493898000

  

  - **notifyUrl**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    To receive asynchronous notifications of order status updates, the merchant can pass the notify URL in the request to place order.

    Example value: https://www.payby.com/

    

     - **subject**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

       Description of this order. 

       Example value:  iPhone.

  

  - **accessoryContent**   <font color = ' #7d8793'>String</font>   

    Used for storing additional information about the order.

    

     - **paySceneCode**   <font color = ' #7d8793'>Enum</font>    <font color = '#f19938'>Required</font>

       Payment scene used to create the order. The possible values are `PAYPAGE`, `INAPP`, `EWALLET`, `DYNQR`, `QRPAY`, `JSAPI`, `AUTODEBIT`, `DIRECTPAY`, etc.

  

  - **paySceneParams**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    Different payment scenarios need to pass different scenario parameters. 

  

     - **deviceId**   <font color = ' #7d8793'>String</font>  

       If the transaction wad made on a terminal or virtual device, the device id can be passed for transaction data statistic.
       Note that this parameter must be passed if you are using the secondery merchant function.

  

  - **secondaryMerchantId**   <font color = ' #7d8793'>String</font>    <font color = '#f19938'>Required</font>

    The code to identifie the order currency. Currently only AED is supported.

    Example value: AED

    

     - **failCode**   <font color = ' #7d8793'>String</font>    

       If the order status is `FAILURE`, the code to identify the exact reason.

       Example value: 12.34

  

  - **failDes**   <font color = ' #7d8793'>String</font>   

    If the order status is `FAILURE`, the failure reason.

    Example value: AED

  

     - **revoked**   <font color = ' #7d8793'>Boolean</font>    <font color = '#f19938'>Required</font>

       If true, it means that the payment has been made but then cancelled.

  

  - **reserved**   <font color = ' #7d8793'>String</font>    

    Merchant's notes for the order.



#### Response sample

```json
Http Header
{
    "sign": "XBRRQzTFvIKPE4bhBLKXF23RBeJn7WytxwhQXutCTZgO7NmzVE0YufjbSUSFKB5Eg6EtrVMVFM/FmTSSI/hWBJnk0j8rBxb+K3FlplTw1ydStM7upkvxLOe2uoOkac8FFXZIvjRWDNY7+VXwo40yI33Ct3YLTwu5kQHhVCEf6ClvnkTynXuy6hxALFsbJchKOZNbqm5WA24x2VtFbNe+jz+CRwD4MD8xg6npPMwfTc0b4sZ7SkUAKYlttxm+nO9qFjczbp55R7LdajxfCXxgpTp0hc576pwo2AqyNDcMjzunynX+AR7pzDqwkgC1NIc/eax67HPq/X+v/T8t671sHg=="
}

Http Body
{
    "head": {
        "applyStatus": "SUCCESS",
        "code": "0",
        "msg": "SUCCESS",
        "traceCode": "1127"
    },
    "body": {
        "acquireOrder": {
            "merchantOrderNo": "M172475858661",
            "orderNo": "131422372621002238",
            "status": "FAILURE",
            "product": "Basic Payment Gateway",
            "totalAmount": {
                "currency": "AED",
                "amount": 21.74
            },
            "payeeMid": "200000004021",
            "expiredTime": 1581429572587,
            "notifyUrl": "http://www.yoursite.com",
            "subject": "Your subject",
            "requestTime": 1581422372587,
            "accessoryContent": {
                "amountDetail": {
                    "discountableAmount": {
                        "currency": "AED",
                        "amount": 2.31
                    },
                    "amount": {
                        "currency": "AED",
                        "amount": 20.65
                    },
                    "vatAmount": {
                        "currency": "AED",
                        "amount": 1.09
                    },
                    "tipAmount": {
                        "currency": "AED",
                        "amount": 0.02
                    }
                },
                "goodsDetail": {
                    "body": "Gifts",
                    "categoriesTree": "CT12",
                    "goodsCategory": "GC10",
                    "goodsId": "GI1005",
                    "goodsName": "candy flower",
                    "price": {
                        "currency": "AED",
                        "amount": 10.87
                    },
                    "quantity": 2,
                    "showUrl": "http://www.yourshop.com/index.htm"
                },
                "terminalDetail": {
                    "operatorId": "OP10001",
                    "storeId": "SI1002",
                    "terminalId": "TI10000",
                    "merchantName": "candy home",
                    "storeName": "lovely house"
                }
            },
            "paySceneCode": "PAYPAGE",
        }
    }
}
```



#### Response Codes

| Code  | Msg                         | Reason                      | Solution           |
| ----- | --------------------------- | ------------------------- | ------------------ |
| 0     | SUCCESS                     | Successful                      |                    |
| 400   | INVALID_PARAMETER           | Parameter error                  | Adjust the request parameters       |
| 400   | REQUESTTIME_TOO_EARLY       | The request time is much earlier than the current time  | Adjust the request parameters       |
| 400   | REQUESTTIME_TOO_LATER       | The request time is much later than the current time  | Adjust the request parameters       |
| 402   | RATE_LIMIT_REJECT           | Requests are too frequent              | Reduce request frequency       |
| 403   | UNAUTHORIZED                | API is not authorized                 | Contact PayBy          |
| 404   | SERVICE_NOT_AVAILABLE       | API service is unavailable             | Contact PayBy           |
| 500   | SYSTEM_ERROR                | System error                  | Contact PayBy and try again later |
| 504   | SERVICE_TIMEOUT             | Service timeout                  | Try again later           |
| 601   | RISK_FAIL                   | Risk control verification failed              | Please adjust the business         |
| 62001 | ORDER_PAID                     | Cancellation is not supported for successfully paid orders | Adjust the merchant order number       |
| 62002 | ORDER_FAILURE               | Failed order initiates cancellation<br/>Failed order initiates refund order | Adjust the merchant order number     |
| 62003 | ORDER_SETTLED               | Settled order initiates cancellation  | Adjust the merchant order number     |
| 62004 | MERCHANT_ORDER_NO_NOT_EXIST | Merchant order number does not exist when quering query    | Modify the merchant order number     |
| 62035 | ORDER_NO_NOT_EXIST                | PayBy order number does not exist         | Adjust PayBy Order number            |

