# Minerva

PaymentSDK provides an easy way to connect to PaymentService

**Table of contents**

1. [Introduction](#introduction)

2. [Installation](#installation)

3. [Configuration](#configuration)

4. [Create Transaction](#create-transaction)

5. [Observe transaction result](#observe-transaction)

6. [Open Payment UI](#open-payment-ui)

7. [Customization](#customization)

## 🤚 Introduction <a name="introduction"></a>

Depending on your purpose, PaymentSDK provides two options to integrate. If you want to use pre built-in UI that contains payment method list, transaction detail and transaction result, please install `Minerva`. In case you only want to use business functions, please install `MinervaCore`.

## 🍖  Installation <a name="installation"></a>

Using **[CocoaPods](https://cocoapods.org/)**

```ruby
pod 'Minerva', :source => 'https://github.com/teko-vn/Specs-ios.git'
```

```ruby
pod 'MinervaCore', :source => 'https://github.com/teko-vn/Specs-ios.git'
```

To make your app able to archive and upload to Appstore, you have to add file `strip-frameworks.sh` to `build-phases` directory in project directory.

And then add this `Run script` to Project Build Phases:
```ruby
chmod u+x "$SRCROOT/build-phases/strip-frameworks.sh"

"$SRCROOT/build-phases/strip-frameworks.sh" "Minerva"
```

## 🔩 Configuration<a name="configuration"></a>

Before using PaymentGateway, we have to set up a `MinervaGatewayConfig`

```swift
let config = MinervaGatewayConfig(
    clientCode, // payment client code, provided by PS
    terminalCode, // payment terminal code, provided by PS
    serviceCode, // payment service code, provided by PS
    secretKey, // payment secret key, provided by PS
    baseUrl // payment service base url
)
```

**Payment api url**

```ruby
Dev: "https://payment.develop.tekoapis.net/api"
Staging: "https://payment.stage.tekoapis.net/api"
Production: "https://payment.tekoapis.com/api"
```

Then we need to init `MinervaGateway` with this config

```swift
PaymentGateway.shared.setUp(config, environment: .development)
```

After that, we can retrieve an instance of `MinervaGateway` by

```swift
let gateway = MinervaGateway.shared
```

Then you have to add a respective config for each payment method you use in your app.

```swift
let qrConfig = QRPaymentConfig(partnerCode: partnerCode)
try MinervaGateway.addConfig(cttConfig, forMethod: .qr)
```

Finally, add payment methods to the `gateway`

```swift
MinervaGateway.addPaymentMethods([.qr, .spos])
```


## 🔑 Create Transaction<a name="create-transaction"></a>

`MinervaGateway` provides business functions to create QR code, or create transaction. To do this, we need to create a `PaymentRequest` and pass to `MinervaGateway.pay(paymentMethod, paymentRequest)`

And then, we can use the result of `pay` method to do everything you want.

```swift
let request = QRTransactionRequest(orderId: orderId,
                                   orderCode: orderCode,
                                   amount: amount, 
                                   expireTime: expireTime)                                     
```

```swift
try gateway.pay(method: .qr, request: request, completion: { result in
    switch result {
    case .success(let transaction):
    // Do something
    case .failure(let error):
    // Handle error
    }
})  
```

## 🔑 Observe Transaction Result<a name="observe-transaction"></a>

In the screen where you want to observe the transaction result, you need to create a `PaymentObserver`

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

## 🔑 Open Payment UI<a name="open-payment-ui"></a>

### Screenshots

<p float="left">
<img src="https://i.imgur.com/cGTRiaa.png" width="100" />
<img src="https://i.imgur.com/AFW3VMW.png" width="100" /> 
<img src="https://i.imgur.com/qbWj3z8.png" width="100" />
<img src="https://i.imgur.com/OYn0BS9.png" width="100" />
<img src="https://i.imgur.com/6PDyS71.png" width="100" />
</p>

We need to create a `PaymentRequest` object and then pass to `PaymentViewController`.

> **Note:** Even when you use Minerva, it's still needed to set config for MinervaGateway.

```swift
let request = PaymentRequest(orderId: orderId,
                             orderCode: orderCode,
                             orderDescription: orderDescription, // not required
                             amount: 100000,
                             expireTime: 600) // not required, default is 600s
```

```swift
let payment = PaymentRouter.createModule(request: request, 
                                         delegate: self)
present(payment, animated: true, completion: nil)
```

Delegate is an object whose class conforms to `PaymentDelegate`. 

> You can either present or push paymentViewController from your navigation. Minerva has itself built-in navigation controller. 

```swift
func onResult(_ result: PaymentResult) {

}

func onCancel() {

}
```

## 🌈 Customization<a name="customization"></a>

View theme properties and string values can be access through `Minerva.Theme`.
