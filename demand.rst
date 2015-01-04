.. _demand:

Android Wear Demand
===================

By Michael Hahn, July 2014

The Demand context is one of the core functions for Android Wear. A demand is displayed as a large icon, typically when you swipe a displayed suggestion (notification). You tap on an icon to perform the desired demand.


 .. figure:: images/demand-2.png
    :scale: 40
    :align: right

The Android Wear app demonstrates this core function for messages. When new email arrives, you swipe to the left to scroll through the demand icons, such as open, reply, and archive. You can create custom icons for your app as well. This section explains how to code your own wearable demand that handles a voice reply.

First Android Wear Demand
--------------------------

If you have not already done so, :ref:`newapp`. The new project wizard in Android Studio beta creates a project with two main activities, one for the handheld device and another for the wearable. To create your first demand, add code in the handheld activity only, located in the "mobile" branch of the project hierarchy. The software preinstalled on a wearable device or emulator handles the task of receiving and displaying notifications on the wearable.

.. _dependencies:

Add Build Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^

Add a build dependency for the wearable support package to the build.gradle (Module:mobile) file in Gradle Scripts folder, if necessary.  

  .. code-block:: java
  
    dependencies {
      compile fileTree(dir: 'libs', include: ['*.jar'])
      compile 'com.google.android.support:wearable:+' 
    }

Modify the Handheld Activity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Your handheld activity creates a notification that includes a wearable action (demand). This action includes an intent that the wearable invokes when a user selects the demand, along with optional data such as a voice response from the user. You build the notification with a hierarchy of objects, intent -> pending intent -> notification action -> wearable extender, and finally the notification itself. 

1. Create an Intent that defines the action that the handheld device should take in response to a wearable action (demand). 

   The Intent has the following parameters:

   > The context of the activity, service, or broadcast receiver that is the intent target.
   
   > The name of the class that is the intent target.
   
   > An extra that provides intent details.
   
   > The type of action requested.
   
   The following example shows how to create the intent for a reply.

  .. code-block:: java
  
    String ACTION_DEMAND = "com.androidweardocs.ACTION_DEMAND";
    String EXTRA_MESSAGE = "com.androidweardocs.EXTRA_MESSAGE";

    Intent demandIntent = new Intent(this, DemandIntentReceiver.class)
      .putExtra(EXTRA_MESSAGE, “Reply selected.")
      .setAction(ACTION_DEMAND);

2. Create a PendingIntent to include in the notification. 

  A PendingIntent wraps the intent to grant the privileges needed for it to execute in your application. It contains the context of your activity, service, or broadcast receiver and the Intent object itself. 

  .. code-block:: java

    PendingIntent demandPendingIntent =
        PendingIntent.getBroadcast(this, 0, demandIntent, 0); // as we are registering Broadcast Receiver, we have to use getBroadcast method if it is Activity than we have to use getActivity

3. Create a RemoteInput object to hold a voice reply from the wearable device. A voice request or response is a common action for a user because of the small size of the wearable UI.

  .. code-block:: java
  
    String replyLabel = getResources().getString(R.string.app_name);
    RemoteInput remoteInput = new RemoteInput.Builder(EXTRA_VOICE_REPLY)
      .setLabel(replyLabel)
      .build();
	  
4. Create a wearable action.

  The following example creates an action for the notification that uses a standard reply icon and label, adds the pending intent, and the remote input for voice.

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

6. Create a notification and extended it to include the wearable extender just created. The following example creates a notification that includes a reply action (demand).

  .. code-block:: java

     Notification notification =
       new NotificationCompat.Builder(this)
         .setContentTitle("Hello Wearable!")
         .setContentText("First Wearable notification.")
         .setSmallIcon(R.drawable.ic_launcher)
         .extend(wearableExtender)
         .build();
  
7. Get an instance of the Notification Manager service.

  .. code-block:: java

    NotificationManagerCompat notificationManager =
      NotificationManagerCompat.from(this);

8. Dispatch the notification. 

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
  
    MyTag: Extra message from intent = Reply icon selected.
    MyTag: User reply from wearable: hello handheld


	
