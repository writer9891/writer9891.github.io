PayPal 提供了完整的自动续费功能，以下是实现自动订阅的关键步骤和注意事项。

## 自动续费的五个步骤

官方文档中提到的自动续费步骤如下：

1. **创建并激活订阅计划**：事先创建订阅计划（Billing Plan），并将其激活。
2. **用户创建订阅**：用户跳转到 PayPal 网站，同意订阅协议。
3. **执行订阅**：用户同意后，跳转回网站，完成订阅执行。
4. **获取账单信息**：包括接收每次扣款结果的通知或主动查询支付结果。
5. **处理取消订阅**：接收用户取消订阅的通知并进行相应处理。

## 使用 PayPal SDK

通过以下命令安装 PayPal SDK：

bash
composer require paypal/rest-api-sdk-php


PayPal 官方提供了完整的 [示例代码](https://paypal.github.io/PayPal-PHP-SDK/sample/#billing)，并支持通过 [PayPal Sandbox](https://www.sandbox.paypal.com/) 进行调试。

## 创建订阅计划并激活

在创建订阅计划时，需要注意以下几点：

- **订阅计划与商品价格的关系**：每个商品的不同价格需要创建不同的订阅计划，但可以在创建协议时针对不同用户调整价格。
- **试用期与正式订阅**：`TRIAL` 类型支付必须与 `REGULAR` 类型支付同时存在。`TRIAL` 无法自动判断是否为新用户，因此新用户的优惠需要通过业务逻辑实现。
- **协议生效时间限制**：协议的生效时间必须在当前时间 24 小时之后，因此无法立即扣款。若需要立即扣款，可通过 `MerchantPreferences` 的 `setSetupFee` 方法设置首次扣款费用。
- **SDK 错误处理**：PayPal SDK 可能会报错 `"NotifyUrl" value is NULL`，这是 PayPal 服务端的已知问题，解决方法可参考 [相关 Issue](https://github.com/paypal/PayPal-PHP-SDK/pull/1152/files)。

## 创建订阅

用户可以基于同一订阅计划创建多个订阅协议（Billing Agreement）。以下是创建订阅时的注意事项：

- **设置首次扣款**：由于 `start_date` 最早为当前时间 24 小时之后，该值实际上设置的是第二次扣款时间。若需立即扣款，可通过 `setSetupFee` 设置首次扣款费用。
- **提取 Token**：创建订阅后，需要从跳转链接中提取 `token`，以便将用户同意的协议与创建的订阅对应。

示例代码如下：

php
$link = $agreement->getApprovalLink();
parse_str(parse_url($link, PHP_URL_QUERY), $params);
$token = $params['token'];


- **取消旧协议**：同一用户可以多次订阅同一计划，因此在执行新协议时，需手动取消用户之前的协议。
- **扣款时间延迟**：实际扣款时间通常会比 `AgreementDetail.next` 显示的时间晚几个小时。为保证连续性，可设置提前一天扣款。

## 循环扣款与通知

- **Webhook 通知**：在 `My Apps -> REST API apps -> WEBHOOKS` 中设置 `webhook`，当每次循环扣款成功时，PayPal 会发送 `PAYMENT.SALE.COMPLETED` 事件通知。通过其中的 `billing_agreement_id` 字段，可匹配对应的订阅协议。
- **交易查询**：每次 `AgreementDetail` 返回的 `next` 参数表示下次收款时间。超过该时间后，可通过 `Agreement::searchTransactions` 方法查询协议的所有交易。需要注意，PayPal 的实际扣款时间可能会延迟，因此需多次重试。

---

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)