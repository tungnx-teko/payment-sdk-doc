# Payment SDK

PaymentSDK provides an easy way for apps to connect to PaymentService

**Table of contents**

1. [Introduction](#introduction)

2. [Installation](#installation)

    * [Android](#android_installation)
    
    * [iOS](#ios_installation)

3. [Configuration](#configuration)

    * [Android](#android_configuration)
  
    * [iOS](#ios_configuration)

4. [Usage](#usage)

    * [PaymentGateway](#paymentgateway)

    * [PaymentSDK](#paymentsdk)

5. [Customization](#customization)

## 🤚 Introduction <a name="introduction"></a>

Depending on your needed, PaymentSDK provides two options for you to integrate. If you want to use pre built-in UI that contains payment method list, transaction detail and transaction result, please install `PaymentSDK`. In case you only want to use business functions, please install `PaymentGateway`.

## 🍖  Installation <a name="installation"></a>

### Android <a name="android_installation"></a>

Add instruction here

### iOS <a name="ios_installation"></a>

Using **[CocoaPods](https://cocoapods.org/)**

To install only `PaymentGateway`

```ruby
pod 'PaymentGateway', :git => 'https://github.com/tungnx-teko/payment-sdk-ios'
```

To install entire `PaymentSDK`

```ruby
pod 'PaymentSDK', :git => 'https://github.com/tungnx-teko/payment-sdk-ios'
```

## 🔩 Configuration

### For **Android**<a name="android_configuration"></a>

```kotlin
val config = PaymentGatewayConfig(
    clientCode, // payment client code, provided by PS
    terminalCode, // payment terminal code, provided by PS
    serviceCode, // payment service code, provided by PS
    secretKey, // payment secret key, provided by PS
    baseUrl, // payment service base url
    logging // enable logging or not
)

val paymentGateway = PaymentGateway.initialize(config)
```

After that we can retrieve the instance anywhere by using

```kotlin
val paymentGateway = PaymentGateway.getInstance()
```

For each payment method, we need to create configuration of which structure depends on that method, and then create `PaymentMethod`object

* For `CashPaymentMethod`

```kotlin
val cashConfig = CashPaymentConfig(
    asiaStaffId,
    crmStaffId,
    partnerCode
)
val cash = CashPaymentMethod(cashConfig, CashMethod)
```

* For `SPOSPaymentMethod`

```kotlin
val sposConfig = SPosPaymentConfig(
    payerId,
    payerAccId,
    payerName,
    cashierId,
    cashierAccId,
    cashierIamId,
    cashierName,
    cashierPhone,
    cashierEmail,
    mcc,
    partnerCode
)
val spos = SPosPaymentMethod(sposConfig, SposMethod)
```

* For `CTTPaymentMethod`

```kotlin
val qrConfig = CTTPaymentConfig(
    payerId,
    payerAccId,
    payerName,
    cashierId,
    cashierAccId,
    cashierIamId,
    cashierName,
    cashierPhone,
    cashierEmail,
    partnerCode
)
val qr = CTTPaymentMethod(qrConfig, MMSMethod)
```

Finally, add payment methods which you wants to use

```kotlin
PaymentGateway.getInstance().addPaymentMethods(
    listOf(cash, spos, qr)
)
```

### For **iOS**<a name="ios_configuration"></a>

```swift
let config = PaymentGatewayConfig(clientCode: 'APP_CLIENT_CODE',
                                  terminalCode: 'APP_TERMINAL_CODE',
                                  serviceCode: 'APP_PAYMENT_SERVICE_CODE',
                                  secretKey: 'APP_PAYMENT_SECRET_KEY',
                                  baseUrl: 'BASE_PAYMENT_URL')
        
```

For each payment method, we need to set callbackUrl. For example, if use want to use `QR` method:

```swift
config.setCallbackUrl(forMethod: .qr,
                      returnUrl: 'QR_RETURN_URL',
                      cancelUrl: 'QR_CANCEL_URL')
```

If you don't use `returnUrl` and `cancelUrl`, don't hesitate to pass emtpy strings to them.

Finally, don't forget to add this config to `PaymentGateway`

```swift
PaymentGateway.shared.setConfig(config)
```

## 🔑 Usage<a name="usage"></a>

## PaymentGateway<a name="paymentgateway"></a>

`PaymentGateway` provides business function to create QR code, or create transaction. To do this, we need to create a `PaymentRequest` and then call function `PaymentGateway.pay(paymentMethod, paymentRequest)`

For details

**Android**

```kotlin
val paymentMethod = paymentGateway.getPaymentMethod(method.name, method.code)

// cash request
val cashConfig = paymentMethod.config as CashPaymentConfig
val cashRequest = CashTransactionRequest.Builder(
    asiaStaffId = cashConfig.asiaStaffId,
    crmStaffId = cashConfig.crmStaffId,
    orderId = orderId, // request data
    methods = listOf(
        CashTransactionRequest.Method(
            amount = amount, // request data
            partnerCode = cashConfig.partnerCode
        )
    )
).build()
```

**iOS**

```swift
let request = CTTPaymentRequest(orderId: payload.orderId.orEmpty,
                                            orderCode: payload.orderCode.orEmpty,
                                            amount: payload.amount ?? 0)                                     
```

And then, we can use the result of `pay` method to do everything you want

**Android**
```kotlin
val result = paymentGateway.pay(paymentMethod.method, qrRequest)
if (result.isSuccess) {
val transactionResponse = result.get()
    if (transactionResponse.isSuccess()) {
        // Requested a transaction
    }
}
```

**iOS**

```swift
try paymentGateway.pay(method: .qr, request: request, completion: { result in
    switch result {
    case .success(let transaction):
        // Do something
    case .failure(let error):
        // Handle error
    }
})  
```

### Result observation

In the screen where you want to observe the transaction result, you need to create a `TransactionObserver`

**Android**
```kotlin
observer.transactionResultEvent(transactionCode)
        .onEach { result ->
            if (result.isSuccess()) {
                // Success, order is paid
            } else {
                // Fail
            }
        }
        .launchIn(lifecycleScope)
```

**iOS**
```swift
PaymentObserver().observe(orderId: order.id) { result in
    switch result {
    case .success:
        // Success, order is paid
    case .failure(let error):
        // Failed
    }
}
```

## PaymentSDK<a name="paymentsdk"></a>

> **Note:** Even when you use PaymentSDK, it's still needed to set config for PaymentGateway.

**Android**

```kotlin
val request = PaymentRequest(
    orderId,
    orderCode,
    orderDescription,
    amount,
    expireTime
)
PaymentActivity.startForResult(this, request)
```

To handle result:
```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    when (requestCode) {
        PaymentActivity.RC_PAYMENT -> {
            when (resultCode) {
                PaymentActivity.RESULT_CANCELED -> {
                    // Payment is canceled
                }
                PaymentActivity.RESULT_FAILED -> {
                    // Payment is failed
                }
                PaymentActivity.RESULT_SUCCEEDED -> {
                    // Payment is succeeded
                
                }
            }
        }
        else -> super.onActivityResult(requestCode, resultCode, data)
    }
}
```

**iOS**
```swift
let request = QRPaymentRequest(orderId: order.id, orderCode: order.code.orEmpty, amount: order.amount)
let payment = PaymentRouter.createModule(request: request, delegate: self)
let nav = UINavigationController(rootViewController: payment)
viewController?.present(nav, animated: true, completion: nil)
```

Delegate is an object whose class conformed to `PaymentDelegate`

```swift
func didSuccess(transaction: PaymentTransactionResult) {
    
}

func didFailure() {
    
}

func didCancel() {
    
}
```

## 🌈 Customization<a name="customization"></a>

All resources is open, so client can override it to get the expected UI.