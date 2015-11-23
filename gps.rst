Wearable GPS
================================

By Michael Hahn, November 2015

Wearables are great when you are on the go, especially when you are out for a run or looking for destination. It would be great to just glance at your watch for your current location, rather than pulling out your handheld. Now that some wearables have a built-in GPS sensor, you can continue to use location-based apps even when the wearable is not paired with a handheld.

You implement location services in a wearable using the Google Play Fused Location Provider, just as you would in a handheld. The Android operating system takes care of choosing the GPS sensor (wearable or handheld) and implements any necessary communications between devices. The handheld GPS is preferred when both devices have a GPS sensor, and the switchover between sensors is automatic when pairing status changes.

You can create your first location-aware wearable app without writing a single line of handheld code. You don't even need to implement a handheld Activity. The section shows how.
  

First Wearable GPS
--------------------

First :ref:`new_wear_app`. The new project has two applications, one for the handheld device and another for the wearable.  These two activities use the same package name, which is essential for the wearable GPS to work.

Add Permissions and Metadata for Google Play Services
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	
Modify the wearable manifest file to permit access to the fine location, and define Google Play Services metadata. Update the manifest for both mobile and wear applications. This is necessary when creating an APK later.

  .. code-block:: java
  
  <manifest ...
  
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
  
    <application> ...

      <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />  
    </application> 
  
  </manifest>

Add Build Dependencies
************************

Add the following build dependencies to the wearable build.gradle file (Module:wear) in the Gradle Scripts folder, if necessary. 

  .. code-block:: java
  
    dependencies {
      compile 'com.google.android.support:wearable:1.3.0'
      compile 'com.google.android.gms:play-services-wearable:68.3.0'
      compile 'com.google.android.gms:play-services-location:8.3.0'
    }

Add a Location Listener
^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Extend the wearable activity for the Google Play Services location API. 

  .. code-block:: java
  
    public class MainWearActivity extends Activity implements
      GoogleApiClient.ConnectionCallbacks,
      GoogleApiClient.OnConnectionFailedListener,
      LocationListener {
	  ...
    } 

2. Create a Google API Client and add the Fused Location API service. 

  .. code-block:: java
  
    GoogleApiClient googleApiClient;
  
    @Override
    protected void onStart() {
      super.onStart();
      if (googleApiClient == null) {
        googleApiClient = new GoogleApiClient.Builder(this)
          .addApi(LocationServices.API)
          .addConnectionCallbacks(this)
          .addOnConnectionFailedListener(this)
          .build();
        }
        googleApiClient.connect();
    }
    
	// Create a Location Request and register as a listener when connected 
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
	
    if (googleApiClient.isConnected()) {
        LocationServices.FusedLocationApi.removeLocationUpdates(googleApiClient, this);
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

This example displays the current latitude and longitude in the wearable UI. 

   .. figure:: images/onboardGPS.png
      :scale: 50	


Verify GPS Sensor
-------------------
This simple example works for all wearables, with or without a GPS sensor. Those without GPS must pair with a handheld to get location updates. A more complete implementation verifies the presence of a GPS sensor and pairing status before using location services, and warns users or reduces functionality when necessary. You can verify the presence of a GPS sensor as follows:

  .. code-block:: java
  
    getPackageManager().hasSystemFeature(PackageManager.FEATURE_LOCATION_GPS
	

Example
--------

The working example for a Wearable GPS is at https://github.com/LarkspurCA/WearableGPS. It also enables the always-on feature to keep the GPS app from timing out to the watchface after a few seconds of inactivity.

Golf Rangefinder Example
-------------------------

Golf is an activity where you often want to know the distance to the next hole, so you can choose the perfect club for the shot. There are plenty of rangefinders on the market today, but few are as small and convenient as a smart watch.  The golf rangefinder example (Clipon Caddie) is a sample application that utilizes the GPS concepts in this section to perform a useful task for golfers, displaying the number of yards from the current location to the upcoming hole. The full source code code is available at https://github.com/GolfMarin/CliponCaddie and the installable app is at http://cliponcaddie.com.

