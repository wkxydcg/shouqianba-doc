# 退款

## 入口

		{api_domain}/upay/v2/refund

## 签名验证

   请参考[签名机制文档](https://wosai.gitbooks.io/shouqianba-doc/content/zh-cn/api/sign.html)
   
## 请求参数说明

参数 | 参数名称 | 类型 | 必填|描述 |范例
--------- | ------ | ----- | -------|---|----
terminal_sn	|收钱吧终端ID| String(32)|Y|收钱吧终端ID|"00101010029201012912"
sn|	收钱吧唯一订单号|	String(16)|N|收钱吧系统内部唯一订单号|"7892259488292938"
client_sn	|商户订单号	|String(32)|N|商户系统订单号。|"7654321132"
client_tsn      |商户退款流水号  |String(32)|N|商户退款流水号，如果商户同一笔订单多次退款，需要传入不同的退款流水号来区分退款，如果退款请求超时，需要发起查询，并根据查询结果的client_tsn判断本次退款请求是否成功
refund\_request_no|退款序列号|	String(20)|Y|商户退款所需序列号，用于唯一标识某次退款请求，以防止意外的重复退款。**正常情况下，对同一笔订单进行多次退款请求时该字段不能重复；而当通信质量不佳，终端不确认退款请求是否成功，自动或手动发起的退款请求重试，则务必要保持序列号不变**|"23030349"
operator|操作员	|String(64)	|Y|执行本次退款的操作员|"Obama"
refund_amount|	退款金额	|String(10)|Y|退款金额|"100"
extended | 扩展参数集合 | JSON map | N | 收钱吧与特定第三方单独约定的参数集合,json格式，最多支持24个字段，每个字段key长度不超过64字节，value长度不超过256字节 | { "goods_tag": "beijing"}
goods_details |商品详情 |JSON |N |格式为json goods_details的值为数组，每一个元素包含五个字段，一个是goods_id商品的编号，一个是goods_name商品名称，一个是quantity商品数量，一个是price商品单价，单位为分，一个是promotion_type优惠类型，0:没有优惠 1: 支付机构优惠，为1会把相关信息送到支付机构| "goods_details": [{"goods_id": "wx001","goods_name": "苹果笔记本电脑","quantity": 1,"price": 2,"promotion_type": 0},{"goods_id": "wx002","goods_name":"tesla","quantity": 1,"price": 2,"promotion_type": 1}]

<p style="color:red; font-weight: bold;">sn与client_sn不能同时为空，优先按照sn查找订单，如果没有，再按照client_sn查询</p>

## 请求参数中关于goods_details说明
字段名称 | 数值格式 | 长度 | 字段描述
--------- | ------ | ----- | -------
goods_id       | String必填 | 32 | 商品的编号
goods_name     | String必填 |32  | 商品名称，如ipad
quantity       | Number必填 | 10 | 商品数量，如10
price          | Number必填 | 9  | 商品单价，单位为分，如2000
promotion_type | Number必填 | 1  | 优惠类型，0表示没有优惠，1表示支付机构优惠，为1会把相关信息送到支付机构


## 同步返回参数说明

参数 | 参数名称 | 类型 | 必填|描述 |范例
--------- | ------ | ----- | -------|---|----
result\_code | 结果码 | String | Y | 结果码表示接口调用的业务逻辑是否成功 | "REFUND_SUCCESS"
error\_code | 错误码 | String | N | 参考附录：业务执行错误码列表 | "ACCOUNT_BALANCE_NOT_ENOUGH"
error\_message | 错误消息 | String | N | 参考附录：业务执行错误码列表 | "商户余额不足"
terminal_sn	|收钱吧终端ID|	String(32)|	Y	|收钱吧终端ID，可使用英文字母和数字	| "103939292020"
sn	|收钱吧唯一订单号	|String(16)	|Y|	收钱吧系统内部唯一订单号| "7894259244061958"
client_sn	|商户订单号|	String(64)|	Y	|商户系统订单号。	| "22345677767776"
status|	退款流水状态	|String(32)|	Y	|本次退款对应的流水的状态| "SUCCESS"
order_status|	订单状态	|String(32)|	Y	|当前订单状态	| "REFUNDED"
trade_no	|支付平台的订单凭证号|	String(64)|	Y	|支付宝或微信的订单号| "2006101016201512090096528672"
total_amount	|交易总金额	|String(10)|	Y	|原始交易实收金额	| "100"
net_amount|	剩余金额	|String(10)|	Y	|实收金额减退款金额| "0"
finish_time	|退款动作在收钱吧的完成时间|	String(13)|	Y	|时间戳，本次退款动作在收钱吧的完成时间| "1449646835244"
channel\_finish_time	|退款动作在支付平台完成的时间|String(13)|	Y	|时间戳，本次退款动作在微信或支付宝的完成时间| "1449646835221"
subject|	商品概述	|String(32)	|Y|交易时候的商品概述| "wx"
operator|	操作员	|String(32)|Y|执行本次退款的操作员|"Obama"

返回的状态请参考<font color="red">**附录**</font>

## 退款返回示例
1. 退款成功

    ```json
    {
        "result_code": "200",
        "biz_response": {
            "result_code": "REFUND_SUCCESS",
            "data": {
                "sn": "7894259244067218",
                "client_sn": "12345677767776",
                "status": "SUCCESS",
                "payway": "3",
                "sub_payway": "1",
                "order_status": "REFUNDED",
                "trade_no": "2006101016201512080095793262",
                "total_amount": "1",
                "net_amount": "0",
                "finish_time": "1449563206776",
                "channel_finish_time": "1449563206632"
            }
        }
    }
    ```

2.退款失败

    ```json
    {
        "result_code": "200",
        "biz_response": {
            "result_code": "FAIL",
            "error_code": "UPAY_REFUND_INVALID_ORDER_STATE",
            "error_message": "订单已全额退款,可退金额不足"
        }
    }
    ```
    
## 退款接口接入过程常见问题
### 1.微信正式商户退款：
   由于微信系统业务变更，现新签约的微信正式商户（即是微信支付特约商户），默认都是没有退款权限的（即不能微信退款）。微信支付特约商户若想开通退款权限，需要将退款权限授权给服务商（即收钱吧）。
另服务商没有代该特约商户发起API退款的权限，需该特约商户进行授权操作，相关授权流程及操作请登陆网址： http://kf.qq.com/faq/120911VrYVrA150929imAfuU.html操作。 

具体操作：
1、登陆微信支付特约商户将退款权限授权给服务商网址： http://kf.qq.com/faq/120911VrYVrA150929imAfuU.html 
2、点击查看《退款指令授权证明书》；
微信官方有提供文本格式的《退款指令授权证明书》模板，直接把文本复制到WORD文档上，填写之后打印，并加盖公章；
3、点击【点击这里】，此处是特约商户需将退款权限授权给服务商的提交入口，提交需要填写的内容和加盖公章的《退款指令授权证明书》即可；

授权申请中会用到的服务商信息如下： 
服务商名称：上海喔噻互联网科技有限公司 
服务商商户号：1238313502

### 2.退款可以在订单支付完成后多久以内可以提交请求？
三个月以内都可以进行退款。

### 3.可以部分退款吗？手续费怎么算？
退款接口，不限制退款次数，可操作至剩余金额为0。退款成功后，手续费按照退款金额占订单金额比例系数乘以订单总手续费进行手续费退还。

### 4.一笔退款不能重复使用退款序列号，不然退款会失败。

### 5.都可以使用哪些参数去退款？
    可以使用trade_no,sn,client_sn去退款，注：使用trade_no和sn退款时需要将参数传入到退款请求参数sn里。
    
### 6.退款发起后金额什么时候到达消费者账户内？
    这个需要联系支付宝、微信等支付渠道，我们拿到支付渠道返回的状态后就会返回结果，至于金额是否立即到帐，需要联系支付渠道。
    
