# Payment SDK

PaymentSDK provides an easy way for apps to connect to PaymentService

## 🤚 Introduction

Depending on your needed, PaymentSDK provides two options for you to integrate. If you want to use pre built-in UI that contains payment method list, transaction detail and transaction result, please install `PaymentSDK`. In case you only want to use business functions, please install `PaymentGateway`.

## 🍖  Installation

### Android

Add instruction here

### iOS

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

### For **Android**

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

### For **iOS**

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

## 🔑 Usage


## 🌈 Customization