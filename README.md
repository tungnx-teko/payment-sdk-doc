# Payment SDK

PaymentSDK provides an easy way for apps to connect to PaymentService

## 🤚 Introduction

Depending on your needed, PaymentSDK provides two options for you to integrate. If you want to use pre built-in UI that contains payment method list, transaction detail and transaction result, please install `PaymentSDK`. In case you only want to use business functions, please install `PaymentGateway`.

## 🍖  Installation

### Android

Add instruction here

### iOS

Using **[CocoaPods](https://cocoapods.org/)**

To install `PaymentGateway`:

```ruby
pod 'PaymentGateway', :git => 'https://github.com/tungnx-teko/payment-sdk-ios'
```

To install entire `PaymentSDK`

```ruby
pod 'PaymentSDK', :git => 'https://github.com/tungnx-teko/payment-sdk-ios'
```

## 🔩 Configuration

Before using, we need to add config for PaymentGateway

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