# DeepSelling Tracking Client #

DeepSelling(底下簡稱ds)用戶端JS函式庫。其移動端(mobile)SDK, 請參考底下:

1. iOS: [SDK](./ios_README.md)
2. Android: [SDK](./android_README.md)


### Installation
在使用此函式庫之前，先確保您有按照安裝流程腳本放置對的位置如下所示：

```javascript
<head> 
...
<script>
  !function(f,b,e,v,n,t,s)
  {if(f.dsq)return;n=f.dsq=function(){n.callMethod?
  n.callMethod.apply(n,arguments):n.queue.push(arguments)};
  if(!f._dsq)f._dsq=n;n.push=n;n.loaded=!0;
  n.queue=[];t=b.createElement(e);t.async=!0;
  t.src=v;s=b.getElementsByTagName(e)[0];
  s.parentNode.insertBefore(t,s)}(window, document,"script",
  "https://<partner>.footprint-ai.com/deepselling.min.js");
  dsq("init", {"dsid": "your-deepselling-id", domainName:"your-domain-name", token:"your-token"});
  dsq("track", "pageview");
</script>
...
</head>
```

在以上安裝腳本實現後，底下幾件事情會被觸發：1. 初始化(init)與 2. PageView追蹤(track). 每當頁面載入完成後便會自行觸發`pageview `事件。




初始化時的詳細參數如下表：


| 資料| 參數名 | 值 | 說明 |  必填 |
|---|---|---|---|---|
| Deepselling ID | dsid  |  ds-101245789452  | Deepselling 平台上的AppID  |   Y |
| Deepselling UserID | dsuid | dsu-789451621 | Deepselling 平台上的UserId, 預設為uuid | N |
| Domain Name | domainName  |  friday.tw  | 電商平台域名  | Y |
| Version Number | v  |  1.0.0  | 追蹤腳本版本號   | Y|
| Screen Resolution | sr | 1680x1020 | 螢幕解析度 | Y |
| Time Zone | tz | +8 | 時區(utc+8) | Y |
| Currency | currency | TWD | 貨幣別(TWD\|USD\|..) | Y |
| User ID | externalid | fri-1478945646 | 電商平台用戶識別碼(用於辨識用戶是否登入) | N |
| User Gender | usergender | M | 使用者性別(M/F/Unknown) | N |
| User Birthday | userbd | 19910526 for May 26, 1991 | 使用者生日 | N |
| User Name | hashedName | 96001f228724d6a... | 用戶名字，得放sha256雜湊後的值 | N |
| User Email | hashedEmail | 96001f228724d6a... | 用戶郵箱，得放sha256雜湊後的值 | N |
| User City | city | Taipei | 用戶註冊城市名 | N |


#### Event Tracker"


例如說我們想要追蹤購賣轉換率，如使用者A購買產品B，我們有底下幾種方式實現:

[1] Add clickEventListener

```javascript
<script type="text/javascript">
  var button = document.getElementById("checkoutButton");
  button.addEventListener(
    "click", 
    function() { 
      dsq("track", "checkedout", {
        products: [{"pid": "7257408", "cid": "415353", "sid": "874", value: 3000}]
      );          
    },
    false
  );
</script>
```

[2] jQuery
 
```javascript
<button id="checkoutButton">購買</button>
<script type="text/javascript">
  $("#$checkoutButton").click(function() {
    dsq("track", "checkedout", {products: [{pid: "7257408", cid: "415353", sid: "874", qty: 1, price: 3000}]);
  });
</script>
```

[3] Predefined content tracker

我們支援html的資料屬性(data attributes data-*)來簡化觸發事件的流程。如底下範例所示：

```html
<form>
  <input type="button" value="購賣" data-ds-track="checkedout" data-ds-date="<json-payload>">
</form>
```

#### Visibility Tracker

我們提供底下幾種方式用來追蹤visibility, 主要是用來追蹤用戶瀏覽網頁進度表

