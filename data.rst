Wearable Data Sync
================

By Michael Hahn, September 2014

The wearable data layer can sync either messages or data. A message contains a single text string, but data is typically wrapped in a DataMap object. A DataMap is similar to a Bundle in that it contains a collection of of one or more of data types, stored as key/value pairs. Android uses a Bundle to encapsulate data exchanged between activities. Similarly, wearable apps can use a DataMap to encapsulate the data exchanged over the wearable data layer. 

Google Play services Version 5 includes a Wearable Data API that provides access to the data layer of a data communications link between the two devices. App data moves down the protocol stack on the sending side, across the bluetooth link, then up the stack on the receiving side. The following diagram shows how a handheld sends a data to a wearable using a DataMap and the Wearable.DataApi. 

  .. figure:: images/data-layer-data.png
      :scale: 70
	  
On the handheld side, you start with a PutDataMapRequest. This is a helper class that simplifies the process of creating the DataMap, DataItem, and PutDataRequest objects that are needed for sending data. On the receiving side a WearableListener Service receives changed data and returns a buffer of  DataEvents. You get a DataItem from the buffer, convert it to a DataMapItem, convert that to a DataMap object, and then get the original handheld data. A WearableListenerService is not the only way to receive data, but it is easy to implement because Android Wear manages its life-cycle.

First Wearable Data
--------------------

If you have not already done so, :ref:`newapp`. The new project wizard in Android Studio beta creates a project with two main activities, one for the handheld device and another for the wearable. These two activities use the same package name, which is essential for the wearable data layer to work.

Data layer transfers can originate from either a handheld or wearable. For bidirectional data both the handheld and wearable should implement the code in this section.

Add a Data Sender
^^^^^^^^^^^^^^^^^^

To send a data object , update the code in the main Activity of the sending device.

1. Build a GoogleApiClient. 

  .. code-block:: java
  
    public class Handheld extends Activity implements
      GoogleApiClient.ConnectionCallbacks,
      GoogleApiClient.OnConnectionFailedListener {

      GoogleApiClient googleClient;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handheld);
        
      // Build a new GoogleApiClient
      googleClient = new GoogleApiClient.Builder(this)
        .addApi(Wearable.API)
        .addConnectionCallbacks(this)
        .addOnConnectionFailedListener(this)
        .build();
      }
	  // Data layer and lifecycle implementation (Step 2)
	  ...
    } 

2. Add callback methods for the data layer and lifecycle events. For simplicity, send a data object in the onConnected callback method.

  .. code-block:: java
  
    // Connect to the data layer when the Activity starts
    @Override
    protected void onStart() {
	  super.onStart();
      googleClient.connect();
    }
	  	  
    // Send a data object when the data layer connection is successful.
    @Override
    public void onConnected(Bundle connectionHint) {
	
      // Create a DataMap object and send it to the data layer
      DataMap dataMap = new DataMap();
      dataMap.putLong("time", new Date().getTime());
      dataMap.putString("hole", "1");
      dataMap.putString("front", "250");
      dataMap.putString("middle", "260");
      dataMap.putString("back", "270");
      //Requires a new thread to avoid blocking the UI
      new SendToDataLayerThread("/data", dataMap).start();
     }
	  
    // Disconnect from the data layer when the Activity stops
    @Override
    protected void onStop() {
      if (null != googleClient && googleClient.isConnected()) {
        googleClient.disconnect();
        }
        super.onStop();
    }	  
	  
    // Placeholders for required connection callbacks
    @Override
    public void onConnectionSuspended(int cause) { }

    @Override
    public void onConnectionFailed(ConnectionResult connectionResult) { }

3. Define a class that extends the Thread class and implements a method that sends your data object to all nodes currently connected to the data layer. This task can block the main UI thread, so it must run in a new thread. 

  .. code-block:: java
  
    class SendToDataLayerThread extends Thread {
      String path;
      String data;

      // Constructor for sending data objects to the data layer
      SendToDataLayerThread(String p, DataMap data) {
        path = p;
        dataMap = data;
      }

      public void run() {
        NodeApi.GetConnectedNodesResult nodes = Wearable.NodeApi.getConnectedNodes(googleClient).await();
        for (Node node : nodes.getNodes()) {

          // Construct a DataRequest and send over the data layer
          PutDataMapRequest putDMR = PutDataMapRequest.create(path);
          putDMR.getDataMap().putAll(dataMap);
          PutDataRequest request = putDMR.asPutDataRequest();
          DataApi.DataItemResult result = Wearable.DataApi.putDataItem(googleClient,request).await();
          if (result.getStatus().isSuccess()) {
            Log.v("myTag", "DataMap: " + dataMap + " sent to: " + node.getDisplayName());
          } else {
            // Log an error
            Log.v("myTag", "ERROR: failed to send DataMap");
          }
        }
      }
    }

Add a Data Receiver
^^^^^^^^^^^^^^^^^^^^

You can monitor the data layer for new data objects using either a listener service or listener activity.  This section explains how to implement a listener service for data objects. 

1. Enable the listener service in the manifest file for the wear application.

  .. code-block:: java
  
    <uses-feature android:name="android.hardware.type.watch" />

    <application
      ...
      <service android:name=".ListenerService">
        <intent-filter>
          <action android:name="com.google.android.gms.wearable.BIND_LISTENER" />
        </intent-filter>
      </service>
    </application>

2. Create a listener in the wear application that extends the WearableListenerService. This example logs any received data objects to the debug output. 

  .. code-block:: java
  
    public class ListenerService extends WearableListenerService {

      @Override
      public void ondataReceived(DataEvent dataEvent) {

        if (dataEvent.getPath().equals("/data")) {
          final String data = new String(data Event.getData());
          Log.v("myTag", "data path received on watch is: " + dataEvent.getPath());
          Log.v("myTag", "data received on watch is: " + data);
        }
        else {
          super.ondataReceived(dataEvent);
        }
      }  
    }
	
Using Received Data
^^^^^^^^^^^^^^^^^^^^^
	
In this example, a background service receives the data. If you need this data in the UI or elsewhere, you can broadcast the results locally, as described in :ref:`forward`. Just add a Bundle (DataMap.toBundle) as the intent extra, instead of the message string.
