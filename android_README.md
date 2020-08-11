# DeepSelling Android Tracking Client SDK #


DeepSelling(底下簡稱ds)用戶端android函式庫。其js/iOS SDK, 請參考底下:

1. JS SDK: [SDK](./README.md)
2. iOS: [SDK](./ios_README.md)


#### Installation

添加私人庫至gradle.build

```gradle
repositories {
    maven {
        name = "deepselling"
        url = uri("https://maven.pkg.github.com/footprintai/deepselling-android")
        credentials {
            username = <USERNAME>
            password = <TOKEN>
        }
    }
}

...

dependencies {
    implementation 'org.deepselling.sdk:tracker:1.0.0'
}
```

#### Usage

初始化tracker application，如底下範例：

```java
import org.deepselling.sdk.TrackerBuilder;
import org.deepselling.sdk.extra.DeepSellingApplication;
import org.deepselling.sdk.extra.TrackHelper;

public class YourApplication extends DeepSellingApplication {
    private Tracker tracker;
    public synchronized Tracker getTracker() {
        if (tracker == null){
            tracker = TrackerBuilder.createDefault();
        }
        return tracker;
    }
    
    @Override
    public void onCreate() {
    	onInitTracker();
    }
    private void onInitTracker() {
    	...
    	// Track this app install, this will only trigger once per app version.
        TrackHelper.track().download().identifier(new DownloadTracker.Extra.ApkChecksum(this)).with(getTracker());
       ...
    }
}
```

以及如何在activity.java裡頭調用tracker

```java
import org.deepselling.sdk.TrackMe;
import org.deepselling.sdk.extra.DeepSellingApplication;

import androidx.appcompat.app.AppCompatActivity;

public class DemoActivity extends AppCompatActivity {

    private Tracker getTracker() {
        return ((DeepSellingApplication) getApplication()).getTracker();
    }
    
    protected void onCreate(Bundle savedInstanceState) {

		...
		
		findViewById(R.id.trackMainScreenViewButton).setOnClickListener(v ->
                TrackHelper.track(new TrackMe().setJSON('<json-params>').with(getTracker())
        );
		
		...
    }
}
```
其action與json-params請參考[文檔](./README.md#資料參數)
