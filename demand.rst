.. _demand:

Android Wear Demand
===================

By Michael Hahn, November 2015

The Demand context is one of the core functions for Android Wear. A demand is displayed as a large icon, typically when you swipe a displayed suggestion (notification). You tap on an icon to perform the desired demand.


 .. figure:: images/demand-2.png
    :scale: 40
    :align: right

When new email arrives for example, you swipe to the left to scroll through the demand icons, such as open, reply, and archive. You can create custom icons for your app as well. This section explains how to code your own wearable demand that handles a voice reply.

First Android Wear Demand
--------------------------

If you have not already done so, :ref:`newapp`. The new project wizard in Android Studio creates a project with two main activities, one for the handheld device and another for the wearable. To create your first demand, add code in the handheld activity only, located in the "mobile" branch of the project hierarchy. The software preinstalled on a wearable device or emulator handles the task of displaying notifications and handling demands on the wearable side.

Verify Build Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^

Verify that the build.gradle (Module: app) file includes the following dependencies:

  .. code-block:: java
   
    compile 'com.android.support:appcompat-v7:23.1.0'
    compile 'com.android.support:design:23.1.0'
	

Modify the Handheld Activity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Your handheld activity initiates the process by creating a notification that includes an action (demand). When a user views the notification and invokes the demand, the wearable broadcasts the demand to the handheld for processing. You build the notification with a hierarchy of objects, intent -> pending intent -> notification action -> wearable extender, and finally the notification itself. 

1. Create an Intent that defines the action that the handheld device should take in response to a wearable demand. 

   The Intent has the following parameters:

   > The context of the activity, service, or broadcast receiver on the handheld that handles wearable demands.
   
   > The name of the class on the handheld that receives wearable demands.
   
   > A constant that specifies a demand action.
   
   > An extra with details about the requested action.
   
   The following example shows how to create an intent for a reply demand. The DemandIntentReceiver is defined later in :ref:`demandReceiver`.
   
  .. code-block:: java
  
    public static final String ACTION_DEMAND = "com.androidweardocs.ACTION_DEMAND";
    public static final String EXTRA_MESSAGE = "com.androidweardocs.EXTRA_MESSAGE";
	public static final String EXTRA_VOICE_REPLY = "com.androidweardocs.EXTRA_VOICE_REPLY";

    Intent demandIntent = new Intent(this, DemandIntentReceiver.class)
      .putExtra(EXTRA_MESSAGE, “Reply selected.")
      .setAction(ACTION_DEMAND);

2. Create a PendingIntent to include in the notification. 

  A PendingIntent wraps the intent to grant the privileges it needs for to execute in your application. It contains the context of the activity, service, or broadcast receiver that will receive the demand, and the Intent object itself. This example creates a PendingIntent using the context of a broadcast receiver. Use getActivity instead of getBroadcast if your activity receives demands.

  .. code-block:: java

    PendingIntent demandPendingIntent =
        PendingIntent.getBroadcast(this, 0, demandIntent, 0);

3. Create a RemoteInput object to hold a voice reply from the wearable device. A voice request or response is a common action for a wearable device because of the small size of the UI.

  .. code-block:: java
  
    String replyLabel = getResources().getString(R.string.app_name);
    RemoteInput remoteInput = new RemoteInput.Builder(EXTRA_VOICE_REPLY)
      .setLabel(replyLabel)
      .build();
	  
4. Create a wearable action.

  The following example creates an wearable action that uses a standard reply icon and label, adds the pending intent, and the RemoteInput object for voice.

  .. code-block:: java
  
    NotificationCompat.Action replyAction =
      new NotificationCompat.Action.Builder(R.drawable.ic_reply_icon,
        getString(R.string.reply_label), demandPendingIntent)
        .addRemoteInput(remoteInput)
        .build(); 

5. Create a WearableExtender for the a notification and add the wearable action.

  .. code-block:: java
  
    NotificationCompat.WearableExtender wearableExtender =
      new NotificationCompat.WearableExtender()
	  .addAction(replyAction);

6. Create a notification and extended it with the wearable extender just created. The following example creates a notification that includes a reply action (demand).

  .. code-block:: java

     Notification notification =
       new NotificationCompat.Builder(this)
         .setContentTitle("Hello Wearable!")
         .setContentText("First Wearable demand.")
         .setSmallIcon(R.drawable.ic_launcher)
         .extend(wearableExtender)
         .build();
  
7. Get an instance of the Notification Manager service.

  .. code-block:: java

    NotificationManagerCompat notificationManager =
      NotificationManagerCompat.from(this);

8. Dispatch the extended notification. 

  .. code-block:: java
   
    int notificationId, notification;
    notificationManager.notify(notificationId, notification);
	
.. _demandReceiver:
	
Create a Demand Receiver
^^^^^^^^^^^^^^^^^^^^^^^^^^

When a user makes a demand, the wearable broadcasts an intent that contains the details. The handheld receives the broadcast and takes an appropriate action. The following example defines a BroadcastReceiver for a voice demand that simply logs the results.

  .. code-block:: java
  
    public class DemandIntentReceiver extends BroadcastReceiver{

      @Override
      public void onReceive(Context context, Intent intent) {

        if (intent.getAction().equals(Handheld.ACTION_DEMAND)) {String message =
		  intent.getStringExtra(Handheld.EXTRA_MESSAGE);
          Log.v("MyTag","Extra message from intent = " + message);
          Bundle remoteInput = RemoteInput.getResultsFromIntent(intent);
          CharSequence reply = remoteInput.getCharSequence(Handheld.EXTRA_VOICE_REPLY);
          Log.v("MyTag", "User reply from wearable: " + reply);
          }
        }
      }
	
You also need to modify the manifest file to accept the broadcast. Add a receiver section within the application section.

  .. code-block:: xml
  
    <receiver android:name=".DemandIntentReceiver" android:exported="false">
      <intent-filter>
        <action android:name="com.androidweardocs.first_wearable.ACTION_DEMAND"/>
      </intent-filter>
	</receiver>

Try the First Demand
---------------------
	 
Your new app creates and dispatches a demand notification when it first opens. Both the handheld and wearable receive and post this notification. The wearable adds it to the notification stream, so scroll through the notifications until you see the demand.	 

Scroll the demand notification into full view then swipe to the left. The Reply icon is displayed.

   .. figure:: images/demand-sequence-icon.png
      :scale: 100

Tap the reply icon to display the voice and emoji prompts. Voice is the default, so just start speaking to enter a voice demand.  

   .. figure:: images/demand-sequence-voice.png
      :scale: 100	
	  
In this example the voice demand is *hello handheld*. Android converts your voice input to text and begins sending it to the handheld. You can abort this by taping the blue Sending icon. When Android finishes sending the demand to the handheld, it returns to the Reply prompt. You could then, for example, tap Reply and then Draw Emoji.

   .. figure:: images/demand-sequence-emoji.png
      :scale: 100	
	
In the first prompt you can draw the emoji you want or just click the emoji icon. Either way you get a grid of emoji icons to choose from. Tap the desired emoji and Android sends it to the handheld.

Note: The emoji functionality in not something you need to code; it is all implemented by the Android system software.

The full Example project on github shows how to display demands received by the handheld onscreen.

   .. figure:: images/demand-handheld.png
      :scale: 100	

Example
--------

The full Android Studio project for demands is posted at https://github.com/LarkspurCA/WearableDemand.