```html
<body>
  <h1>進度式網頁瀏覽追蹤</h1>
  <div >某些內容.....</div>
  <h1 data-ds-pixel>當這個標籤被顯示的時候，會自行觸發追蹤事件</h1>
  <div >某些內容.....</div>
</body>
```

#### Function Signature
追蹤函式原型如下：

```javascript
dsq(eventName, action, params);
```

目前支援的事件有:

1. `init`：初始化，如上述所描述。
2. `track`：觸發追蹤事件，並支援底下數總行為(Action)類型：

|  行為名稱|  參數名  | 資料參數  | 註記 |
|---|---|---|---|
|  頁瀏覽| pageview | [Pageview Data Params](#pageview-data-params) | 預設於每頁載完後觸發。 |
|  點追蹤  | pixel | [Pixel Data Params](#pixel-data-params) | 點追蹤，可用於頁面瀏覽進度觸發 |
|  點擊 | click | [Click Data Params](#click-data-params) | 點擊追蹤，可於按鈕/圖片/及其他點擊事件觸發。 |
|  搜尋 | search  | [Search Data Params](#search-data-params) |  可於搜尋事件觸發。|
|  結帳 | checkedout  | [Checkout Data Params](#checkout-data-params)  | 用於結帳後呼叫病傳入訂單編號，訂單細節可由資料庫取得後分析。|
|  加入購物車 | cartadd | [Cart Data Params](#cart-data-params)  | 可於加入購物車按鈕觸發。 |
|  移出購物車 | cartremove | [Cartr Data Params](#cart-data-params)  |可於移出購物車按鈕觸發。 |
|  加入我的最愛 | favoriteadd | [Favorite Data Params](#favorite-data-params)  | 可於加入我的最愛按鈕觸發。 |
|  移出我的最愛 | favoritemove | [Favorite Data Params](#favorite-data-params)  | 可於移出我的最愛按鈕觸發。 |
|  社群分享 | socialsharing | [Social Sharing Data Params](#social-sharing-data-params)  | 可於社群分享按鈕觸發。 |
|  查看優惠卷 | viewcoupon | [View Coupon Data Params](#view-coupon-data-params)  | 可於查看優惠卷時觸發。|
|  其他 | others | [Others Data Params](#others-data-params)  | 可觸發其他上述沒列舉到的事件。 |

#### 資料參數

##### PageView Data Params
```json
{ 
	"category": "product",
	"url": "url to the page",
	"utm_source": "<xx>",
	"utm_medium": "<xx>",
	"utm_term": "<xx>",
	"utm_content": "<xx>",
	"utm_campaign": "<xx>"
}
```


##### Pixel Data Params
```json
{
	"load": 75
}
```

##### Search Data Params
 ```json
 {
 	"keyword": "airpod",
 	"page": 1,
 	"total": 20
 }
 ```
 
##### Click Data Params
```json
{
	"type": "img",
	"product": {
		"pid": "7257408", 
		"cid": "415353", 
		"sid": "874"
	},
	"content_name": "product-photo1",
	"content_id": "123456",
	"content_category": "image", 
	"content_link": "/img/aaxxxx.png"
}
```

##### Cart Data Params
```json
{
	"product": {
		"pid": "7257408", 
		"cid": "415353", 
		"sid": "874", 
		"qty": 1, 
		"price": 3000}
}
```

##### Favorite Data Params
```json
{
	"product": {
		"pid": "7257408", 
		"cid": "415353", 
		"sid": "874"}
}
```


##### Social Sharing Data Params
```json
{
	"product": {
		"pid": "7257408", 
		"cid": "415353", 
		"sid": "874"}
}
```

##### View Coupon Data Params
```json
{
	"product": {
		"pid": "7257408", 
		"cid": "415353", 
		"sid": "874"},
	"coupon": {
		"type": "coupon"
	}
}
```


##### Others Data Params
```json
{
	"category": "<customized-category>",
	"data": "<raw-json-blob>"
}
```
	 	




