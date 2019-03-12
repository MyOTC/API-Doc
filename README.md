参看《PingPay接入文档_V0.5》

具体可以参看demo.php

### 创建订单

```
<?php
$order_no = time();
$amount = 100;
$outUid = mt_rand(1,10);
$accessKey = "9khriuu4-tzzh96jl-ekxibzth-q2390";
$AccessKeySecret = "qmnh2bya-pajgb9aw-bic7jebv-o0125";
$redirect = "http://pingpay.co";
$signStr = "{$redirect}MD5{$order_no}{$amount}{$outUid}{$accessKey}{$AccessKeySecret}";
$sign = md5($signStr);
?>

<!DOCTYPE html>
<html>
<head>
    <title>测试</title>
    <style type="text/css">
        .flex{
            display: flex;
            -webkit-flex: flex;
            -ms-flex: flex;
        }
        .flex-auto{
            -webkit-flex: 0 0 auto;
            -ms-flex: 0 0 auto;
            flex: 0 0 auto;
        }
        .flex-fill{
            -webkit-flex: 1 1;
            -ms-flex: 1 1;
            flex: 1 1;
        }
        .flex-center{
            align-items: center;
            -webkit-align-items: center;
           -ms-flex-align: center;
        }
        .item{
            margin-bottom: 10px;
        }
        .it{
            width: 100px;
        }
        input{
            width: 320px;
            height: 32px;
        }
        button{
            width: 320px;
            height: 32px;
        }
    </style>
</head>
<body>
    <h3>入金测试1</h3>
    <div>
        <form method="POST" action="http://gateway.dragonscam.me/v1/openapi/buy">
            <div class="item flex flex-center">
                <div class="flex-auto it">订单号</div>
                <input type="text" name="outOrderSn" value="<?= $order_no?>">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">数量</div>
                <input type="text" name="amount" value="<?= $amount?>">
                CNY
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">AccessKey</div>
                <input type="text" name="accessKey" value="<?= $accessKey?>">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">UID</div>
                <input type="text" name="outUid" value="<?= $outUid?>">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">redirect</div>
                <input type="text" name="redirect" value="<?= $redirect?>">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">sign</div>
                <input type="text" name="sign" value="<?= $sign?>">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it">signType</div>
                <input type="text" name="signType" value="MD5">
            </div>
            <div class="item flex flex-center">
                <div class="flex-auto it"></div>
                <button type="submit">提交订单</button>
            </div>
        </form>
    </div>
</body>
</html>
```
### 接收回调
java
```
/**
     * 订单状态异步回调
     *
     * @param request
     * @throws IOException
     */
    @RequestMapping("/v1/api0/demo/notify")
    public void orderStatusNotify(HttpServletRequest request) throws IOException {
        String messageJson = IOUtils.toString(request.getInputStream(), "UTF-8");
        
        if (messageJson == null) {
            throw new NullPointerException("messageJson is marked @NonNull but is null");
        } else {
            log.info("order Status notify message received:{}", messageJson);

            Message message;
            try {
                message = (Message)JSON.parseObject(messageJson, Message.class);
            } catch (Exception var5) {
                throw new InvalidParameterException("message format error.");
            }

            if (message != null && message.getType() != null) {
                if ("1".equals(message.getSignatureVersion()) && isMessageSignatureValid(message)) {
                    OrderStatusAsyncNotifyMessage orderStatusAsyncNotifyMessage = null;
                    if (message.getType().equals("Notification")) {
                        if (!ApiIdentityUtil.sign(this.SECRET_KEY, HmacAlgorithms.HMAC_SHA_256.getName(), message.getMessage()).equals(message.getSubject())) {
                            log.info("Business signature verification failed.");
                            throw new SecurityException("Unexpected signature version. Unable to verify signature.");
                        }
                        
                        
                    } else if (message.getType().equals("SubscriptionConfirmation")) {
                        this.subscription(message);
                    } else if (message.getType().equals("UnsubscribeConfirmation")) {
                        this.subscription(message);
                    } else {
                        log.info("Unknown message type.");
                    }

                    log.info("Done processing message: " + message.getMessageId());
                    return orderStatusAsyncNotifyMessage;
                } else {
                    throw new SecurityException("Signature verification failed.");
                }
            } else {
                log.info("message type not exist");
                throw new InvalidParameterException("Unexpected signature version. Unable to verify signature.");
            }
        }
        
        IOUtils.closeQuietly(request.getInputStream());
    }
    
    //确认订阅
    private void subscription(Message msg) throws IOException {
        InputStream inputStream = (new URL(msg.getSubscribeURL())).openStream();
        String response = IOUtils.toString(inputStream, "UTF-8");
        log.info("Subscription confirmation (" + msg.getSubscribeURL() + ") Return value: " + response);
    }
    
```
