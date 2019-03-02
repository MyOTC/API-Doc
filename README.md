
<!DOCTYPE html>
<html>

<body>

<h1 id="toc_0">目录</h1>

<ul>
<li><a href="#API%E6%A6%82%E8%BF%B0">API 概述</a></li>
<li><a href="#%E5%85%A5%E9%87%91">入金</a></li>
<li><a href="#%E5%87%BA%E9%87%91">出金</a></li>
<li><a href="#%E5%85%B6%E5%AE%83API%E6%8E%A5%E5%8F%A3">其它API接口</a></li>
<li><a href="#%E5%BC%82%E6%AD%A5%E9%80%9A%E7%9F%A5">异步通知</a></li>
<li>签名机制

<ul>
<li><a href="#API%E7%AD%BE%E5%90%8D%E6%9C%BA%E5%88%B6">API签名机制</a></li>
<li><a href="#%E7%BD%91%E5%85%B3URL%E7%AD%BE%E5%90%8D%E6%9C%BA%E5%88%B6">网关URL签名机制</a></li>
</ul></li>
</ul>

<h1 id="toc_1"><a name="API概述">一.API 概述</a></h1>

<p>通过网关+API的方式实现OTC的对接  </p>

<ol>
<li>出金</li>
<li>入金 </li>
<li>同步资产</li>
</ol>

<p>订单接口列表和申诉接口列表，要进行签名调用。</p>

<p>概览</p>

<h2 id="toc_2">网关&amp;接口列表</h2>

<table>
<thead>
<tr>
<th>接口名称</th>
<th>请求方法</th>
<th>访问方式</th>
<th>类型</th>
<th>描述</th>
<th>需要验签</th>
</tr>
</thead>

<tbody>
<tr>
<td>获取账户信息</td>
<td><a href="#%E8%8E%B7%E5%8F%96%E8%B4%A6%E6%88%B7%E4%BF%A1%E6%81%AF">/v1/api/open/account</a></td>
<td>API</td>
<td>POST  Content-Type:application-json</td>
<td>获取账户信息</td>
<td>Y</td>
</tr>
<tr>
<td>获取币种参考价</td>
<td><a href="#%E6%9F%A5%E8%AF%A2%E4%BB%B7%E6%A0%BC%E6%8E%A5%E5%8F%A3">/v1/api/open/price</a></td>
<td>API</td>
<td>POST Content-Type:application-json</td>
<td>获取币种参考价</td>
<td>Y</td>
</tr>
<tr>
<td>订单详情</td>
<td><a href="#%E8%8E%B7%E5%8F%96%E8%AE%A2%E5%8D%95%E8%AF%A6%E6%83%85">/v1/api/open/order/detail</a></td>
<td>API</td>
<td>POST Content-Type:application-json</td>
<td>获取币种参考价</td>
<td>Y</td>
</tr>
<tr>
<td>出金下单</td>
<td><a href="#%E5%87%BA%E9%87%91%E4%B8%8B%E5%8D%95">/v1/api/open/order/sell</a></td>
<td>API</td>
<td>POST Content-Type:application-json</td>
<td>卖币下单</td>
<td>Y</td>
</tr>
<tr>
<td>出金订单详情</td>
<td><a href="#%E5%87%BA%E9%87%91%E8%AE%A2%E5%8D%95%E8%AF%A6%E6%83%85">/gateway/v1/receipt</a></td>
<td>网关</td>
<td>GET</td>
<td>卖币下单</td>
<td>Y</td>
</tr>
<tr>
<td>入金下单 &amp; 详情</td>
<td><a href="#%E5%85%A5%E9%87%91%E4%B8%8B%E5%8D%95">/gateway/v1/pay</a></td>
<td>网关</td>
<td>GET</td>
<td>卖币下单</td>
<td>Y</td>
</tr>
</tbody>
</table>

<h1 id="toc_3"><a name="入金">二.入金</a></h1>

<h2 id="toc_4">入金流程</h2>

<p><img src="https://raw.githubusercontent.com/bddpay/APIDOC/master/flow_buy.jpg" alt=""></p>

<h2 id="toc_5">接入准备</h2>

<ol>
<li><a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">设置AccessKey 和 SecretKey</a></li>
<li>下单成功后接收同步通知的callback地址（上图第4部需要）</li>
<li><a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">设置异步通知接收地址和秘钥</a>（上图第7，10部需要）</li>
</ol>

<h2 id="toc_6"><a name="入金下单" style="color:#000000">入金网关接口</a></h2>

<h3 id="toc_7">1. 网关下单&amp;订单详情 （图中第2步）</h3>

<p>生成BDD网关下单url </p>

<p><strong>样例：</strong></p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/pay?AccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-12-18T11%3A34%3A53.989Z&amp;amount=150&amp;callback=http%3A%2F%2Fpay.rmmlm.com%2Fv1%2Fapi0%2Ftest%2Fgateway%2FcreateOrder&amp;currency=CNY&amp;idNumber=2303234234234234234&amp;mobile=13688282823423&amp;name=%E4%BE%AF%E5%85%B4%E7%9B%9B&amp;outOrderSn=1545132894045&amp;outUid=1000&amp;type=3&amp;unit=2&amp;variety=USDT&amp;Signature=E++6HXtjVg3Ib3SmOIyRAZTFtdX5VVNcDpKyvWbNlZI=</code></pre></div>

<p><strong>请求参数:</strong></p>

<table>
<thead>
<tr>
<th style="text-align: left">参数</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">必填</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">AccessKeyId</td>
<td style="text-align: left">keyid</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">Timestamp</td>
<td style="text-align: left">时间戳当前时间毫秒数</td>
<td style="text-align: left">是</td>
<td style="text-align: left">gmt 时间</td>
</tr>
<tr>
<td style="text-align: left">Signature</td>
<td style="text-align: left">签名</td>
<td style="text-align: left">是</td>
<td style="text-align: left">参考<a href="#%E7%BD%91%E5%85%B3URL%E7%AD%BE%E5%90%8D%E6%9C%BA%E5%88%B6">网关URL签名机制</a></td>
</tr>
<tr>
<td style="text-align: left">SignatureVersion</td>
<td style="text-align: left">签名版本</td>
<td style="text-align: left">是</td>
<td style="text-align: left">1</td>
</tr>
<tr>
<td style="text-align: left">SignatureMethod</td>
<td style="text-align: left">签名方法</td>
<td style="text-align: left">是</td>
<td style="text-align: left">HmacSHA256</td>
</tr>
<tr>
<td style="text-align: left">variety</td>
<td style="text-align: left">数字货币币种</td>
<td style="text-align: left">是</td>
<td style="text-align: left">USDT</td>
</tr>
<tr>
<td style="text-align: left">currency</td>
<td style="text-align: left">法币币种</td>
<td style="text-align: left">是</td>
<td style="text-align: left">CNY</td>
</tr>
<tr>
<td style="text-align: left">amount</td>
<td style="text-align: left">金额、币数量</td>
<td style="text-align: left">是</td>
<td style="text-align: left">法币保留两位小数， 数字货币保留四位</td>
</tr>
<tr>
<td style="text-align: left">unit</td>
<td style="text-align: left">amount 单位</td>
<td style="text-align: left">是</td>
<td style="text-align: left">2- amount 为数字货币数量</td>
</tr>
<tr>
<td style="text-align: left">outOrderSn</td>
<td style="text-align: left">商户订单号</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">outUid</td>
<td style="text-align: left">商户用户唯一标识</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">name</td>
<td style="text-align: left">商户用户真实姓名</td>
<td style="text-align: left">否</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">idNumber</td>
<td style="text-align: left">商户用户身份证号</td>
<td style="text-align: left">否</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">mobile</td>
<td style="text-align: left">商户用户手机号</td>
<td style="text-align: left">否</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">callback</td>
<td style="text-align: left">下单成功同步回调地址</td>
<td style="text-align: left">是</td>
<td style="text-align: left">用来接收订单信息</td>
</tr>
<tr>
<td style="text-align: left">redirect</td>
<td style="text-align: left">交易完成时要跳转到商户的地址</td>
<td style="text-align: left">否</td>
<td style="text-align: left"></td>
</tr>
</tbody>
</table>

<p><strong>响应数据:</strong></p>

<p>redirect 到 your web site 的callback地址 （图中第4步）</p>

<div><pre><code class="language-none">http://{callback}?orderSn=OTC00162173&amp;outOrderSn=ordersn123&amp;result=1&amp;msg=&amp;totalAmount=100&amp;amount=10&amp;price=6.7&amp;SignatureMethod=HmacSHA256&amp;Signature=SmH8OzgmheDOHOJGzLVp0/DMA7CIPcEg6Wb0e38mqww=</code></pre></div>

<table>
<thead>
<tr>
<th style="text-align: left">参数</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">orderSn</td>
<td style="text-align: left">BDD订单编号</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">outOrderSn</td>
<td style="text-align: left">商户订单号</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">total</td>
<td style="text-align: left">法币金额</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">price</td>
<td style="text-align: left">单价</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">amount</td>
<td style="text-align: left">数字货币数量</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">result</td>
<td style="text-align: left">下单状态</td>
<td style="text-align: left">1-下单成功 2-订单已取消 3-余额不足 4-其他错误</td>
</tr>
<tr>
<td style="text-align: left">msg</td>
<td style="text-align: left">错误信息</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">Timestamp</td>
<td style="text-align: left">时间戳</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">SignatureMethod</td>
<td style="text-align: left">签名法法</td>
<td style="text-align: left">HmacSHA256</td>
</tr>
<tr>
<td style="text-align: left">Signature</td>
<td style="text-align: left">签名</td>
<td style="text-align: left">见下方验签方法 ，<a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">验签秘钥</a> 中的秘钥</td>
</tr>
</tbody>
</table>

