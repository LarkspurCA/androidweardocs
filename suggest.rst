Android Wear Suggest
====================

By: Michael Hahn, June 2014

The Suggest context stream is one of the core functions for Android Wear. It consists of a sequence of notifications about timely information, such as incoming messages or upcoming appointments. It can also display useful information about a task at hand, such as preparing a recipe or communicating with a digital assistant.

 .. figure:: images/suggest-basic.png
    :scale: 35
    :align: right

This section explains how to display your own custom notifications on a wearable device. The easiest way is to create a normal Notification, initialize it with your custom message, and send it using the NotificationManager. These notifications are displayed on both the handheld device and wearable emulator with a similar level of detail.

 .. figure:: images/emulator-notify.png
    :scale: 35
    :align: right

Normal text notifications are only the beginning however. Android 4.1 introduced three additional styles: Big Picture, Big Text, and Inbox. The email inbox reduces the number of notifications in the suggest context stream.

 .. figure:: images/emulator-group.png
    :scale: 35
    :align: right

Android Wear preview adds even more styles that improve the user experience on the small screen of a wearable device. These styles make it possible to group or add pages to notifications. The Notifications Bundle example shows how you can group two notifications.


First Android Wear Suggest
---------------------------

This section explains how to create your first Android Wear notification and add it to the Suggest context stream on the Android Wear emulator. To keep this simple,  start with a basic Hello World project and then extend it for wearable devices.

.. _newapp:

Create a Project
^^^^^^^^^^^^^^^^^

1. Launch Android Studio (Version 0.8 or later).

2. Select New Project in the Welcome screen.

3. In the New Project dialog, enter your Application Name, Domain, and a project directory. Click Next.

4. In the Form Factors dialog, select Phone and Wear for the handheld and wearable devices, respectively. Keep the default SDKs, and then click Next.

5. In the Mobile dialog, select Blank Activity. Click Next.

6. In the Option dialog, enter the Activity Name, Layout Name, and Title for the handheld activity. Then click Next.

7. In the Wear dialog, Select Blank Wear Activity. Then click Next.

8.  In the Options dialog, enter an Activity Name, Layout Name, Round Layout Name, and Rectangular Layout Name for the wearable activity. Click Finish.


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
          setGroup("First_Wearable", WearableNotifications.GROUP_ORDER_SUMMARY)
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
	
	
The result of this code is a notification with a title and body, that is the summary notification for the First_Wearable group:

Before proceeding to more interesting wearable formats, it is worth learning how to add actions (Demands) to a notification.


