#Battery saving

Performing the following allows you to save up to 60% in battery consumption

```java
public class MyApplication extends Application implements BootstrapNotifier {
    private BackgroundPowerSaver backgroundPowerSaver;

    public void onCreate() {
        super.onCreate();
        // Simply constructing this class and holding a reference to it in your custom Application class
        // enables auto battery saving of about 60%
        backgroundPowerSaver = new BackgroundPowerSaver(this);
    }
}
```