<p><strong>验签</strong></p>

<p>1.去掉请求url 中的 Signature 参数得到下面的url</p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/confirmPay?AccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-12-18T11%3A42%3A10.257Z&amp;outOrderSn=134567     </code></pre></div>

<p>2.对上面的url进行签名，秘钥见：<a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">验签秘钥</a></p>

<p>3.将得到的签名与，url中Signature decode之后的值比较，相同则为验签成功</p>

<h3 id="toc_8">2. 网关确认付款页面（图中第5步）</h3>

<p>生成网关确认付款页面url</p>

<p><strong>样例如下：</strong></p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/confirmPay?AccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-12-18T11%3A42%3A10.257Z&amp;outOrderSn=134567&amp;Signature=q2oVnQHoTSyN5DkaWRk/KYWSn9i9l6L8mWiA1hgoryk=</code></pre></div>

<p><strong>请求参数：</strong></p>

<table>
<thead>
<tr>
<th style="text-align: left">参数</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">必填</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">AccessKeyId</td>
<td style="text-align: left">keyid</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">Timestamp</td>
<td style="text-align: left">时间戳当前时间毫秒数</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">Signature</td>
<td style="text-align: left">签名</td>
<td style="text-align: left">是</td>
<td style="text-align: left">参考<a href="#%E7%BD%91%E5%85%B3URL%E7%AD%BE%E5%90%8D%E6%9C%BA%E5%88%B6">网关URL签名机制</a></td>
</tr>
<tr>
<td style="text-align: left">SignatureVersion</td>
<td style="text-align: left">签名版本</td>
<td style="text-align: left">是</td>
<td style="text-align: left">1</td>
</tr>
<tr>
<td style="text-align: left">SignatureMethod</td>
<td style="text-align: left">签名方法</td>
<td style="text-align: left">是</td>
<td style="text-align: left">HmacSHA256</td>
</tr>
<tr>
<td style="text-align: left">outOrderSn</td>
<td style="text-align: left">商户订单号</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
</tbody>
</table>

<h3 id="toc_9">接收异步通知</h3>

<ol>
<li><a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">设置接收地址和秘钥</a></li>
<li><a href="#%E8%AE%A2%E5%8D%95%E7%8A%B6%E6%80%81%E6%94%B9%E5%8F%98%E5%BC%82%E6%AD%A5%E9%80%9A%E7%9F%A5url">通知消息结构</a></li>
<li><a href="#%E5%85%A5%E9%87%91%E6%B6%88%E6%81%AF%E6%A0%B7%E4%BE%8B">入金订单通知消息格式</a></li>
</ol>

<h1 id="toc_10"><a name="出金">三.出金</a></h1>

<h1 id="toc_11"><font color="red">调用出金API时请您先冻结您系统的用户资产，以免用户多出金造成损失</font></h1>

<h2 id="toc_12">出金流程</h2>

<p><img src="https://raw.githubusercontent.com/bddpay/APIDOC/master/flow_sell.jpg" alt=""></p>

<h2 id="toc_13">接入准备</h2>

<ol>
<li><a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">设置AccessKey 和 SecretKey</a></li>
<li>下单成功后接收同步通知的callback地址（上图第4、7部需要）</li>
<li><a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">设置异步通知接收地址和秘钥</a></li>
</ol>

<h2 id="toc_14"><a name="出金下单" style="color:#000000">出金下单（图中第2步）</a></h2>

<div><pre><code class="language-none">POST /v1/api/open/order/sell</code></pre></div>

<h3 id="toc_15">请求参数:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
<th>默认值</th>
<th>取值范围</th>
</tr>
</thead>

<tbody>
<tr>
<td>variety</td>
<td>是</td>
<td>string</td>
<td>币种</td>
<td>usdt</td>
<td>usdt</td>
</tr>
<tr>
<td>currency</td>
<td>是</td>
<td>string</td>
<td>法币类型</td>
<td></td>
<td>CNY</td>
</tr>
<tr>
<td>amount</td>
<td>是</td>
<td>string</td>
<td>币数量</td>
<td></td>
<td>大于0,最多8位小数</td>
</tr>
<tr>
<td>unit</td>
<td>是</td>
<td>int</td>
<td>单位</td>
<td>2</td>
<td>2- amount 为币数量</td>
</tr>
<tr>
<td>outUid</td>
<td>是</td>
<td>string</td>
<td>商户用户id</td>
<td></td>
<td></td>
</tr>
<tr>
<td>outOrderSn</td>
<td>是</td>
<td>string</td>
<td>商户订单号</td>
<td></td>
<td></td>
</tr>
<tr>
<td>paymentType</td>
<td>是</td>
<td>int</td>
<td>支付方式</td>
<td>无</td>
<td>1-支付宝 2-微信 3-银行卡</td>
</tr>
<tr>
<td>name</td>
<td>是</td>
<td>string</td>
<td>收款人姓名</td>
<td></td>
<td></td>
</tr>
<tr>
<td>number</td>
<td>是</td>
<td>string</td>
<td>收款人银行卡账号</td>
<td></td>
<td></td>
</tr>
<tr>
<td>bank</td>
<td>是</td>
<td>string</td>
<td>收款人银行名称</td>
<td></td>
<td></td>
</tr>
<tr>
<td>bankName</td>
<td>是</td>
<td>string</td>
<td>收款人开户行</td>
<td></td>
<td></td>
</tr>
<tr>
<td>idNumber</td>
<td>是</td>
<td>string</td>
<td>收款用户身份证号</td>
<td></td>
<td></td>
</tr>
<tr>
<td>AccessKeyId</td>
<td>是</td>
<td>string</td>
<td>KeyId</td>
<td></td>
<td>即Access Key， <a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">如何获取Access Key</a></td>
</tr>
<tr>
<td>SignatureVersion</td>
<td>是</td>
<td>int</td>
<td>签名版本</td>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td>SignatureMethod</td>
<td>是</td>
<td>string</td>
<td>签名方法</td>
<td>HmacSHA256</td>
<td>HmacSHA256</td>
</tr>
<tr>
<td>Timestamp</td>
<td>是</td>
<td>string</td>
<td>UTC时间戳</td>
<td></td>
<td>2018-10-26T08:55:45</td>
</tr>
<tr>
<td>Signature</td>
<td>是</td>
<td>string</td>
<td>签名</td>
<td></td>
<td><a href="#API%E7%AD%BE%E5%90%8D%E6%96%B9%E6%B3%95">见API签名方法</a></td>
</tr>
</tbody>
</table>

<h3 id="toc_16">响应数据:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>code</td>
<td>是</td>
<td>int</td>
<td>调用状态，成功 code = 200</td>
</tr>
<tr>
<td>body</td>
<td>是</td>
<td>json</td>
<td>消息体，为订单信息</td>
</tr>
<tr>
<td>msg</td>
<td>否</td>
<td>string</td>
<td>错误信息</td>
</tr>
</tbody>
</table>

<p><strong>body中的订单信息</strong></p>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>tradeSn</td>
<td>string</td>
<td>BDD平台订单号</td>
</tr>
<tr>
<td>amount</td>
<td>string</td>
<td>卖币数量</td>
</tr>
<tr>
<td>merchantFee</td>
<td>string</td>
<td>手续费</td>
</tr>
<tr>
<td>status</td>
<td>int</td>
<td>订单状态 1-已创建（未审核） 2-未付款 3-已付款待收款 4-已成交 5-已取消</td>
</tr>
<tr>
<td>createTime</td>
<td>long</td>
<td>下单时间的毫秒数</td>
</tr>
</tbody>
</table>

<h3 id="toc_17">请求示例:</h3>

<div><pre><code class="language-none">{
    &quot;variety&quot;: &quot;usdt&quot;,
    &quot;currency&quot;: &quot;CNY&quot;,
    &quot;unit&quot;:&quot;2&quot;,
    &quot;amount&quot;: &quot;10000&quot;,
    &quot;unit&quot;: &quot;2&quot;,
    &quot;outUid&quot;:&quot;uid12323&quot;,
    &quot;outOrderSn&quot;:&quot;order23424234&quot;,
    &quot;name&quot;: &quot;张三&quot;,
    &quot;paymentType&quot;: 1,
    &quot;number&quot;: &quot;4392267537606583&quot;,
    &quot;bank&quot;: &quot;招商银行&quot;,
    &quot;bankName&quot;: &quot;中国招商银行中关村支行&quot;,
    &quot;idNumber&quot;: &quot;41124523&quot;,
    &quot;AccessKeyId&quot;:&quot;sdfsdfsd&quot;,
    &quot;SignatureVersion&quot;:&quot;1&quot;,
    &quot;SignatureMethod&quot;:&quot;HmacSHA256&quot;,
    &quot;Timestamp&quot;:&quot;2018-12-18T11:34:53&quot;,
    &quot;Signature&quot;:&quot;sdfsdfsdfsdsdf&quot;
}</code></pre></div>

