# OneUI-LockScreen-Widgets
Samsung One UI lockscreen widgets and blur effect customization toolkit.


# Samsung Lock Screen Widgets and Blur

This project explains how to prepare Android widgets for Samsung One UI features such as Lock Screen widgets, AOD widgets, Home Screen widgets, Samsung widget sizes, colorful and monotone styles, and launcher blur background behavior.

The main idea is that Samsung reads extra metadata from the widget provider XML file inside `res/xml`. These values are not part of the standard Android widget API, so custom attributes must be declared inside `res/values` before they can be used in the provider XML.

## 1. Declare Samsung Widget Attributes in res/values

Create a file such as:

```text
res/values/samsung_widget_attrs.xml
```

Add the Samsung widget attributes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <attr name="targetHost">
        <flag name="home" value="0x1" />
        <flag name="lockAndAod" value="0x2" />
    </attr>

    <attr name="widgetStyle">
        <flag name="colorful" value="0x1" />
        <flag name="monotone" value="0x2" />
    </attr>

    <attr name="lockWidgetSize">
        <flag name="tiny" value="0x1" />
        <flag name="small" value="0x2" />
    </attr>

    <attr name="monotoneWidgetSize">
        <flag name="tiny" value="0x1" />
        <flag name="small" value="0x2" />
    </attr>

    <attr name="initialLayoutTiny" format="reference" />
    <attr name="previewLayoutTiny" format="reference" />
    <attr name="monotoneInitialLayoutTiny" format="reference" />
    <attr name="monotonePreviewLayoutTiny" format="reference" />

    <declare-styleable name="AppWidgetProviderInfo">
        <attr name="targetHost" />
        <attr name="widgetStyle" />
        <attr name="lockWidgetSize" />
        <attr name="monotoneWidgetSize" />
        <attr name="initialLayoutTiny" />
        <attr name="previewLayoutTiny" />
        <attr name="monotoneInitialLayoutTiny" />
        <attr name="monotonePreviewLayoutTiny" />
    </declare-styleable>

</resources>
```

`targetHost` controls where the widget can appear. `home` is for the Home Screen. `lockAndAod` is for the Lock Screen and Always On Display.

`widgetStyle` controls the supported visual styles. `colorful` uses the normal widget colors. `monotone` is useful for Lock Screen and AOD because the system may render the widget with a simpler style.

`lockWidgetSize` controls the widget size on the Lock Screen. Usually `tiny` or `small` is used because the Lock Screen area is limited.

## 2. Define a Lock Screen and AOD Widget Provider

Inside:

```text
res/xml
```

Create a provider file such as:

```text
res/xml/lock_clock_widget_info.xml
```

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"

    android:minWidth="56dp"
    android:minHeight="56dp"
    android:updatePeriodMillis="0"
    android:initialLayout="@layout/widget_lock"
    android:previewLayout="@layout/widget_lock"
    android:resizeMode="none"
    android:widgetCategory="keyguard"

    app:targetHost="lockAndAod"
    app:widgetStyle="colorful|monotone"
    app:lockWidgetSize="tiny"
    app:monotoneWidgetSize="tiny"

    app:initialLayoutTiny="@layout/widget_lock"
    app:previewLayoutTiny="@layout/widget_lock"
    app:monotoneInitialLayoutTiny="@layout/widget_lock_mono"
    app:monotonePreviewLayoutTiny="@layout/widget_lock_mono" />
```

The important line is:

```xml
android:widgetCategory="keyguard"
```

This declares the widget as a Lock Screen widget.

This line tells One UI that the widget targets the Lock Screen and AOD:

```xml
app:targetHost="lockAndAod"
```

If the widget supports both normal and monotone modes, use:

```xml
app:widgetStyle="colorful|monotone"
```

Then link each mode to its own layout:

```xml
app:initialLayoutTiny="@layout/widget_lock"
app:monotoneInitialLayoutTiny="@layout/widget_lock_mono"
```

This allows the system to use the normal layout in one place and the monotone layout in another.

