Wearable GPS
================================

By Michael Hahn, January 2015

Wearables are great when you are on the go, especially when you are out for a run or looking for destination. It would be great to just glance at your watch for your current location, rather than pulling out your handheld. Now that some wearables have a built-in GPS sensor, you can continue to use location-based apps even when the wearable is not paired with a handheld.

You implement location services in a wearable using the Google Play Fused Location Provider, just as you would in a handheld. The Android operating system takes care of choosing the GPS sensor (wearable or handheld) and implements any necessary communications between devices. The handheld GPS is preferred when both devices have a GPS sensor, and the switchover between sensors is automatic when pairing status changes.

You can create your first location-aware wearable app without writing a single line of handheld code. You don't even need to implement a handheld Activity. The section shows how.
  

First Wearable GPS
--------------------

If you have not already done so, :ref:`newapp`. The new project wizard in Android Studio beta creates a project with two main activities, one for the handheld device and another for the wearable. These two activities use the same package name, which is essential for the wearable GPS to work.

Add Permissions and Metadata for Google Play Services
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	
Modify the wearable manifest file to permit location service access and define Google Play Services metadata. This example gives access permission for course location only.

  .. code-block:: java
  
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
  
    <application>
    ...
      <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application> 

Add Build Dependencies
************************

Add the following build dependencies to the wearable build.gradle file (Module:wear) in the Gradle Scripts folder, if necessary. 

  .. code-block:: java
  
    dependencies {
      compile 'com.google.android.support:wearable:1.1.0'
      compile 'com.google.android.gms:play-services-wearable:6.5.+'
      compile 'com.google.android.gms:play-services-location:6.5.+'
    }

Add a Location Listener
^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Build a Google Play Services client that includes the LocationServices API. 

  .. code-block:: java
  
    public class WearableActivity extends Activity implements
      GoogleApiClient.ConnectionCallbacks,
      GoogleApiClient.OnConnectionFailedListener
      LocationListener {

      GoogleApiClient googleClient;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handheld);
        
      // Build a new GoogleApiClient
      googleClient = new GoogleApiClient.Builder(this)
        .addApi(LocationServices.API)
        .addConnectionCallbacks(this)
        .addOnConnectionFailedListener(this)
        .build();
      }
	  // Location listener implementation (Step 2)
	  ...
    } 

2. Register a location listener for the Fused Location API. 

  .. code-block:: java
  
    // Connect to Google Play Services when the Activity starts
    @Override
    protected void onStart() {
	  super.onStart();
      googleClient.connect();
    }
    
	// Register as a listener when connected 
    @Override
    public void onConnected(Bundle connectionHint) {
      
      // Create the LocationRequest object
      LocationRequest locationRequest = LocationRequest.create();
      // Use high accuracy
      locationRequest.setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY);
      // Set the update interval to 2 seconds
      locationRequest.setInterval(TimeUnit.SECONDS.toMillis(2));
      // Set the fastest update interval to 2 seconds
      locationRequest.setFastestInterval(TimeUnit.SECONDS.toMillis(2));
      // Set the minimum displacement
      locationRequest.setSmallestDisplacement(2);
         
       // Register listener using the LocationRequest object
       LocationServices.FusedLocationApi.requestLocationUpdates(googleClient, locationRequest, this);
     }
	  
    // Disconnect from Google Play Services when the Activity stops
    @Override
    protected void onStop() {
	
    if (mGoogleApiClient.isConnected()) {
        LocationServices.FusedLocationApi.removeLocationUpdates(mGoogleApiClient, this);
        googleClient.disconnect();
      }
      super.onStop();
    }	  
	  
    // Placeholders for required connection callbacks
    @Override
    public void onConnectionSuspended(int cause) { }

    @Override
    public void onConnectionFailed(ConnectionResult connectionResult) { }

3. Implement the LocationListener callback for location updates.

  .. code-block:: java
  
    @Override
    public void onLocationChanged(Location location){
   
        // Display the latitude and longitude in the UI
        mTextView.setText("Latitude:  " + String.valueOf( location.getLatitude()) +
                          "\nLongitude:  " + String.valueOf( location.getLongitude()));
    }

This example displays the current latitude and longitude in the wearable UI. The location is from the handheld GPS.

   .. figure:: images/offboardGPS.png
      :scale: 50	

When the wearable is not paired with the handheld, for example when you leave the wearable behind to exercise, the GPS automatically switches to the onboard GPS.

   .. figure:: images/onboardGPS.png
      :scale: 50	
	
This screen capture is from the Sony SmartWatch 3. The number of digits displayed is greater now, providing a visual clue that the wearable GPS is active. When you return and re-pair with the handheld, the GPS automatically switches back to the handheld and the display returns to its original format.

Verify GPS Sensor
-------------------
This simple example works for all wearables, with or without a GPS sensor. Those without GPS must pair with a handheld to get location updates. A more complete implementation verifies the presence of a GPS sensor and pairing status before using location services, and warns users or reduces functionality when necessary. You can verify the presence of a GPS sensor as follows:

  .. code-block:: java
  
    getPackageManager().hasSystemFeature(PackageManager.FEATURE_LOCATION_GPS
	

Example
--------

The working example for wearable GPS is at https://github.com/LarkspurCA/WearableGPS.