<h3 id="toc_18">响应示例:</h3>

<div><pre><code class="language-none">{
    &quot;body&quot;: {
        &quot;tradeSn&quot;: &quot;B00000212&quot;,
        &quot;merchantFee&quot;: &quot;0.00&quot;,
        &quot;status&quot;: 1,
        &quot;amount&quot;: &quot;1000.00000000&quot;,
        &quot;createTime&quot;: 1546576293000,
    },
    &quot;code&quot;: 200,
    &quot;msg&quot;: null
}</code></pre></div>

<h2 id="toc_19"><a name="出金订单详情" style="color:#000000">网关出金订单详情 （图中第5步）</a></h2>

<p>生成BDD网关订单详情url</p>

<p><strong>样例：</strong></p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/receipt?AccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-12-18T11%3A34%3A53.989Z&amp;outOrderSn=1545132894045&amp;Signature=E++6HXtjVg3Ib3SmOIyRAZTFtdX5VVNcDpKyvWbNlZI=</code></pre></div>

<p><strong>请求参数:</strong></p>

<table>
<thead>
<tr>
<th style="text-align: left">参数</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">必填</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">AccessKeyId</td>
<td style="text-align: left">keyid</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">Timestamp</td>
<td style="text-align: left">时间戳当前时间毫秒数</td>
<td style="text-align: left">是</td>
<td style="text-align: left">gmt 时间</td>
</tr>
<tr>
<td style="text-align: left">Signature</td>
<td style="text-align: left">签名</td>
<td style="text-align: left">是</td>
<td style="text-align: left">参数名称按字母顺序正序排列后签名，例如:key1=value1&amp;key2=value2&amp;key3=value3</td>
</tr>
<tr>
<td style="text-align: left">SignatureVersion</td>
<td style="text-align: left">签名版本</td>
<td style="text-align: left">是</td>
<td style="text-align: left">1</td>
</tr>
<tr>
<td style="text-align: left">SignatureMethod</td>
<td style="text-align: left">签名方法</td>
<td style="text-align: left">是</td>
<td style="text-align: left">HmacSHA256</td>
</tr>
<tr>
<td style="text-align: left">outOrderSn</td>
<td style="text-align: left">商户订单号</td>
<td style="text-align: left">是</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">redirect</td>
<td style="text-align: left">交易完成时要跳转到商户的地址</td>
<td style="text-align: left">否</td>
<td style="text-align: left"></td>
</tr>
</tbody>
</table>

<h3 id="toc_20">接收异步通知</h3>

<ol>
<li><a href="#%E5%BC%82%E6%AD%A5%E6%8E%A8%E9%80%81%E8%AE%BE%E7%BD%AE">设置接收地址和秘钥</a></li>
<li><a href="#%E8%AE%A2%E5%8D%95%E7%8A%B6%E6%80%81%E6%94%B9%E5%8F%98%E5%BC%82%E6%AD%A5%E9%80%9A%E7%9F%A5url">通知消息结构</a></li>
<li><a href="https://github.com/bddpay/APIDOC/wiki/%E5%BC%82%E6%AD%A5%E9%80%9A%E7%9F%A5#%E5%87%BA%E9%87%91">出金订单通知消息格式</a></li>
</ol>

<h1 id="toc_21"><a name="其它API接口">四.其他API接口</a></h1>

<h2 id="toc_22"><a name="查询价格接口" style="color:#000000">查询价格接口</a></h2>

<div><pre><code class="language-none">POST /v1/api/open/price</code></pre></div>

<h3 id="toc_23">请求参数:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
<th>默认值</th>
<th>取值范围</th>
</tr>
</thead>

<tbody>
<tr>
<td>variety</td>
<td>是</td>
<td>string</td>
<td>币种</td>
<td>usdt</td>
<td>usdt</td>
</tr>
<tr>
<td>currency</td>
<td>是</td>
<td>string</td>
<td>法币类型</td>
<td></td>
<td>CNY</td>
</tr>
<tr>
<td>AccessKeyId</td>
<td>是</td>
<td>string</td>
<td>KeyId</td>
<td></td>
<td>即Access Key， <a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">如何获取Access Key</a></td>
</tr>
<tr>
<td>SignatureVersion</td>
<td>是</td>
<td>int</td>
<td>签名版本</td>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td>SignatureMethod</td>
<td>是</td>
<td>string</td>
<td>签名方法</td>
<td>HmacSHA256</td>
<td>HmacSHA256</td>
</tr>
<tr>
<td>Timestamp</td>
<td>是</td>
<td>string</td>
<td>UTC时间戳</td>
<td></td>
<td>2018-10-26T08:55:45</td>
</tr>
<tr>
<td>Signature</td>
<td>是</td>
<td>string</td>
<td>签名</td>
<td></td>
<td><a href="#API%E7%AD%BE%E5%90%8D%E6%96%B9%E6%B3%95">见API签名方法</a></td>
</tr>
</tbody>
</table>

<h3 id="toc_24">响应数据:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>code</td>
<td>是</td>
<td>int</td>
<td>调用状态，成功 code = 200</td>
</tr>
<tr>
<td>body</td>
<td>是</td>
<td>json</td>
<td>消息体，为订单信息</td>
</tr>
<tr>
<td>msg</td>
<td>否</td>
<td>string</td>
<td>错误信息</td>
</tr>
</tbody>
</table>

<p><strong>body的数据</strong></p>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>variety</td>
<td>string</td>
<td>币种代码</td>
</tr>
<tr>
<td>currency</td>
<td>string</td>
<td>货币代码</td>
</tr>
<tr>
<td>buy</td>
<td>string</td>
<td>入金价格</td>
</tr>
<tr>
<td>sell</td>
<td>string</td>
<td>出金价格</td>
</tr>
</tbody>
</table>

<h3 id="toc_25">请求示例:</h3>

<div><pre><code class="language-none">{
    &quot;variety&quot;: &quot;usdt&quot;,
    &quot;currency&quot;: &quot;CNY&quot;
   &quot;AccessKeyId&quot;:&quot;sdfsdfsd&quot;,
    &quot;SignatureVersion&quot;:&quot;1&quot;,
    &quot;SignatureMethod&quot;:&quot;HmacSHA256&quot;,
    &quot;Timestamp&quot;:&quot;2018-12-18T11:34:53&quot;,
    &quot;Signature&quot;:&quot;sdfsdfsdfsdsdf&quot;
}</code></pre></div>

<h3 id="toc_26">响应示例:</h3>

<div><pre><code class="language-none">{
    &quot;body&quot;: {
        &quot;variety&quot;: &quot;usdt&quot;,
        &quot;currency&quot;: &quot;CNY&quot;
        &quot;buy&quot;: &quot;1000.00000000&quot;,
        &quot;sell&quot;: &quot;1000.00000000&quot;
    },
    &quot;code&quot;: 200,
    &quot;msg&quot;: null
}</code></pre></div>

<h2 id="toc_27"><a name="获取账户信息" style="color:#000000">获取账户信息</a></h2>

<div><pre><code class="language-none">POST /v1/api/open/account</code></pre></div>

<h3 id="toc_28">请求参数:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
<th>默认值</th>
<th>取值范围</th>
</tr>
</thead>

<tbody>
<tr>
<td>AccessKeyId</td>
<td>是</td>
<td>string</td>
<td>KeyId</td>
<td></td>
<td>即Access Key， <a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">如何获取Access Key</a></td>
</tr>
<tr>
<td>SignatureVersion</td>
<td>是</td>
<td>int</td>
<td>签名版本</td>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td>SignatureMethod</td>
<td>是</td>
<td>string</td>
<td>签名方法</td>
<td>HmacSHA256</td>
<td>HmacSHA256</td>
</tr>
<tr>
<td>Timestamp</td>
<td>是</td>
<td>string</td>
<td>UTC时间戳</td>
<td></td>
<td>2018-10-26T08:55:45</td>
</tr>
<tr>
<td>Signature</td>
<td>是</td>
<td>string</td>
<td>签名</td>
<td></td>
<td><a href="#API%E7%AD%BE%E5%90%8D%E6%96%B9%E6%B3%95">见API签名方法</a></td>
</tr>
</tbody>
</table>

<h3 id="toc_29">响应数据:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>code</td>
<td>是</td>
<td>int</td>
<td>调用状态，成功 code = 200</td>
</tr>
<tr>
<td>body</td>
<td>是</td>
<td>json</td>
<td>消息体，为订单信息</td>
</tr>
<tr>
<td>msg</td>
<td>否</td>
<td>string</td>
<td>错误信息</td>
</tr>
</tbody>
</table>

<p><strong>body的数据</strong></p>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>varietyCode</td>
<td>string</td>
<td>币种代码</td>
</tr>
<tr>
<td>balance</td>
<td>string</td>
<td>余额，余额=可用+冻结</td>
</tr>
<tr>
<td>available</td>
<td>string</td>
<td>可用</td>
</tr>
<tr>
<td>freeze</td>
<td>string</td>
<td>冻结</td>
</tr>
</tbody>
</table>

<h3 id="toc_30">请求示例:</h3>

