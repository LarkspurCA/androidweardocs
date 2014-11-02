Wearable Application Launch
=============================

By Michael Hahn, November 2014

Android wearable devices have an Android operating system, so you can develop  applications for wearables much as you do for handheld devices. Simple handheld apps can potentially run in a standalone mode on the wearable, without being paired with a handheld. More commonly however, wearable apps work in conjunction with a handheld for voice recognition, Internet access, and other services.

Android Studio creates a very simple "Hello World" wearable app when you use the new project wizard. If you select Wearable as an option, the wizard creates both a handheld and a wearable app. Typically you install and launch this app using Android SDK tools.

Voice Activation
------------------

An easy way to launch a wearable app is with a voice command. Either say "OK Google" or touch the screen. At the "Speak Now" prompt tell Google to start your app, for example  "start first wearable". The wearable then confirms your voice command and launches the the app.

 .. figure:: images/hello-sequence.png
      :scale: 70
	  
Menu Activation
-----------------

To manually launch the app, touch the watch face and scroll to the last action, which is "Start...". Then select your app from the list of installed apps, in this case First_Wearable. 

 .. figure:: images/wear-menu.png
      :scale: 35 

So far, not a single line of new code was necessary; the Android Studio new project wizard created it all. The upcoming sections show how to add more interesting content and actions.

Handheld Activation 
----------------------------------------

A great way to start your wearable app is from a notification on the wearable. This is useful when you already have a handheld app and want to extend features to a wearable. Users simply swipe the notification and tap the Open icon to launch the wearable portion of your app. They return to the notification stream when they dismiss the wearable app.

Creating a notification and demand that starts your wearable app is somewhat different than that described in :ref:`demand`. In this case the notification includes a demand that invokes an action in the wearable itself, rather than an action back in the handheld. So rather than dispatching a notification from the handheld, we have the handheld ask the wearable to perform that task. 

Prerequisite
^^^^^^^^^^^^^^
This procedure relies on communication through the data layer. Communication through the data layer requires setup of Google Play Services for wearables in both the handheld and wearable devices (see :ref:`datalayer`).

Post a Notification
^^^^^^^^^^^^^^^^^^^^^

1. In the handheld, send the title and body of a notification to the wearable device over the data layer. The following excerpt shows how you could implement this task in the onConnected method of the GooglePlayServicesClient. The SendToDataLayerThread class is defined in :ref:`datalayer`.

  .. code-block:: java
  
    @Override
    public void onConnected(Bundle dataBundle) {
      // Data layer connection code
	  ...
      // Send DataMap object to wearable
      String WEARABLE_START_PATH = "/wearable_start";
      DataMap notifyWearable = new DataMap();
      notifyWearable.putString("title", "Title");
      notifyWearable.putString("body", "Start now?");
      // Send to data layer
      new SendToDataLayerThread(WEARABLE_START_PATH, notifyWearable).start();
    }

2. In the wearable, receive the title and body in the WearableListenerService class. The following excerpt shows a sample override for the onDataChanged method.

  .. code-block:: java
  
    String WEARABLE_START_PATH = "/wearable_start";
  
    @Override
    public void onDataChanged(DataEventBuffer dataEvents) {
      DataMap dataMap;
      for (DataEvent event : dataEvents) {
          // Check the event type
          if (event.getType() == DataEvent.TYPE_CHANGED) {
              String path = event.getDataItem().getUri().getPath();
              //Verify the data path, get the DataMap, and send local notification
              if (path.equals(WEARABLE_START_PATH)) {
                  // Create and send a local notification inviting the user to start the wearable app
                  dataMap = DataMapItem.fromDataItem(event.getDataItem()).getDataMap();
                  sendLocalNotification(dataMap.getString("title"), dataMap.getString("body"));
               }
            }
        }
    }
  

3. In the wearable, implement the procedure that constructs and posts a notification.

  .. code-block:: java
  
    private void sendLocalNotification(String title, String body) {
      int notificationId = 001;
      
      // Create a pending intent that starts this wearable app
      Intent startIntent = new Intent(this, HoleActivity.class).setAction(Intent.ACTION_MAIN);
      PendingIntent startPendingIntent = PendingIntent.getActivity(this, 0, startIntent, 0);
      
      Notification notify = new NotificationCompat.Builder(this)
        .setContentTitle(title)
        .setContentText(body)
        .setSmallIcon(R.drawable.ic_launcher)
        .setContentIntent(startPendingIntent)
        .build();
        
      NotificationManagerCompat notificationManager = NotificationManagerCompat.from(this);
      notificationManager.notify(notificationId, notify);
    }

The wearable display now includes a notification inviting the user to launch the wearable app. A swipe to the left displays the launcher icon, which the user clicks to launch the app.
