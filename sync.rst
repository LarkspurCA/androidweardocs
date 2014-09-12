Wearable Data Layer
================================

By Michael Hahn, August 2014

An application that runs on a wearable device usually utilizes some of the capabilities of a paired handheld device. This means you need two separate Android apps, one that runs on the wearable and another that runs on the handheld. These two apps communicate with one another over the bluetooth link that connects the two devices. 

Google Play services Version 5 includes a Wearable Message API that provides access to the data layer of a data communications link between the two devices. Messages or data move down the protocol stack on the sending side, across the bluetooth link, then up the stack on the receive side. The following diagram shows how a simple message flows through the wearable communications link.

  .. figure:: images/data-layer-stack.png
      :scale: 70
	  
In this example, a handheld sends a message to a wearable using the sendMessage method of the Wearable.MessageApi. On the receiving side, a WearableListenerService monitors the data layer and invokes the onMessageReceived callback when a message arrives. The listener service then performs some application-specific task based on the received message. The WearableListenerService is not the only way to receive data, but it is easy to implement because Android Wear manages its life-cycle. 

First Wearable Message
------------------------

If you have not already done so, :ref:`newapp`. The new project wizard in Android Studio beta creates a project with two main activities, one for the handheld device and another for the wearable. These two activities use the same package name, which is essential for the wearable data layer to work.

Data layer messages can originate from either a handheld or wearable. For bidirectional messaging both the handheld and wearable should implement the code in this section.

Add Build Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^

Add build dependencies for the wearable support package and Google Play services to the build.gradle file, if they are not already present. For Android Studio Beta, modify the build.gradle file in both the "mobile" and "wear" branches of the project hierarchy. 

  .. code-block:: java
  
    dependencies {
      compile fileTree(dir: 'libs', include: ['*.jar'])
      compile 'com.google.android.support:wearable:+' 
      compile 'com.google.android.gms:play-services-wearable:+'
    }
	
Add a Message Sender
^^^^^^^^^^^^^^^^^^^^^^

To send a message , update the code in the main Activity of the sending device.

1. Build a GoogleApiClient. 

  .. code-block:: java
  
    public class Handheld extends Activity implements
      GoogleApiClient.ConnectionCallbacks,
      GoogleApiClient.OnConnectionFailedListener {

      GoogleApiClient googleClient;
      private static final String MESSAGE_RECEIVED_PATH = "/message_received_path";
      private static final String MESSAGE = "Hello wearable\n Via the data layer";

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

2. Add callback methods for the data layer and lifecycle events. 

  .. code-block:: java
  
      // Connect to the data layer when the Activity starts
      @Override
      protected void onStart() {
	    super.onStart();
        googleClient.connect();
      }
	  	  
      // Send a message when the data layer connection is successful.
      @Override
      public void onConnected(Bundle connectionHint) {
	    sendDataLayerMessage();
      }
	  
      // Disconnect from the data layer when the Activity stops
      @Override
      protected void onStop() {
        if (null != googleClient && googleClient.isConnected()) {
          googleClient.disconnect();
          }
          super.onStop();
      }	  
	  
3. Add onConnectionSuspended and onConnectionFailed callbacks. For now they can be placeholders.  

4. Add a method that sends a message to all nodes currently connected to the data layer. This task can block the main UI thread, so it must run in a new thread. 

  .. code-block:: java
  
    private void sendDataLayerMessage() {
      new Thread(new Runnable() {
        @Override
        public void run() {
          // Get the connected nodes and wait for results
          NodeApi.GetConnectedNodesResult nodes = Wearable.NodeApi.getConnectedNodes(googleClient).await();
          for (Node node : nodes.getNodes()) {
            // Send a message and wait for result
            SendMessageResult result =
              Wearable.MessageApi.sendMessage(googleClient, node.getId(),
              MESSAGE_RECEIVED_PATH, MESSAGE.getBytes()).await();
              if (result.getStatus().isSuccess()) {
                Log.v("myTag", "Message sent to : " + node.getDisplayName());
              }
              else {
                // Log an error
                Log.v("myTag", "MESSAGE ERROR: failed to send Message");
              }
           }
        }
      }).start();
    }
	  
Add a Message Receiver
^^^^^^^^^^^^^^^^^^^^^^^

You can monitor the data layer for new messages using either a listener service or listener activity.  This section explains how to implement a listener service for messages. 

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

2. Create a listener that extends the WearableListenerService. This example logs any received message to the debug output.

  .. code-block:: java
  
      public class ListenerService extends WearableListenerService {
	  
        private static final String MESSAGE_RECEIVED_PATH = "/message_received_path";

        @Override
        public void onMessageReceived(MessageEvent messageEvent) {

          if (messageEvent.getPath().equals(MESSAGE_RECEIVED_PATH)) {
            final String message = new String(messageEvent.getData());
            Log.v("myTag", "Message path received on watch is: " + messageEvent.getPath());
            Log.v("myTag", "Message received on watch is: " + message);
          }
          else {
            super.onMessageReceived(messageEvent);
          }
        }  
      }

Forward Message to the Main Activity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The message listener might need to forward received messages to a component of the application that is running on a different thread. The LocalBroadcastManager can be useful in these cases. This procedure shows how the LocalBroadcastManager can forward received messages to the main Activity, for display in the UI.

1. In the wearable listener service, send a local broadcast that contains the message.

  .. code-block:: java
  
    @Override
    public void onMessageReceived(MessageEvent messageEvent) {

      if (messageEvent.getPath().equals(MESSAGE_RECEIVED_PATH)) {

        // Broadcast message to wearable activity for display
        Intent messageIntent = new Intent();
        messageIntent.setAction("message-forwarded-from-data-layer");
        messageIntent.putExtra("message", message);
        LocalBroadcastManager.getInstance(this).sendBroadcast(messageIntent);
        }
      else {
        super.onMessageReceived(messageEvent);
      }
    }
	
2. In the main Activity, register a local broadcast receiver in onCreate method. This receiver filters incoming broadcasts for those that contain messages from the data layer.

  .. code-block:: java

      @Override 
      protected void onCreate(Bundle savedInstanceState) {
        // Basic UI code, generated by New Project wizard.
		...

        // Register a local broadcast receiver, defined is Step 3.
        IntentFilter messageFilter = new IntentFilter("message-forwarded-from-data-layer");
        MessageReceiver messageReceiver= new MessageReceiver();
        LocalBroadcastManager.getInstance(this).registerReceiver(messageReceiver, messageFilter);
      }
	
3. In the main Activity, define a broadcast receiver class that extracts the message from the broadcast and  displays the message text the UI.

	  .. code-block:: java

	      public class MessageReceiver extends BroadcastReceiver {
	        @Override
	        public void onReceive(Context context, Intent intent) {
	          String message = intent.getStringExtra("message");
			  // Display message in UI
	          mTextView.setText(message);
	        }
	      }
	    }
		
Try the First Data Layer App
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To try your new data layer app, set up your development environment with either emulators or devices, for both the handheld and wearable. Google documentation covers these topics.

Start the "wear" app. It displays the default Hello message generated by the Android Studio New Project wizard:

  .. figure:: images/wear-square.png
      :scale: 50
	  
Then launch the handheld app. The wearable display changes to the message sent from the handheld device through the wearable data layer. 

  .. figure:: images/wear-message.png
      :scale: 50	  