<div><pre><code class="language-none">{
    &quot;AccessKeyId&quot;:&quot;sdfsdfsd&quot;,
    &quot;SignatureVersion&quot;:&quot;1&quot;,
    &quot;SignatureMethod&quot;:&quot;HmacSHA256&quot;,
    &quot;Timestamp&quot;:&quot;2018-12-18T11:34:53&quot;,
    &quot;Signature&quot;:&quot;sdfsdfsdfsdsdf&quot;
}</code></pre></div>

<h3 id="toc_31">响应示例:</h3>

<div><pre><code class="language-none">{
    &quot;body&quot;: [
        {
          varietyCode:&quot;usdt&quot;,
          balance:&quot;200&quot;,
          available:&quot;100&quot;,
          freeze:&quot;100&quot;
        }
     ],
    &quot;code&quot;: 200,
    &quot;msg&quot;: null
}</code></pre></div>

<h2 id="toc_32"><a name="获取订单详情" style="color:#000000">获取订单详情</a></h2>

<div><pre><code class="language-none">POST /v1/api/open/order/detail</code></pre></div>

<h3 id="toc_33">请求参数:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
<th>默认值</th>
<th>取值范围</th>
</tr>
</thead>

<tbody>
<tr>
<td>outOrderSn</td>
<td>是</td>
<td>string</td>
<td>商户订单号</td>
<td></td>
<td></td>
</tr>
<tr>
<td>AccessKeyId</td>
<td>是</td>
<td>string</td>
<td>KeyId</td>
<td></td>
<td>即Access Key， <a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">如何获取Access Key</a></td>
</tr>
<tr>
<td>SignatureVersion</td>
<td>是</td>
<td>int</td>
<td>签名版本</td>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td>SignatureMethod</td>
<td>是</td>
<td>string</td>
<td>签名方法</td>
<td>HmacSHA256</td>
<td>HmacSHA256</td>
</tr>
<tr>
<td>Timestamp</td>
<td>是</td>
<td>string</td>
<td>UTC时间戳</td>
<td></td>
<td>2018-10-26T08:55:45</td>
</tr>
<tr>
<td>Signature</td>
<td>是</td>
<td>string</td>
<td>签名</td>
<td></td>
<td><a href="#API%E7%AD%BE%E5%90%8D%E6%96%B9%E6%B3%95">见API签名方法</a></td>
</tr>
</tbody>
</table>

<h3 id="toc_34">响应数据:</h3>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>是否必须</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>code</td>
<td>是</td>
<td>int</td>
<td>调用状态，成功 code = 200</td>
</tr>
<tr>
<td>body</td>
<td>是</td>
<td>json</td>
<td>消息体，为订单信息</td>
</tr>
<tr>
<td>msg</td>
<td>否</td>
<td>string</td>
<td>错误信息</td>
</tr>
</tbody>
</table>

<p><strong>body的数据</strong></p>

<table>
<thead>
<tr>
<th>参数名称</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead>

<tbody>
<tr>
<td>tradeSn</td>
<td>string</td>
<td>平台订单号</td>
</tr>
<tr>
<td>outOrderSn</td>
<td>string</td>
<td>商户订单号</td>
</tr>
<tr>
<td>outUid</td>
<td>string</td>
<td>商户用户id</td>
</tr>
<tr>
<td>type</td>
<td>int</td>
<td>订单类型 1-买 2-卖</td>
</tr>
<tr>
<td>varietyCode</td>
<td>string</td>
<td>币种代码</td>
</tr>
<tr>
<td>status</td>
<td>string</td>
<td>订单状态</td>
</tr>
<tr>
<td>amount</td>
<td>string</td>
<td>币数量</td>
</tr>
<tr>
<td>dealPrice</td>
<td>string</td>
<td>单价</td>
</tr>
<tr>
<td>merchantFee</td>
<td>string</td>
<td>手续费</td>
</tr>
<tr>
<td>total</td>
<td>string</td>
<td>法币总额</td>
</tr>
<tr>
<td>createTime</td>
<td>string</td>
<td>订单创建时间</td>
</tr>
<tr>
<td>approveStatus</td>
<td>string</td>
<td>出金订单审核状态，1-待审核 2-通过 3-拒绝</td>
</tr>
<tr>
<td>approveTime</td>
<td>string</td>
<td>卖币审核时间</td>
</tr>
<tr>
<td>applyTime</td>
<td>string</td>
<td>匹配到承兑商时间</td>
</tr>
<tr>
<td>payTime</td>
<td>long</td>
<td>确认付款时间</td>
</tr>
<tr>
<td>confirmTime</td>
<td>string</td>
<td>确认收款时间</td>
</tr>
<tr>
<td>cancelStatus</td>
<td>int</td>
<td>取消类型 1-用户 2-系统</td>
</tr>
<tr>
<td>cancelTime</td>
<td>long</td>
<td>取消时间</td>
</tr>
<tr>
<td>appealStatus</td>
<td>int</td>
<td>申诉状态</td>
</tr>
<tr>
<td>appeal</td>
<td>string</td>
<td></td>
</tr>
<tr>
<td>appealId</td>
<td>string</td>
<td>申诉编号</td>
</tr>
<tr>
<td>appealTime</td>
<td>long</td>
<td>申诉时间</td>
</tr>
<tr>
<td>appealDealTime</td>
<td>long</td>
<td>申诉关闭时间</td>
</tr>
<tr>
<td>endPayTime</td>
<td>long</td>
<td>付款截止时间，还有多少毫秒付款超时</td>
</tr>
<tr>
<td>providerName</td>
<td>string</td>
<td>承兑商姓名</td>
</tr>
<tr>
<td>paymentJson</td>
<td>string</td>
<td></td>
</tr>
<tr>
<td>endConfirmTime</td>
<td>long</td>
<td>截止确认收款时间</td>
</tr>
</tbody>
</table>

<h3 id="toc_35">请求示例:</h3>

<div><pre><code class="language-none">{
    &quot;outOrderSn&quot;:&quot;os234234234&quot;,
    &quot;AccessKeyId&quot;:&quot;sdfsdfsd&quot;,
    &quot;SignatureVersion&quot;:&quot;1&quot;,
    &quot;SignatureMethod&quot;:&quot;HmacSHA256&quot;,
    &quot;Timestamp&quot;:&quot;2018-12-18T11:34:53&quot;,
    &quot;Signature&quot;:&quot;sdfsdfsdfsdsdf&quot;
}</code></pre></div>

<h3 id="toc_36">响应示例:</h3>

<div><pre><code class="language-none">{
    &quot;code&quot;:200,
    &quot;body&quot;:{
        &quot;approveStatus&quot;:2,
        &quot;sysType&quot;:2,
        &quot;appealStatus&quot;:1,
        &quot;dealPrice&quot;:&quot;7.00&quot;,
        &quot;idNumber&quot;:&quot;150981197202284550&quot;,
        &quot;type&quot;:1,
        &quot;total&quot;:&quot;7000.00&quot;,
        &quot;varietyId&quot;:1,
        &quot;endPayTime&quot;:4158298,
        &quot;currency&quot;:1,
        &quot;approveTime&quot;:1546576293000,
        &quot;applyTime&quot;:1546576293000,
        &quot;providerName&quot;:&quot;xxxx&quot;,
        &quot;amount&quot;:&quot;1000.00000000&quot;,
        &quot;paymentJson&quot;:[
            {
                &quot;number&quot;:&quot;18811478037&quot;,
                &quot;name&quot;:&quot;xxxx&quot;,
                &quot;type&quot;:2,
                &quot;url&quot;:&quot;http://www.xxx.com/pay/7b9aaef27a1af3550e06f65174ab79d6%21small?OSSAccessKeyId=LTAIMoSidU9xd2Nv&amp;Signature=JkxOYZ0hIoEQhMecdrRCPwBP3vo%3D&amp;Expires=1546578093&quot;
            },
            {
                &quot;number&quot;:&quot;6222620910029698556&quot;,
                &quot;bank&quot;:&quot;交通银行&quot;,
                &quot;name&quot;:&quot;xxxx,
                &quot;bankName&quot;:&quot;支行&quot;,
                &quot;type&quot;:3
            }
        ],
        &quot;merchantFee&quot;:&quot;0.00&quot;,
        &quot;mobile&quot;:&quot;&quot;,
        &quot;outUid&quot;:&quot;121212&quot;,
        &quot;createTime&quot;:1546576293000,
        &quot;varietyCode&quot;:&quot;usdt&quot;,
        &quot;outOrderSn&quot;:&quot;7274c0c3-899b-4c7f-8f0c-743b0fd2c4c2&quot;,
        &quot;name&quot;:&quot;xxxx&quot;,
        &quot;tradeSn&quot;:&quot;B00000212&quot;,
        &quot;tradeId&quot;:212,
        &quot;status&quot;:2,
        &quot;otcOrderSn&quot;:&quot;OTC00164250&quot;
    }
}</code></pre></div>

<h1 id="toc_37"><a name="异步通知">五.异步通知</a></h1>

<p>通过API 下单后， 当订单任何状态的变化，包括 申诉 都会进行异步通知。</p>

<h2 id="toc_38"><a name="异步推送设置" style="color:#000000">第一步注册订单异步通知回调地址</a></h2>

<p><img src="https://raw.githubusercontent.com/bddpay/APIDOC/master/intro_notify.jpg" alt=""></p>

<p>在回调地址一栏中填写订单回调地址。 例如 http://www.xxx.com/order/nofity</p>

<p>同时请填写密钥  例如 xxxxxxxxx</p>