## 3. Example Lock Screen Widget Layout

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/transparent"
    android:paddingStart="10dp"
    android:paddingEnd="10dp"
    android:paddingTop="6dp"
    android:paddingBottom="6dp">

    <TextView
        android:id="@+id/widget_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|center_vertical"
        android:text="Prayer"
        android:textColor="@android:color/white"
        android:textSize="13sp"
        android:textStyle="bold"
        android:includeFontPadding="false"
        android:singleLine="true" />

    <TextView
        android:id="@+id/widget_value"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="start|center_vertical"
        android:text="12:17"
        android:textColor="@android:color/white"
        android:textSize="15sp"
        android:textStyle="bold"
        android:includeFontPadding="false"
        android:singleLine="true" />

</FrameLayout>
```

Lock Screen widget layouts should stay lightweight. Avoid complex layouts and large images because the available area is small and the system may render the widget inside a limited container.

## 4. Home Screen Widget Sizes

Home Screen widgets use slightly different Samsung attributes. Add this inside `res/values`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <attr name="widgetStyle">
        <flag name="colorful" value="0x1" />
        <flag name="monotone" value="0x2" />
    </attr>

    <attr name="widgetSize">
        <flag name="small" value="0x2" />
        <flag name="wideSmall" value="0x4" />
        <flag name="medium" value="0x8" />
        <flag name="large" value="0x10" />
        <flag name="extraLarge" value="0x20" />
    </attr>

    <attr name="featuredWidget">
        <flag name="small" value="0x2" />
        <flag name="wideSmall" value="0x4" />
        <flag name="medium" value="0x8" />
        <flag name="large" value="0x10" />
        <flag name="extraLarge" value="0x20" />
    </attr>

    <declare-styleable name="AppWidgetProviderInfo">
        <attr name="widgetStyle" />
        <attr name="widgetSize" />
        <attr name="featuredWidget" />
    </declare-styleable>

</resources>
```

Then use the attributes inside the Home Screen widget provider:

```xml
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"

    android:minWidth="250dp"
    android:minHeight="110dp"
    android:minResizeWidth="180dp"
    android:minResizeHeight="90dp"
    android:targetCellWidth="4"
    android:targetCellHeight="2"
    android:updatePeriodMillis="0"
    android:initialLayout="@layout/widget_home_large"
    android:previewLayout="@layout/widget_home_preview"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen"

    app:widgetStyle="colorful"
    app:widgetSize="large"
    app:featuredWidget="large" />
```

`widgetSize` defines the supported Samsung widget size.

`featuredWidget` helps the launcher decide which size should be highlighted inside the widget picker.

If the widget supports multiple sizes, write them like this:

```xml
app:widgetSize="small|wideSmall|medium|large|extraLarge"
app:featuredWidget="small|wideSmall|medium|large|extraLarge"
```

## 5. Samsung Launcher Blur Widget

To test blur behavior in Samsung Launcher, the layout should include a dedicated background view with this id:

```xml
android:id="@android:id/background"
```

Example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@android:id/background"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#80FFFFFF" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        android:padding="18dp">

        <TextView
            android:id="@+id/widget_blur_title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Blur Test"
            android:textColor="#FFFFFFFF"
            android:textSize="18sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/widget_blur_subtitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="6dp"
            android:text="Samsung background layer"
            android:textColor="#CCFFFFFF"
            android:textSize="12sp" />

    </LinearLayout>

</FrameLayout>
```

The `@android:id/background` id is important because some Samsung Launcher versions treat this view as the widget background layer. If the background is only placed on the root view, the launcher may not detect it correctly.

The background color or transparency can be changed from code using `RemoteViews`:

```kotlin
object SamsungBlurHelper {

    fun applyTint(
        views: RemoteViews,
        enabled: Boolean,
        color: Int
    ) {
        val finalColor = if (enabled) color else android.graphics.Color.TRANSPARENT

        try {
            views.setInt(
                android.R.id.background,
                "setBackgroundColor",
                finalColor
            )
        } catch (_: Throwable) {
        }
    }
}
```

Usage example:

```kotlin
val views = RemoteViews(context.packageName, R.layout.widget_blur_demo)

SamsungBlurHelper.applyTint(
    views = views,
    enabled = true,
    color = android.graphics.Color.argb(120, 255, 255, 255)
)

