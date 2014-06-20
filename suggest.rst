Android Wear Suggest
====================

By: Michael Hahn, June 2014

The Suggest context stream is one of the core functions for Android Wear. It consists of a sequence of notifications about timely information, such as incoming messages or upcoming appointments. It can also display useful information about a task at hand, such as preparing a recipe or communicating with a digital assistant.

 .. figure:: images/suggest-basic.png
    :scale: 35
    :align: right

This section explains how to display your own custom notifications on a wearable device. The easiest way is to create a normal Notification, initialize it with your custom message, and send it using the NotificationManager. These notifications are displayed on both the handheld device and wearable emulator with a similar level of detail.

Normal text notifications are only the beginning however. Android 4.1 introduced three additional styles: Big Picture, Big Text, and Inbox. The Big Picture example shows how the Big Picture notification style is displayed on a wearable device.

 .. figure:: images/emulator-notify.png
    :scale: 35
    :align: right

Android Wear preview adds even more styles that improve the user experience on the small screen of a wearable device. These styles make it possible to stack, group, and sequence notifications. The email example shows how grouping emails can reduce the number of notifications in the suggest context stream.


First Android Wear Suggest
---------------------------

This section explains how to create your first Android Wear notification and add it to the Suggest context stream on the Android Wear emulator. To keep this simple,  start with a basic Hello World project and then extend it for wearable devices.

Create a Project
^^^^^^^^^^^^^^^^^

1. Launch Android Studio.

2. Select New Project in the Welcome screen.

3. Enter your Application Name and a project directory, then click Next.

4. Select Blank Activity, then click Next.

5. Click Finish.

6. Select the Main_Activity.java tab. This contains the onCreate method, which is a convenient place to add the code for your first notification.

Add the Support Libraries as Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Copy your wearable-preview-support.jar file to the libs folder.

2. Open the build.gradle file in your project and add the following dependencies. The first dependency adds the Android Wear Support Library.

  ::
  
    dependencies {
      compile fileTree(dir: 'libs', include: ['*.jar'])
      compile "com.android.support:support-v4:19.1.+"
    }
  
Write the Notification Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1.  Import the following packages into your Main_Activity:

  .. code-block:: java
   
    import android.preview.support.wearable.notifications.*;
    import android.preview.support.v4.app.NotificationManagerCompat;
    import android.support.v4.app.NotificationCompat;
  
2. Create a normal Android notification using the NotificationCompat.Builder, and set desired properties.

  .. code-block:: java
	  
    NotificationCompat.Builder notificationBuilder =
          new NotificationCompat.Builder(this)
          .setSmallIcon(R.drawable.ic_launcher)
          .setContentTitle("Hello Android Wear")
          .setContentText("First Wearable notification.");
		  
3. Optionally, enhance the normal notification in one of two ways:

  * Apply a release 4.1 style to the normal notification, such as the one used in the Big Picture example (NotificationCompat.BigPictureStyle).


  * Apply Android wearable formats to the normal notification. First create a WearableNotification.builder, passing the NotificationCompat.builder created in Step 2. Then set the desired properties and build the notification.

    .. code-block:: java
  
      Notification notification =
          new WearableNotifications.Builder(notificationBuilder)
          .setMinPriority
          .build();

4. Get an instance of the Notification Manager service.

  .. code-block:: java

    NotificationManagerCompat notificationManager =
        NotificationManagerCompat.from(this);

5. Dispatch the notification. 

  .. code-block:: java
   
    notificationManager.notify(notificationId, notification);
	

 .. figure:: images/hello-wearable.png
    :scale: 35
    :align: right
	
	
The result of this code is a notification with a title and body, that has minimum priority:

Before proceeding to more interesting wearable formats, it is worth learning how to add actions (Demands) to a notification.