<h2 id="toc_39">消息订阅确认</h2>

<p>你需要确保 http://www.xxx.com/order/nofity 这个地址可以外网访问， 每当你保存回调地址的时候， 平台会给你的回调地址［http://www.xxx.com/order/nofity］ 发送确认订阅的请求 请求类型  Content-Type: application/json 或 Content-Type: text/plain 你的服务［http://www.xxx.com/order/nofity］需要同时支持</p>

<p>我们将向您的服务器发送如下请求：</p>

<div><pre><code class="language-none">
POST / HTTP/1.1
    x-amz-sns-message-type: SubscriptionConfirmation
    x-amz-sns-message-id: 165545c9-2a5c-472c-8df2-7ff2be2b3b1b
    x-amz-sns-topic-arn: arn:aws:sns:us-west-2:123456789012:MyTopic
    Content-Length: 1336
    Content-Type: application/json; charset=UTF-8
    Host: www.xxx.com
    Connection: Keep-Alive
    User-Agent: Amazon Simple Notification Service Agent

    {
      &quot;Type&quot; : &quot;SubscriptionConfirmation&quot;,
      &quot;MessageId&quot; : &quot;165545c9-2a5c-472c-8df2-7ff2be2b3b1b&quot;,
      &quot;Token&quot; : &quot;2336412f37fb687f5d51e6e241d09c805a5a57b30d712f794cc5f6a988666d92768dd60a747ba6f3beb71854e285d6ad02428b09ceece29417f1f02d609c582afbacc99c583a916b9981dd2728f4ae6fdb82efd087cc3b7849e05798d2d2785c03b0879594eeac82c01f235d0e717736&quot;,
      &quot;TopicArn&quot; : &quot;arn:aws:sns:us-west-2:123456789012:MyTopic&quot;,
      &quot;Message&quot; : &quot;You have chosen to subscribe to the topic arn:aws:sns:us-west-2:123456789012:MyTopic.\nTo confirm the subscription, visit the SubscribeURL included in this message.&quot;,
      &quot;SubscribeURL&quot; : &quot;https://sns.us-west-2.amazonaws.com/?Action=ConfirmSubscription&amp;TopicArn=arn:aws:sns:us-west-2:123456789012:MyTopic&amp;Token=2336412f37fb687f5d51e6e241d09c805a5a57b30d712f794cc5f6a988666d92768dd60a747ba6f3beb71854e285d6ad02428b09ceece29417f1f02d609c582afbacc99c583a916b9981dd2728f4ae6fdb82efd087cc3b7849e05798d2d2785c03b0879594eeac82c01f235d0e717736&quot;,
      &quot;Timestamp&quot; : &quot;2012-04-26T20:45:04.751Z&quot;,
      &quot;SignatureVersion&quot; : &quot;1&quot;,
      &quot;Signature&quot; : &quot;EXAMPLEpH+DcEwjAPg8O9mY8dReBSwksfg2S7WKQcikcNKWLQjwu6A4VbeS0QHVCkhRS7fUQvi2egU3N858fiTDN6bkkOxYDVrY0Ad8L10Hs3zH81mtnPk5uvvolIC1CXGu43obcgFxeL3khZl8IKvO61GWB6jI9b5+gLPoBc1Q=&quot;,
      &quot;SigningCertURL&quot; : &quot;https://sns.us-west-2.amazonaws.com/SimpleNotificationService-f3ecfb7224c7233fe7bb5f59f96de52f.pem&quot;
    }
</code></pre></div>

<p>当您收入请求时，请用程序取出 SubscribeURL 这个字段中的URL， 进行请求  例如
<code>
GET  https://sns.us-west-2.amazonaws.com/?Action=ConfirmSubscription&amp;TopicArn=arn:aws:sns:us-west-2:123456789012:MyTopic&amp;Token=2336412f37fb687f5d51e6e241d09c805a5a57b30d712f794cc5f6a988666d92768dd60a747ba6f3beb71854e285d6ad02428b09ceece29417f1f02d609c582afbacc99c583a916b9981dd2728f4ae6fdb82efd087cc3b7849e05798d2d2785c03b0879594eeac82c01f235d0e717736
</code></p>

<p>订阅成功您会收到如下信息：</p>

<div><pre><code class="language-none">&lt;ConfirmSubscriptionResponse xmlns=&quot;http://sns.amazonaws.com/doc/2010-03-31/&quot;&gt;
      &lt;ConfirmSubscriptionResult&gt;
        &lt;SubscriptionArn&gt;arn:aws:sns:us-west-2:123456789012:MyTopic:2bcfbf39-05c3-41de-beaa-fcfcc21c8f55&lt;/SubscriptionArn&gt;
      &lt;/ConfirmSubscriptionResult&gt;
      &lt;ResponseMetadata&gt;
        &lt;RequestId&gt;075ecce8-8dac-11e1-bf80-f781d96e9307&lt;/RequestId&gt;
      &lt;/ResponseMetadata&gt;
    &lt;/ConfirmSubscriptionResponse&gt;</code></pre></div>

<p>以上完成后， 当您的订单有变化您会收到订单通知</p>

<h2 id="toc_40">取消订阅的确认消息(收到该类型的消息需要再次访问该消息中的SubscribeURL即可重新订阅):</h2>

<div><pre><code class="language-none">POST / HTTP/1.1
    x-amz-sns-message-type: UnsubscribeConfirmation
    x-amz-sns-message-id: da41e39f-ea4d-435a-b922-c6aae3915ebe
    x-amz-sns-topic-arn: arn:aws:sns:us-west-2:123456789012:MyTopic
    x-amz-sns-subscription-arn: arn:aws:sns:us-west-2:123456789012:MyTopic:2bcfbf39-05c3-41de-beaa-fcfcc21c8f55
    Content-Length: 761
    Content-Type: application/json; charset=UTF-8
    Host: ec2-50-17-44-49.compute-1.amazonaws.com
    Connection: Keep-Alive
    User-Agent: Amazon Simple Notification Service Agent
{
    &quot;Type&quot;:&quot;UnsubscribeConfirmation&quot;,
    &quot;MessageId&quot;:&quot;9ac15f98-7cb6-4d23-9ed5-3d33f1e97858&quot;,
    &quot;Token&quot;:&quot;2336412f37fb687f5d51e6e241dbca52ee34abd0180a0f1cde2bf8721f28156ddc79306c5a22b7b128eaa19bb2a830b7f32d0d5ed1a468eb47e488d545d61a52dbc9b2213d51ff5316777899ccf5d03e6fa5b2a7a2533ae4303a252ff621d1da8e390137beb60d73517958ba378d40897dbd75c556cdadec2f33d0efa4c6f3f4&quot;,
    &quot;TopicArn&quot;:&quot;arn:aws:sns:us-east-2:713166642506:T_BDD_PAYMENT_CHANNEL_UID_8&quot;,
    &quot;Message&quot;:&quot;You have chosen to deactivate subscription arn:aws:sns:us-east-2:713166642506:T_BDD_PAYMENT_CHANNEL_UID_8:565fca06-8ea9-470d-a781-7ee925214470.
To cancel this operation and restore the subscription, visit the SubscribeURL included in this message.&quot;,
    &quot;SubscribeURL&quot;:&quot;https://sns.us-east-2.amazonaws.com/?Action=ConfirmSubscription&amp;TopicArn=arn:aws:sns:us-east-2:713166642506:T_BDD_PAYMENT_CHANNEL_UID_8&amp;Token=2336412f37fb687f5d51e6e241dbca52ee34abd0180a0f1cde2bf8721f28156ddc79306c5a22b7b128eaa19bb2a830b7f32d0d5ed1a468eb47e488d545d61a52dbc9b2213d51ff5316777899ccf5d03e6fa5b2a7a2533ae4303a252ff621d1da8e390137beb60d73517958ba378d40897dbd75c556cdadec2f33d0efa4c6f3f4&quot;,
    &quot;Timestamp&quot;:&quot;2019-01-11T09:19:45.178Z&quot;,
    &quot;SignatureVersion&quot;:&quot;1&quot;,
    &quot;Signature&quot;:&quot;UFbbzQi0nmtsCTCNoUysf7JRQbAivw8DMNbrOfi2AGd/n+jBmzu3Y25b2BFDZrfZMrhxFf8m0zvTB4+hzZlIkVSb66J4X3Eac3jQnPWohMFJc+0P+DceUBUwMl6q7tlMmqQG8YvVnrGiE20uE5V/3OxuighX7Mo92xbAwG49m0LAWDP0eVGuBgPGDtmVUG1KmFXh/IeEY4ptJOQA05hb+8/3hxZWiidtHI8OdWDcIMRvL/8O4K9qFGuYgKMmuSPWIPm6zbYNZaThNzyWL35vUH2/qSAowuzoKAPzX6sgOeBet5i751UpAkA8sUOqZz8DVnk4YXmas5UWAR07FeHepQ==&quot;,
    &quot;SigningCertURL&quot;:&quot;https://sns.us-east-2.amazonaws.com/SimpleNotificationService-ac565b8b1a6c5d002d285f9598aa1d9b.pem&quot;
}
</code></pre></div>

<h2 id="toc_41">订单变化通知</h2>