appWidgetManager.updateAppWidget(appWidgetId, views)
```

If the widget appears with only a gray or translucent background, the launcher did not apply blur and only used the color as a normal background. If it appears like a glass or blurred layer, the system detected the background layer correctly.

## 6. Simple Widget Provider Class

Example Lock Screen widget provider:

```kotlin
class LockClockWidgetProvider : AppWidgetProvider() {

    override fun onUpdate(
        context: Context,
        manager: AppWidgetManager,
        ids: IntArray
    ) {
        ids.forEach { id ->
            val views = RemoteViews(
                context.packageName,
                R.layout.widget_lock
            )

            views.setTextViewText(R.id.widget_title, "Dhuhr")
            views.setTextViewText(R.id.widget_value, "12:17")

            manager.updateAppWidget(id, views)
        }
    }
}
```

Example interactive counter provider:

```kotlin
class CounterLockWidgetProvider : AppWidgetProvider() {

    override fun onUpdate(
        context: Context,
        manager: AppWidgetManager,
        ids: IntArray
    ) {
        val prefs = context.getSharedPreferences("counter_widget", Context.MODE_PRIVATE)
        val count = prefs.getInt("count", 0)

        ids.forEach { id ->
            val views = RemoteViews(context.packageName, R.layout.widget_counter_lock)
            views.setTextViewText(R.id.widget_counter_value, count.toString())

            val intent = Intent(context, CounterLockWidgetProvider::class.java).apply {
                action = "com.example.widget.ACTION_COUNTER_PLUS"
            }

            val pendingIntent = PendingIntent.getBroadcast(
                context,
                1001,
                intent,
                PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
            )

            views.setOnClickPendingIntent(R.id.widget_root, pendingIntent)

            manager.updateAppWidget(id, views)
        }
    }

    override fun onReceive(context: Context, intent: Intent) {
        super.onReceive(context, intent)

        if (intent.action == "com.example.widget.ACTION_COUNTER_PLUS") {
            val prefs = context.getSharedPreferences("counter_widget", Context.MODE_PRIVATE)
            val next = prefs.getInt("count", 0) + 1
            prefs.edit().putInt("count", next).apply()

            val manager = AppWidgetManager.getInstance(context)
            val ids = manager.getAppWidgetIds(
                ComponentName(context, CounterLockWidgetProvider::class.java)
            )

            onUpdate(context, manager, ids)
        }
    }
}
```

## 7. Register the Widget in AndroidManifest

Each widget provider must be registered inside `AndroidManifest.xml`.

Example:

```xml
<receiver
    android:name=".LockClockWidgetProvider"
    android:exported="true">

    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>

    <meta-data
        android:name="android.appwidget.provider"
        android:resource="@xml/lock_clock_widget_info" />

</receiver>
```

For Samsung Basic Interaction compatibility, add this inside `<application>`:

```xml
<meta-data
    android:name="SamsungBasicInteraction"
    android:value="SEP10" />
```

Full example:

```xml
<application
    android:allowBackup="true"
    android:theme="@style/AppTheme">

    <meta-data
        android:name="SamsungBasicInteraction"
        android:value="SEP10" />

    <receiver
        android:name=".LockClockWidgetProvider"
        android:exported="true">

        <intent-filter>
            <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
        </intent-filter>

        <meta-data
            android:name="android.appwidget.provider"
            android:resource="@xml/lock_clock_widget_info" />

    </receiver>

</application>
```

## 8. Notes

Files inside `res/values` do not add system features by themselves. They only declare custom attributes so the project can build and use Samsung-specific XML values.

The `res/xml/*_widget_info.xml` file is the most important part because it tells the system whether the widget is for the Home Screen, Lock Screen, or AOD.

For Lock Screen widgets, use small sizes such as `tiny` and `small`.

For Home Screen widgets, use `widgetSize` and `featuredWidget` to support Samsung widget sizes.

For blur testing, do not forget to add a dedicated background view with this id:

```xml
android:id="@android:id/background"
```

Android widgets use `RemoteViews`, so not every normal Android view is supported. It is better to use simple views such as `FrameLayout`, `LinearLayout`, `TextView`, `ImageView`, and `ProgressBar`.

If Samsung-specific behavior does not appear on a device, the result depends on the One UI version, Samsung Launcher version, and whether the device supports these widget features.
