Android Wear Suggest
====================

By: Michael Hahn, July 2014

The Suggest context stream is one of the core functions for Android Wear. It consists of a sequence of notifications about timely information, such as incoming messages or upcoming appointments. It can also display useful information about a task at hand, such as preparing a recipe or communicating with a digital assistant.

 .. figure:: images/suggest-notify.png
    :scale: 35
    :align: right

This section explains how to display your own custom notifications on a wearable device. The easiest way is to create a normal Notification, initialize it with your custom message, and send it using the NotificationManager. These notifications are displayed on both the handheld device and wearable emulator with a similar level of detail.

 .. figure:: images/suggest-big-picture.png
    :scale: 35
    :align: right

Normal text notifications are only the beginning however. Android 4.1 introduced three additional styles: Big Picture, Big Text, and Inbox. The big picture example demonstrates one way to add a contextual image to the notification.

 .. figure:: images/suggest-email.png
    :scale: 35
    :align: right

Android Wear adds even more styles that improve the user experience on the small screen of a wearable device. These styles make it possible to group or add pages to notifications. The email example shows how messages are grouped to reduce the number of notification delivered to a wearable.

You do not have to rely any of these stock UI styles. You can create your own full-screen layout that best suits your custom application. Just keep it simple and be consistent in presentation and usage with other wearable apps. For example, do not try to replicate the grid layout of the handheld device - the wearable is just too small for this approach. Users just glance at their watch, speak simple commands, or tap and swipe the screen.


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

Add Dependencies for the Wearable Package
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Add a build dependency for the wearable support package to the build.gradle file. For Android Studio Beta, the correct file is within the mobile folder.

  .. code-block:: java
  
    dependencies {
      compile fileTree(dir: 'libs', include: ['*.jar'])
      compile "com.android.support:support-v4:20.0.+" 
    }


Write the Notification Code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1.  Import the following packages into the Activity class for mobile:

  .. code-block:: java
   
    import android.support.v4.app.NotificationManagerCompat;
    import android.support.v4.app.NotificationCompat;
  
2. Add Android Wearable functionality to a WearableExtender object, for example ShowBackgroundOnly.

  .. code-block:: java
  
    NotificationCompat.WearableExtender wearableExtender =
      new NotificationCompat.WearableExtender()
          .setHintShowBackgroundOnly(true);

3. Create a normal Android notification using the NotificationCompat.Builder and set desired properties, including those defined in the WearableExtender.

  .. code-block:: java
	  
    NotificationCompat.Builder notificationBuilder =
      new NotificationCompat.Builder(this)
         .setSmallIcon(R.drawable.ic_launcher)
          .setContentTitle("Hello Android Wear")
          .setContentText("First Wearable notification.")
		  .extend(wearableExtender)
		  .build();
		  
3. Optionally, apply a release 4.1 style to the normal notification, such as the one used in the Big Picture example (NotificationCompat.BigPictureStyle).

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
	
	
The result of this example code is a notification with a title and body.

Before proceeding to more interesting wearable formats, it is worth learning how to add actions (Demands) to a notification.