<div><pre><code class="language-none">POST / HTTP/1.1
    x-amz-sns-message-type: Notification
    x-amz-sns-message-id: da41e39f-ea4d-435a-b922-c6aae3915ebe
    x-amz-sns-topic-arn: arn:aws:sns:us-west-2:123456789012:MyTopic
    x-amz-sns-subscription-arn: arn:aws:sns:us-west-2:123456789012:MyTopic:2bcfbf39-05c3-41de-beaa-fcfcc21c8f55
    Content-Length: 761
    Content-Type: application/json; charset=UTF-8
    Host: ec2-50-17-44-49.compute-1.amazonaws.com
    Connection: Keep-Alive
    User-Agent: Amazon Simple Notification Service Agent

    {
      &quot;Type&quot; : &quot;Notification&quot;,
      &quot;MessageId&quot; : &quot;da41e39f-ea4d-435a-b922-c6aae3915ebe&quot;,
      &quot;TopicArn&quot; : &quot;arn:aws:sns:us-west-2:123456789012:MyTopic&quot;,
      &quot;Subject&quot; : &quot;签名的值&quot;,
      &quot;Message&quot; : &quot;{&quot;amount&quot;:&quot;17.14285714&quot;,&quot;appealStatus&quot;:1,&quot;isReopen&quot;:false,&quot;outOrderSn&quot;:&quot;20181229645326085005&quot;,&quot;price&quot;:&quot;7.0000&quot;,&quot;status&quot;:3,&quot;timestamp&quot;:&quot;2018-12-29T06:23:41.576Z&quot;,&quot;total&quot;:&quot;120.0000&quot;,&quot;type&quot;:&quot;OrderStatusChangeMessage&quot;}&quot;,
      &quot;Timestamp&quot; : &quot;2012-04-25T21:49:25.719Z&quot;,
      &quot;SignatureVersion&quot; : &quot;1&quot;,
      &quot;Signature&quot; : &quot;EXAMPLElDMXvB8r9R83tGoNn0ecwd5UjllzsvSvbItzfaMpN2nk5HVSw7XnOn/49IkxDKz8YrlH2qJXj2iZB0Zo2O71c4qQk1fMUDi3LGpij7RCW7AW9vYYsSqIKRnFS94ilu7NFhUzLiieYr4BKHpdTmdD6c0esKEYBpabxDSc=&quot;,
      &quot;SigningCertURL&quot; : &quot;https://sns.us-west-2.amazonaws.com/SimpleNotificationService-f3ecfb7224c7233fe7bb5f59f96de52f.pem&quot;,
      &quot;UnsubscribeURL&quot; : &quot;https://sns.us-west-2.amazonaws.com/?Action=Unsubscribe&amp;SubscriptionArn=arn:aws:sns:us-west-2:123456789012:MyTopic:2bcfbf39-05c3-41de-beaa-fcfcc21c8f55&quot;
    }
</code></pre></div>

<p>取出 Message 例如</p>

<div><pre><code class="language-none">{&quot;amount&quot;:&quot;17.14285714&quot;,&quot;appealStatus&quot;:1,&quot;isReopen&quot;:false,&quot;outOrderSn&quot;:&quot;20181229645326085005&quot;,&quot;price&quot;:&quot;7.0000&quot;,&quot;status&quot;:3,&quot;timestamp&quot;:&quot;2018-12-29T06:23:41.576Z&quot;,&quot;total&quot;:&quot;120.0000&quot;,&quot;type&quot;:&quot;OrderStatusChangeMessage&quot;}
</code></pre></div>

<p>平台使用您在渠道商上设置的密钥对Mesasge的内容进行签名， 签名的值在Subject中。</p>

<h3 id="toc_42">签名算法如下：</h3>

<p>示例使用的是Java Base64编码方法。</p>

<div><pre><code class="language-none">Signature = encodeBase64String( HMAC-SHA256(渠道商设置的密钥, Message中的值)，&quot;UTF-8&quot;)

当 Signature == Subject的值 时说明验签成功
</code></pre></div>

<h2 id="toc_43"><a name="#订单状态改变异步通知url" style="color:#000000">订单状态改变异步通知url:</a></h2>

<p><code>post: http://{notifyUrl}</code></p>

<h3 id="toc_44">注: notifyUrl 是用户配置[渠道商回调地址][1]</h3>

<h2 id="toc_45">请求参数:</h2>

<table>
<thead>
<tr>
<th style="text-align: left">参数</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">subject</td>
<td style="text-align: left">签名</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">message</td>
<td style="text-align: left">消息体(json格式)，验签对整个消息体签名后，对subject中的签名对比</td>
<td style="text-align: left"></td>
</tr>
</tbody>
</table>

<h3 id="toc_46">message包含:</h3>

<table>
<thead>
<tr>
<th style="text-align: left">属性名</th>
<th style="text-align: left">描述</th>
<th style="text-align: left">取值</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">noticeType</td>
<td style="text-align: left">通知类型</td>
<td style="text-align: left">OrderNotice - 订单变化通知</td>
</tr>
<tr>
<td style="text-align: left">noticeAction</td>
<td style="text-align: left">action</td>
<td style="text-align: left">1-等待付款（匹配上承兑商）2-确认付款</td>
</tr>
<tr>
<td style="text-align: left">noticeTimestamp</td>
<td style="text-align: left">通知时间</td>
<td style="text-align: left">格林尼治时间</td>
</tr>
<tr>
<td style="text-align: left">outOrderSn</td>
<td style="text-align: left">商户订单号</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">status</td>
<td style="text-align: left">订单状态</td>
<td style="text-align: left">1已创建,2待付款,3已付款,4已完成,5取消(已关闭)</td>
</tr>
<tr>
<td style="text-align: left">cancelType</td>
<td style="text-align: left">取消状态</td>
<td style="text-align: left">1-用户取消 2-系统取消</td>
</tr>
<tr>
<td style="text-align: left">amount</td>
<td style="text-align: left">币数量</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">price</td>
<td style="text-align: left">单价</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">total</td>
<td style="text-align: left">总金额</td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">fee</td>
<td style="text-align: left">手续费</td>
<td style="text-align: left"></td>
</tr>
</tbody>
</table>

<h3 id="toc_47">OrderNotice（订单变化通知） noticeAction枚举</h3>

<table>
<thead>
<tr>
<th style="text-align: left">枚举值</th>
<th style="text-align: left">业务</th>
<th style="text-align: left">含义</th>
</tr>
</thead>

<tbody>
<tr>
<td style="text-align: left">101</td>
<td style="text-align: left">入金</td>
<td style="text-align: left">已匹配到承兑商</td>
</tr>
<tr>
<td style="text-align: left">102</td>
<td style="text-align: left">入金</td>
<td style="text-align: left">您的用户确认付款</td>
</tr>
<tr>
<td style="text-align: left">103</td>
<td style="text-align: left">入金</td>
<td style="text-align: left">承兑商确认收款放币</td>
</tr>
<tr>
<td style="text-align: left">104</td>
<td style="text-align: left">入金</td>
<td style="text-align: left">您的用户取消入金订单</td>
</tr>
<tr>
<td style="text-align: left">105</td>
<td style="text-align: left">入金</td>
<td style="text-align: left">系统重开入金订单</td>
</tr>
<tr>
<td style="text-align: left"></td>
<td style="text-align: left"></td>
<td style="text-align: left"></td>
</tr>
<tr>
<td style="text-align: left">201</td>
<td style="text-align: left">出金</td>
<td style="text-align: left">审核通过，即匹配到承兑商</td>
</tr>
<tr>
<td style="text-align: left">202</td>
<td style="text-align: left">出金</td>
<td style="text-align: left">审核失败，订单取消</td>
</tr>
<tr>
<td style="text-align: left">203</td>
<td style="text-align: left">出金</td>
<td style="text-align: left">承兑商付款</td>
</tr>
<tr>
<td style="text-align: left">304</td>
<td style="text-align: left">出金</td>
<td style="text-align: left">您的用户确认收款</td>
</tr>
<tr>
<td style="text-align: left">205</td>
<td style="text-align: left">出金</td>
<td style="text-align: left">承兑商取消订单</td>
</tr>
</tbody>
</table>

<h2 id="toc_48">消息样例</h2>

<h3 id="toc_49"><a name="入金消息样例" style="color:#000000">消息样例</a></h3>

<p><strong>1. 你的用户确认付款:</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;102&quot;,
    &quot;status&quot;: 3, //3-确认收款
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<p><strong>2. 承兑商确认收款:</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;103&quot;,
    &quot;status&quot;: 4, //4-承兑商确认收款
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}
</code></pre></div>

<p><strong>3. 你的用户取消订单</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;104&quot;,
    &quot;status&quot;: 5, //5-取消
    &quot;cancelType&quot;: 1, // 1-用户取消
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,
}</code></pre></div>

<p><strong>4.你的用户付款超时取消</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;104&quot;,
    &quot;status&quot;: 5, //5-取消
    &quot;cancelType&quot;: 2, // 2-系统取消
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<p><strong>5.订单重开</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;104&quot;,
    &quot;status&quot;: 2, //2-代付款
    &quot;cancelType&quot;: 2, // 2-系统取消
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<h3 id="toc_50">出金</h3>

<p><strong>1. 出金审核通过（匹配到承兑商）:</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;201&quot;,
    &quot;status&quot;: 2, //2-等待付款
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<p><strong>2. 出金审核拒绝（取消）:</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;103&quot;,
    &quot;status&quot;: 5, //5-取消
    &quot;cancelType&quot;: 2, // 2-系统取消
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}
</code></pre></div>

