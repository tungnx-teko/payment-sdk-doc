# Payment SDK

PaymentSDK provides an easy way to connect to PaymentService

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

Depending on your needed, PaymentSDK provides two options to integrate. If you want to use pre built-in UI that contains payment method list, transaction detail and transaction result, please install `PaymentSDK`. In case you only want to use business functions, please install `PaymentGateway`.

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

## 🔩 Configuration<a name="configuration"></a>

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

* For `SPOSPaymentMethod`

```kotlin
val sposConfig = SPosPaymentConfig(partnerCode)
val spos = SPosPaymentMethod(sposConfig, SposMethod)
```

* For `CTTPaymentMethod`

```kotlin
val qrConfig = CTTPaymentConfig(partnerCode)
val qr = CTTPaymentMethod(qrConfig, MMSMethod)
```

Finally, add payment methods which you wants to use

```kotlin
PaymentGateway.getInstance().addPaymentMethods(
    listOf(qr, spos)
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

If you use `CTT` method, please set callbackUrl to config. `returnUrl` and `cancelUrl` values are used to redirect when payment is completed.

```swift
config.setCallbackUrl(forMethod: .qr,
                      returnUrl: 'RETURN_URL',
                      cancelUrl: 'CANCEL_URL')
```


Finally, don't forget to add this config to `PaymentGateway`

```swift
PaymentGateway.shared.setConfig(config)
```

## 🔑 Usage<a name="usage"></a>

## PaymentGateway Usage<a name="paymentgateway"></a>

`PaymentGateway` provides business functions to create QR code, or create transaction. To do this, we need to create a `PaymentRequest` and pass to `PaymentGateway.pay(paymentMethod, paymentRequest)`

And then, we can use the result of `pay` method to do everything you want.

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

```kotlin
val result = paymentGateway.pay(paymentMethod.method, qrRequest)
if (result.isSuccess) {
val transactionResponse = result.get()
    if (transactionResponse.isSuccess()) {
        // Requested a transaction
    }
}
```

<br/>

**iOS**

```swift
let request = CTTPaymentRequest(orderId: orderId,
                                orderCode: orderCode,
                                amount: amount)                                     
```

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

In the screen where you want to observe the transaction result, you need to create a `PaymentObserver`

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
observer.observe(transactionCode: transactionCode) { result in
    switch result {
    case .success:
        // Success, order is paid
    case .failure(let error):
        // Failed
    }
}
```

## PaymentSDK Usage<a name="paymentsdk"></a>

### Screenshots

<p float="left">
  <img src="https://i.imgur.com/cGTRiaa.png" width="100" />
  <img src="https://i.imgur.com/AFW3VMW.png" width="100" /> 
  <img src="https://i.imgur.com/qbWj3z8.png" width="100" />
  <img src="https://i.imgur.com/OYn0BS9.png" width="100" />
  <img src="https://i.imgur.com/6PDyS71.png" width="100" />
</p>

We need to create a `PaymentRequest` object and then pass to `PaymentActivity` or `PaymentViewController`.

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
import PaymentSDK
import PaymentGateway

let request = PaymentRequest(orderId: "[orderId]",
                             orderCode: "[orderCode]",
                             amount: 20000)
let payment = PaymentRouter.createModule(request: request, 
                                         delegate: self)
present(payment, animated: true, completion: nil)
```

Delegate is an object whose class conforms to `PaymentDelegate`. 

> You can either present or push paymentViewController from your navigation. PaymentSDK has itself built-in navigation controller. 

```swift
func onResult(_ result: PaymentResult) {
    
}

func onCancel() {
    
}
```

## 🌈 Customization<a name="customization"></a>

All resources is open, so client can override it to get the expected UI.

## 🐣 Contributors

<a href="https://github.com/tungnx-teko" target="_blank"><img src="https://avatars3.githubusercontent.com/u/54269108?s=460&u=2a1c8a8745bcb721d943697d3fb4a0af4a4a203f&v=4" width="64px" height="64px" style="border-radius:32px;"></a>&nbsp;&nbsp;&nbsp;<a href="https://github.com/tantv-teko" target="_blank"><img src="https://avatars3.githubusercontent.com/u/52944583?s=460&u=99b567cb2d2feb9fee11e9b4a04c8cf21a02249b&v=4" width="64px" height="64px" style="border-radius:32px;"></a>