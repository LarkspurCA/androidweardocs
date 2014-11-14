Wearable GPS
================================

By Michael Hahn, November 2014

Wearables are great when you are on the go, especially when you are out for a run or looking for destination. It would be great to just glance at your watch for your current location, rather than pulling out your handheld. Now that some wearables have a built-in GPS sensor, you can continue to use location-based apps even when the wearable is not paired with a handheld.

You implement location services in a wearable using the Google Play Fused Location Provider, just as you would in a handheld. The Android operating system takes care of choosing the GPS sensor (wearable or handheld) and implements any necessary communications between devices. The handheld GPS is preferred when both devices have a GPS sensor, and the switchover between sensors is automatic when pairing status changes.

You can create your first location-aware wearable app without writing a single line of handheld code. The section shows how.
  

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

Add a Location Listener
^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Build a Google Play Services client that includes the LocationServices API. 

  .. code-block:: java
  
    public class Wearable extends Activity implements
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
       locationRequest = LocationRequest.create();
       // Use high accuracy
       locationRequest.setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY);
       // Set the update interval to 2 seconds
       locationRequest.setInterval(2);
       // Set the fastest update interval to 2 seconds
       locationRequest.setFastestInterval(2);
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

3. Implement LocationListener callback for location updates.

  .. code-block:: java
  
    @Override
    public void onLocationChanged(Location location){
        // Log the location latitude and longitude, and
       // display the longitude value in the wearable display
        Log.v("myTag", "Latitude: " + location.getLatitude() + ", Longitude: " + location.getLongitude());
        mTextView.setText("Lat: " + String.valueOf( location.getLatitude()));
    }

Verify GPS Sensor
-------------------
This simple example works for wearables that have a GPS sensor whether they are paired with a handheld or not. It also works for wearables without GPS when they are paired with a handheld. A complete wearable implementation verifies the presence of a GPS sensor and pairing status before working with location services. For example, you can verify the presence of a GPS sensor as follows:

  .. code-block:: java
  
    getPackageManager().hasSystemFeature(PackageManager.FEATURE_LOCATION_GPS
	