<p><strong>3. 承兑商确认付款</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;203&quot;,
    &quot;status&quot;: 3, //3-已付款
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,
}</code></pre></div>

<p><strong>4.你的用户确认收款</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;304&quot;,
    &quot;status&quot;: 4, //4-确认收款（已完成）
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<p><strong>5.承兑商取消</strong></p>

<div><pre><code class="language-none">{
    &quot;noticeTimestamp&quot;: &quot;2019-01-02T02:22:53.509Z&quot;,
    &quot;noticeType&quot;:&quot;OrderNotice&quot;,
    &quot;noticeAction&quot;:&quot;204&quot;,
    &quot;status&quot;: 5, //5-已取消
    &quot;cancelType&quot;: 1, // 1-用户取消
    &quot;outOrderSn&quot;:&quot;O234234234&quot;,
    &quot;amount&quot;: &quot;100&quot;,
    &quot;total&quot;: &quot;700&quot;,
    &quot;price&quot;: &quot;7.0000&quot;,

}</code></pre></div>

<h1 id="toc_51"><a name="API签名机制">六.API签名机制</a></h1>

<h2 id="toc_52"><a style="color:#000000" name="获取签名用KEY">获取签名用的Access Key和Secret Key</a></h2>

<p>对于每一次HTTP或者HTTPS协议请求，我们会根据访问中的签名信息验证访问请求者身份。具体由使用Access Key和Secret Key对称加密验证实现。
其中Access Key是访问者身份，Secret Key是加密签名字符串和服务器端验证签名字符串的密钥，必须严格保密谨防泄露。</p>

<p>Access Key和Secret Key的获取通过</p>

<p><img src="https://raw.githubusercontent.com/bddpay/APIDOC/master/intro_key.jpg" alt=""></p>

<h2 id="toc_53">API签名方法</h2>

<h3 id="toc_54">步骤 1. 构造规范化请求字符串</h3>

<p>排序参数。排序规则以首字母顺序排序，排序参数包括 公共请求参数 和接口自定义参数，不包括公共请求参数中的 Signature 参数。
说明 当使用GET方法提交请求时，这些参数就是请求URL中的参数部分，即URL中 ? 之后由 &amp; 连接的部分。
编码参数。使用UTF-8字符集按照 RFC3986 规则编码请求参数和参数取值，编码规则如下：
字符A~Z、a~z、0~9以及字符 -、_、.、~ 不编码。</p>

<p>其它字符编码成 %XY 的格式，其中 XY 是字符对应ASCII码的16进制。示例：半角双引号（&quot;）对应 %22。</p>

<p>扩展的UTF-8字符，编码成 %XY%ZA… 的格式。</p>

<p>空格（ ）编码成 %20，而不是加号（+）。</p>

<p>该编码方式与 application/x-www-form-urlencoded MIME格式编码算法相似，但又有所不同。</p>

<p>如果您使用的是Java标准库中的 java.net.URLEncoder，可以先用标准库中 percentEncode 编码，随后将编码后的字符中加号（+）替换为 %20、星号（*）替换为 %2A、%7E 替换为波浪号（~），即可得到上述规则描述的编码字符串。</p>

<div><pre><code class="language-none">private static final String ENCODING = &quot;UTF-8&quot;;
private static String percentEncode(String value) throws UnsupportedEncodingException {
return value != null ? URLEncoder.encode(value, ENCODING).replace(&quot;+&quot;, &quot;%20&quot;).replace(&quot;*&quot;, &quot;%2A&quot;).replace(&quot;%7E&quot;, &quot;~&quot;) : null;
}</code></pre></div>

<p>使用等号（=）连接编码后的请求参数和参数取值。
使用与号（&amp;）连接编码后的请求参数，注意参数排序与 步骤1 一致。
现在，您得到了规范化请求字符串（CanonicalizedQueryString），其结构遵循 请求结构。</p>

<h3 id="toc_55">步骤 2. 构造签名字符串</h3>

<p>构造待签名字符串 StringToSign。您可以同样使用 percentEncode 处理上一步构造的规范化请求字符串，规则如下：</p>

<div><pre><code class="language-none">StringToSign=
  HTTPMethod + &quot;\n&quot; + //HTTPMethod：发送请求的 HTTP 方法，例如 POST。
  HTTPHost  + &quot;\n&quot; + // 测试和正式地址不一样
  URI_PATH + &quot;\n&quot; + // 例如 获取账户信息  /v1/api/open/account
  CanonicalizedQueryString  //您的规范化请求字符串。例如 AccessKeyId=3dDnvhpbUP2I&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-10-26T08%3A55%3A45&amp;attach=%E8%AE%A2%E5%8D%95%20%2B%E7%BC%96%E5%8F%B79527&amp;outOrderSn=20181026101528gqHSlFJ49BI7dPtNoE
</code></pre></div>

<h3 id="toc_56">步骤 3. 生成签名字符串</h3>

<p>按照 RFC2104 的定义，计算待签名字符串 StringToSign 的HMAC-SHA256值。示例使用的是Java Base64编码方法。
Signature = encodeBase64String( HMAC-SHA256( SecretKey, StringToSign)，&quot;UTF-8&quot;)</p>

<h2 id="toc_57">示例</h2>

<h3 id="toc_58">签名参数</h3>

<table>
<thead>
<tr>
<th>参数名</th>
<th>描述</th>
<th>默认值</th>
<th>是否参与签名</th>
<th>示例值</th>
</tr>
</thead>

<tbody>
<tr>
<td>AccessKeyId</td>
<td>KeyId</td>
<td></td>
<td>是</td>
<td>3dDnvhpbUP2I</td>
</tr>
<tr>
<td>accessKeySecret</td>
<td>key</td>
<td></td>
<td>是</td>
<td>QHqNZXZZ2XRm</td>
</tr>
<tr>
<td>SignatureVersion</td>
<td>签名版本</td>
<td>1</td>
<td>是</td>
<td>1</td>
</tr>
<tr>
<td>SignatureMethod</td>
<td>签名方法</td>
<td>HmacSHA256</td>
<td>是</td>
<td>HmacSHA256</td>
</tr>
<tr>
<td>Timestamp</td>
<td>UTC时间戳</td>
<td></td>
<td>是</td>
<td>2018-10-26T08:55:45</td>
</tr>
<tr>
<td>Signature</td>
<td>签名结果</td>
<td></td>
<td>否</td>
<td></td>
</tr>
</tbody>
</table>

<h2 id="toc_59">签名步骤</h2>

<ul>
<li>组装参数列表字符串</li>
<li>组装待签名字符串</li>
<li>签名</li>
</ul>

<h3 id="toc_60">组装参数列表字符串</h3>

<ul>
<li>把参与签名的签名参数加入原始参数列表</li>
<li>把新的参数列表的 key 按自然序排序</li>
<li>编码值 encodeURIComponent(value)</li>
<li>按 key1=value2&amp;key2=values 拼接；</li>
</ul>

<h4 id="toc_61">示例：</h4>

<p>参数列表：
<code>
{
    &quot;outOrderSn&quot;: &quot;20181026101528gqHSlFJ49BI7dPtNoE&quot;,
    &quot;attach&quot;: &quot;订单 +编号9527&quot;
}
</code>
组装结果：
<code>
AccessKeyId=3dDnvhpbUP2I&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-10-26T08%3A55%3A45&amp;attach=%E8%AE%A2%E5%8D%95%20%2B%E7%BC%96%E5%8F%B79527&amp;outOrderSn=20181026101528gqHSlFJ49BI7dPtNoE
</code>
说明：
encodeURIComponent(value) 与 javascript encodeURIComponent 函数结果一致，可在浏览器控制台用该函数测试；</p>

<h3 id="toc_62">组装待签名字符串</h3>

<p>HTTP方法名大写、请求域名小写、请求URI、组装完的参数列表字符串，用 \n 组装成待签名的字符串；</p>

<h4 id="toc_63">示例：</h4>

<div><pre><code class="language-none">POST\nepay.rmmlm.com\n/v1/api/open/order/buy\nAccessKeyId=3dDnvhpbUP2I&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-10-26T08%3A55%3A45&amp;attach=%E8%AE%A2%E5%8D%95%20%2B%E7%BC%96%E5%8F%B79527&amp;outOrderSn=20181026101528gqHSlFJ49BI7dPtNoE</code></pre></div>

<h3 id="toc_64">签名</h3>

<p>encodeBase64String(HmacSHA256(strToSign, accessKeySecret), &quot;UTF-8&quot;)
示例：
<code>
S4ErItdpKdHF4Rxwss3FZGBWJqkwT3iLzPIxfMrPzMU=
</code></p>

<h2 id="toc_65">示例买币</h2>

<h3 id="toc_66">原始参数：</h3>

<div><pre><code class="language-none">{
  &quot;outOrderSn&quot;: &quot;201811011009150NOLpBHPBkwQqhfX16&quot;,
  &quot;variety&quot;: &quot;usdt&quot;,
  &quot;currency&quot;: &quot;CNY&quot;,
  &quot;totalAmount&quot;: &quot;100&quot;,
  &quot;paymentType&quot;: &quot;3&quot;
}</code></pre></div>

