Android Wear Suggest
====================

By Michael Hahn, February 2017

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

You do not have to rely any of these stock UI styles. You can create your own full-screen layout that best suits your custom wearable application. Just keep it simple and be consistent in presentation and usage with other wearable displays. For example, do not try to replicate the grid layout of the handheld device - the wearable is just too small for this approach. Users just glance at their watch, speak simple commands, or tap and swipe the screen.


First Android Wear Suggest
---------------------------

This section explains how to create your first Android Wear notification and add it to the Suggest context stream on an Android wearable, or emulator. The new project wizard in Android Studio creates a project with two main activities, one for the handheld device and another for the wearable. To create your first suggest notification, add code in the handheld activity only, located in the "mobile" branch of the project hierarchy. The preinstalled software on a wearable device or emulator handles the task of receiving and displaying notifications from the handheld.

Create a Project
^^^^^^^^^^^^^^^^^ 

Using Android Studio, :ref:`new_wear_app`. The project name used in the example code is WearableSuggest.

Modify the Handheld Activity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 

Modify the onCreate method of the handheld activity as follows.

 1. Optionally, add Android Wearable features to a Wearable extender object. This example adds the HintShowBackgroundOnly option.

  .. code-block:: java
  
    NotificationCompat.WearableExtender wearableExtender =
      new NotificationCompat.WearableExtender()
          .setHintShowBackgroundOnly(true);

 2. Create a normal Android notification using the NotificationCompat.Builder and set desired properties, including those defined in the WearableExtender.

  .. code-block:: java
	  
    Notification notification =
      new NotificationCompat.Builder(this)
         .setSmallIcon(R.drawable.ic_launcher)
          .setContentTitle("Hello Android Wear")
          .setContentText("First Wearable notification.")
          .extend(wearableExtender)
          .build();

 3. Create a graphic for the notification by copying the ic_launcher.png (hdpi) icon from the mipmap folder to the drawable folder. 
 
 4. Optionally, apply a release 4.1 style to the normal notification, such as the one used in the Big Picture example (NotificationCompat.BigPictureStyle).

 5. Get an instance of the Notification Manager service.

  .. code-block:: java

    NotificationManagerCompat notificationManager =
        NotificationManagerCompat.from(this);

 6. Dispatch the notification. 

  .. code-block:: java
   
    int notificationId = 1;
    notificationManager.notify(notificationId, notification);

This app creates a notification that is sent to the handheld and forwarded to the wearable. If you do not see any notifications on the wearable, enable wearable notifications on the handheld. If necessary on the wearable, scroll through other notifications to view this one. The Hello World displayed on the handheld screen is part of the default layout created by the new project wizard, not the notification.

The basic Suggest functionality on a wearable is handled entirely by its default system software; no custom wearable app is required. 

.. figure:: images/hello-wearable.png
    :scale: 35
    :align: right


Example
--------

The full Android Studio project for this example is posted at https://github.com/LarkspurCA/WearableSuggest.

