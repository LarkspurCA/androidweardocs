Android Wear Suggest
====================


The Suggest context stream is one of the core functions for Android Wear. It consists of a sequence of notifications about timely information, such as incoming messages or upcoming appointments. It can also display useful information about a task at hand, such as preparing a recipe or communicating with a digital assistant.

 .. figure:: images/suggest.png
    :scale: 45
    :align: right

This section explains how to display your own custom notifications on a wearable device. The easiest way is to create a normal Notification object, initialized with your custom message, and send it using the NotificationManager. Such notifications are displayed on both the handheld device and wearable emulator, but the wearable can only display the notification text and a small icon.

 .. figure:: images/suggest-big-picture.png
    :scale: 35
    :align: right

Simple text notifications are only the beginning however. The Android Wear preview provides additional notification templates that improve the user experience for the small screen of a wearable device. To utilize this capability you create a WearableNotification using one of the templates and pass that to the NotificationManager. The big picture example shows one possibility.