<h3 id="toc_67">每个请求都需要参与签名的参数，Timestamp：UTC时间 格式 2018-11-01T01:39:52</h3>

<div><pre><code class="language-none">{
  &quot;AccessKeyId&quot;: &quot;jzbuptbq-vtnaauby-aj8q04vw-z1615&quot;,
  &quot;SignatureMethod&quot;: &quot;HmacSHA256&quot;,
  &quot;SignatureVersion&quot;: 1,
  &quot;Timestamp&quot;: &quot;2018-11-01T01:39:52&quot;
}
</code></pre></div>

<h3 id="toc_68">用于签名的参数</h3>

<div><pre><code class="language-none">{
  &quot;outOrderSn&quot;: &quot;201811011009150NOLpBHPBkwQqhfX16&quot;,
  &quot;variety&quot;: &quot;usdt&quot;,
  &quot;currency&quot;: &quot;CNY&quot;,
  &quot;totalAmount&quot;: &quot;100&quot;,
  &quot;paymentType&quot;: &quot;3&quot;,
  &quot;AccessKeyId&quot;: &quot;jzbuptbq-vtnaauby-aj8q04vw-z1615&quot;,
  &quot;SignatureMethod&quot;: &quot;HmacSHA256&quot;,
  &quot;SignatureVersion&quot;: 1,
  &quot;Timestamp&quot;: &quot;2018-11-01T01:39:52&quot;
}
</code></pre></div>

<h3 id="toc_69">组装参数列表字符串</h3>

<div><pre><code class="language-none">&quot;AccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-11-01T01%3A39%3A52&amp;currency=CNY&amp;outOrderSn=201811011009150NOLpBHPBkwQqhfX16&amp;paymentType=3&amp;totalAmount=100&amp;variety=usdt&quot;
</code></pre></div>

<h3 id="toc_70">签名的字符串</h3>

<div><pre><code class="language-none">&quot;POST\nepay.rmmlm.com\n/v1/api/open/order/buy\nAccessKeyId=jzbuptbq-vtnaauby-aj8q04vw-z1615&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-11-01T01%3A39%3A52&amp;currency=CNY&amp;outOrderSn=201811011009150NOLpBHPBkwQqhfX16&amp;paymentType=3&amp;totalAmount=100&amp;variety=usdt&quot;
</code></pre></div>

<p>得到 qaUWgO36NYGTmlnuSAeNSBnm++y/QBuFaeX4sI4hOFw=</p>

<h3 id="toc_71">发送的请求</h3>

<p>Content-Type: application/json</p>

<p>{
  &quot;outOrderSn&quot;: &quot;201811011009150NOLpBHPBkwQqhfX16&quot;,
  &quot;variety&quot;: &quot;usdt&quot;,
  &quot;currency&quot;: &quot;CNY&quot;,
  &quot;totalAmount&quot;: &quot;100&quot;,
  &quot;paymentType&quot;: &quot;3&quot;,
  &quot;AccessKeyId&quot;: &quot;jzbuptbq-vtnaauby-aj8q04vw-z1615&quot;,
  &quot;SignatureMethod&quot;: &quot;HmacSHA256&quot;,
  &quot;SignatureVersion&quot;: 1,
  &quot;Timestamp&quot;: &quot;2018-11-01T01:39:52&quot;,
  &quot;Signature&quot;: &quot;qaUWgO36NYGTmlnuSAeNSBnm++y/QBuFaeX4sI4hOFw=&quot;
}</p>

<h1 id="toc_72"><a name="网关URL签名机制">七.网关URL签名机制</a></h1>

<h2 id="toc_73">获取签名用的Access Key和Secret Key</h2>

<p><a href="#%E8%8E%B7%E5%8F%96%E7%AD%BE%E5%90%8D%E7%94%A8KEY">参照API获取签名用KEY</a></p>

<h2 id="toc_74">API签名方法</h2>

<h3 id="toc_75">步骤 1. 构造规范化请求字符串</h3>

<p>排序参数。排序规则以首字母顺序排序，排序参数包括 公共请求参数 和接口自定义参数，不包括公共请求参数中的 Signature 参数。
说明 当使用GET方法提交请求时，这些参数就是请求URL中的参数部分，即URL中 ? 之后由 &amp; 连接的部分。
编码参数。使用UTF-8字符集按照 RFC3986 规则编码请求参数和参数取值，编码规则如下：
字符A~Z、a~z、0~9以及字符 -、_、.、~ 不编码。</p>

<p>其它字符编码成 %XY 的格式，其中 XY 是字符对应ASCII码的16进制。示例：半角双引号（&quot;）对应 %22。</p>

<p>扩展的UTF-8字符，编码成 %XY%ZA… 的格式。</p>

<p>空格（ ）编码成 %20，而不是加号（+）。</p>

<p>该编码方式与 application/x-www-form-urlencoded MIME格式编码算法相似，但又有所不同。</p>

<p>如果您使用的是Java标准库中的 java.net.URLEncoder，可以先用标准库中 percentEncode 编码，随后将编码后的字符中加号（+）替换为 %20、星号（*）替换为 %2A、%7E 替换为波浪号（~），即可得到上述规则描述的编码字符串。</p>

<div><pre><code class="language-none">private static final String ENCODING = &quot;UTF-8&quot;;
private static String percentEncode(String value) throws UnsupportedEncodingException {
return value != null ? URLEncoder.encode(value, ENCODING).replace(&quot;+&quot;, &quot;%20&quot;).replace(&quot;*&quot;, &quot;%2A&quot;).replace(&quot;%7E&quot;, &quot;~&quot;) : null;
}</code></pre></div>

<p>使用等号（=）连接编码后的请求参数和参数取值。
使用与号（&amp;）连接编码后的请求参数，注意参数排序与 步骤1 一致。
现在，您得到了规范化请求字符串（CanonicalizedQueryString），其结构遵循 请求结构。</p>

<h3 id="toc_76">步骤 2. 构造要签名的URL</h3>

<p>构造待签名字符串 StringToSign。您可以同样使用 percentEncode 处理上一步构造的规范化请求字符串，规则如下：</p>

<div><pre><code class="language-none">String toSignUrl =&quot;http://gateway.rmmlm.com&quot;+  //网关域名
  &quot;/gateway/v1/pay&quot;+ //网关接口，以出金下单为例
  +&quot;?&quot;
  + CanonicalizedQueryString  //您的规范化请求字符串。例如 AccessKeyId=3dDnvhpbUP2I&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2018-10-26T08%3A55%3A45&amp;attach=%E8%AE%A2%E5%8D%95%20%2B%E7%BC%96%E5%8F%B79527&amp;outOrderSn=20181026101528gqHSlFJ49BI7dPtNoE</code></pre></div>

<p><strong>out put</strong></p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/pay?AccessKeyId=rslqzn7n-hd3sjzbj-avd13wrj-n1353&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2019-01-04T10%3A19%3A52.816Z&amp;amount=0.1&amp;callback=http%3A%2F%2Fgateway.rmmlm.com%2Fv1%2Fapi0%2Ftest%2Fgateway%2FcreateOrder&amp;currency=CNY&amp;idNumber=2303234234234234234&amp;mobile=13688282823423&amp;name=%E4%BE%AF%E5%85%B4%E7%9B%9B&amp;outOrderSn=1546597192897&amp;outUid=1000&amp;type=3&amp;unit=1&amp;variety=USDT</code></pre></div>

<h3 id="toc_77">步骤 3. 生成签名字符串</h3>

<p>按照 RFC2104 的定义，计算待签名字符串 StringToSign 的HMAC-SHA256值。示例使用的是Java Base64编码方法。</p>

<div><pre><code class="language-none">String signature = encodeBase64String( HMAC-SHA256( SecretKey, StringToSign)，&quot;UTF-8&quot;)</code></pre></div>

<p><strong>out put:</strong></p>

<div><pre><code class="language-none"> f97lPursseUVa+RUBtOc6d9/EY7oT1jU/PEKgquOq5g=</code></pre></div>

<h3 id="toc_78">步骤 4. 将签名加到URL末尾</h3>

<p>将 步骤 2 生成的url <code>toSignUrl</code> 和 步骤 3 的签名字符串<code>encode(signature)</code> 链接起来</p>

<div><pre><code class="language-none">String signedUrl = toSignUrl +&quot;&amp;Signature=&quot;+ encode(signature);</code></pre></div>

<p><strong>out put</strong>：</p>

<div><pre><code class="language-none">http://gateway.rmmlm.com/gateway/v1/pay?AccessKeyId=rslqzn7n-hd3sjzbj-avd13wrj-n1353&amp;SignatureMethod=HmacSHA256&amp;SignatureVersion=1&amp;Timestamp=2019-01-04T10%3A19%3A52.816Z&amp;amount=0.1&amp;callback=http%3A%2F%2Fgateway.rmmlm.com%2Fv1%2Fapi0%2Ftest%2Fgateway%2FcreateOrder&amp;currency=CNY&amp;idNumber=2303234234234234234&amp;mobile=13688282823423&amp;name=%E4%BE%AF%E5%85%B4%E7%9B%9B&amp;outOrderSn=1546597192897&amp;outUid=1000&amp;type=3&amp;unit=1&amp;variety=USDT&amp;Signature=f97lPursseUVa%2BRUBtOc6d9%2FEY7oT1jU%2FPEKgquOq5g%3D</code></pre></div>




</body>

</html>
