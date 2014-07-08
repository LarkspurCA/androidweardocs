Android Wear Demand
===================

By Michael Hahn, July 2014

The Demand context is one of the core functions for Android Wear. A demand is displayed as a large icon, typically when you swipe a displayed suggestion. You tap on an icon to perform the desired demand.


 .. figure:: images/demand-2.png
    :scale: 40
    :align: right

The Android Wear app demonstrates this core function for messages. When new email arrives, you swipe to the left to scroll through the demand icons, such as open, reply, and archive. You can any custom icon as well. This section explains how to code your own wearable demand for voice input.

First Android Wear Demand
--------------------------

Create the main activity
^^^^^^^^^^^^^^^^^^^^^^^^^

Android Wear preview implements demands using Intents.

1. If you have not already done so, :ref:`newapp` and :ref:`dependencies`.

2. Create an Intent that defines the action that the handheld device should take in response to a wearable action (demand). 

   The Intent has the following parameters:

   > The context of the activity, service, or broadcast receiver that is the intent target.
   
   > The name of the class that is the intent target.
   
   > An extra consisting of a demand id and a value that identify the intent.
   
   The following example shows how to create the intent for a reply.

  .. code-block:: java

    Intent demandIntent = new Intent(this, DemandIntentReceiver.class)
      .putExtra(EXTRA_MESSAGE, “Reply selected.")
      .setAction(ACTION_DEMAND);

3. Create a PendingIntent for the notification. 

  A PendingIntent wraps the intent to grant the privileges needed for it to execute in your application. It contains the context of your activity, service, or broadcast receiver and the Intent object itself. 

  The following example creates a pending intent for a broadcast receiver.

  .. code-block:: java

    PendingIntent demandPendingIntent =
        PendingIntent.getActivity(this, 0, demandIntent, 0);

4. Optionally, create a remoteInput object to hold a voice reply from the wearable device. A voice request or response is a common action for a user because of the small size of the wearable UI.

  .. code-block:: java
  
    String replyLabel = getResources().getString(R.string.app_name);
    RemoteInput remoteInput = new RemoteInput.Builder(EXTRA_VOICE_REPLY)
      .setLabel(replyLabel)
      .build();
	  
5. Create a wearable action.

  The following example creates an action for a notification that uses a standard reply icon and label, and adds the pending intent and remote input from previous steps.

  .. code-block:: java
  
    NotificationCompat.Action replyAction =
      new NotificationCompat.Action.Builder(R.drawable.ic_reply_icon,
        getString(R.string.reply_label), demandPendingIntent)
        .addRemoteInput(remoteInput)
        .build(); 

6. Create a WearableExtender for the a notification and add the wearable reply action.

  .. code-block:: java
  
    NotificationCompat.WearableExtender wearableExtender =
      new NotificationCompat.WearableExtender()
	  .addAction(replyAction);

7. Create a notification that includes the PendingIntent. The following example creates a normal notification that includes an an open demand for the content.

  .. code-block:: java

     Notification notify =
       new NotificationCompat.Builder(this)
         .setContentTitle("Hello Wearable!")
         .setContentText("First Wearable notification.")
         .setSmallIcon(R.drawable.ic_launcher)
         .extend(wearableExtender)
         .build();
  
  8. Get an instance of the Notification Manager service.

    .. code-block:: java

      NotificationManagerCompat notificationManager =
        NotificationManagerCompat.from(this);

  9. Dispatch the notification. 

    .. code-block:: java
   
      notificationManager.notify(notificationId, notification);
	
Create a Notification Receiver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following example receives the wearable action and logs the contents as debug messages.

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
	 
The sample code first displays the notification text, which is simply a title and body. Swiping to the left displays the reply icon with the reply label.	 

   .. figure:: images/demand-sequence-icon.png
      :scale: 100

Selecting the reply icon displays the voice prompt, and then the confirmation after you speak (or type on the emulator).	 

   .. figure:: images/demand-sequence-voice.png
      :scale: 100	
	
  The example DemandIntentReceiver receives the broadcasted intent and extracts the voice input. Log messages show the value of the extra text within the intent and the voice string from the remote input. 
  
  .. code-block:: text
	
	V/MyTag﹕ Extra message from intent = Reply icon selected.
	V/MyTag﹕ User reply from wearable: Hello handheld!	