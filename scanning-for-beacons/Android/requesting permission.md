#Requesting Permission

Apps that target SDK 23+ (Android Marshmallow) must also prompt the user for a location permission after the app is launched. If you fail to prompt for and get this permission on apps on apps targeting SDK 23+, you won’t detect beacons in either the background or the foreground. For apps that target SDKS earlier than 23, failing to include these permission in the manifest will cause apps installed on Android 6 devices to fail to scan in the background. In either case where you do not have the proper permissions, you’ll get the following error in LogCat when you try to do a bluetooth scan, and no beacons will be detected:


Android Beacon Library versions 2.6 and above already include 
```java
android.permission.ACCESS_COARSE_LOCATION
``` 
in the manifest, but if you are using an older version of the library, you must add that manually. 

You must also add code like the following to an Activity if your app targets Marshmallow (if you set targetSdkVersion 23):

```java
private static final int PERMISSION_REQUEST_COARSE_LOCATION = 1;
...
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);

		...
		
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {     
        // Android M Permission check     
        if (this.checkSelfPermission(Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {         
            final AlertDialog.Builder builder = new AlertDialog.Builder(this); 
            builder.setTitle("This app needs location access");
            builder.setMessage("Please grant location access so this app can detect beacons.");
            builder.setPositiveButton(android.R.string.ok, null); 
            builder.setOnDismissListener(new DialogInterface.OnDismissListener() {  
                    @Override 
                    public void onDismiss(DialogInterface dialog) {
                            requestPermissions(new String[]{Manifest.permission.ACCESS_COARSE_LOCATION}, PERMISSION_REQUEST_COARSE_LOCATION);             
                    }  
            }); 
            builder.show();    
        }
     }
}

@Override
public void onRequestPermissionsResult(int requestCode,
										   String permissions[], int[] grantResults) {
	switch (requestCode) {
		case PERMISSION_REQUEST_COARSE_LOCATION: {
			if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
				Log.d(TAG, "coarse location permission granted");
			} else {
				final AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.setTitle("Functionality limited");
				builder.setMessage("Since location access has not been granted, this app will not be able to discover beacons when in the background.");
				builder.setPositiveButton(android.R.string.ok, null);
				builder.setOnDismissListener(new DialogInterface.OnDismissListener() {

					@Override
					public void onDismiss(DialogInterface dialog) {
					}

				});
				builder.show();
			}
			return;
		}
	}
}
```
