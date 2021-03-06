# DeepSelling iOS Tracking Client SDK #

DeepSelling(底下簡稱ds)用戶端函式庫。其js/android SDK, 請參考底下:

1. JS SDK: [SDK](./README.md)
2. Android: [SDK](./android_README.md)


### Installation

#### [CocoaPods](https://cocoapods.org)

修改Podfile並添加私人函式庫

```podfile

source 'https://<USERNAME>:<PASSWORD>@github.com/footprintai/deepselling-ios.git'
source 'https://github.com/CocoPods/Specs.git'

target 'demoapp' do
    use_framework!
    pod 'DeepSellingTracker', '1.0.0'
    ...
```


#### Usage

```Swift
import DeepSellingTracker

let dsTracker = DeepSellingTracker(dsid: 'your-deepselling-id', domainName: 'your-domain-name', token: 'your-token')
```

#### Function Signature

```Swift
let params = JsonParams()
dsTracker.track('action', params)
```

資料參數設定可參考此`params.swift`

```Swift

struct JsonParams: Codeable {
    ...
}

```

其action與json-params請參考[文檔](./README.md#資料參數)。
