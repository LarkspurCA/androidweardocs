How Does Android Wear Work?
===========================

By Michael Hahn, December 2014

The easiest way to learn how Android Wear works is to install the Android Wear companion app on your handheld device, pair with an Android watch or emulator and try out a few examples.  This site focuses on wearable app development, so it starts by explaining how to set up a development environment that you can use to build and run sample code provided by Android.

.. _setup:

Set Up the Development Environment
-----------------------------------

To try out Android Wear in a development environment, perform the following tasks:

1. Install `Android Studio <http://developer.android.com/sdk/installing/studio.html>`_.

2. Install the Android Wear system image and support packages using the Android SDK Manager. 

  * Android 5.0 (API 21)
  * Android SDK Tools 24 or later
  * Android Support Library 21 or later
  * Android Support Repository 11 or later
  * Android Wear ARM EABI v7a System Image 21 or later (for wearable emulator)

.. _start:

Set Up Your Handheld Device
------------------------------

1. Install the Android Wear companion app on your handheld device.

  .. figure:: images/apps.png
    :scale: 25 %
	

2.  Start the Android Wear companion app. 

    The first time you launch the app, an onscreen message reminds you that Android Wear is not a notification listener.  Follow the onscreen instructions.


3. Enable USB debugging on your handheld device.

  Your handheld device disables USB debugging by default, and the option to enable it can be hidden as well. For Samsung Galaxy, you must open Options, select About Phone, and then click Build Number seven times. This adds Developer Options to the Options menu, where you can enable USB debugging.

4. Connect your handheld device to the computer with a USB cable. 

  Accept any warning or security messages displayed on either the handheld device or computer.

5. Verify that the handheld device successfully connected to the computer using the following command:

  ::

     adb devices
     List of devices attached 
     a1b2c3d5 device
	
  If a device is not displayed or it displays with an error such as unauthorized, you must resolve that problem before proceeding.
  
  Note: The adb executable is located in the Platform Tools directory of your Android SDK. Add it to your path if necessary.

  
Set Up Your Wearable
---------------------
  
You can try the sample apps using either an Android device or emulator. A wearable initially displays the default watchface, which varies by device. An emulator generally defaults to a digital watch face on a sky background. Notifications are displayed as they arrive at the bottom of the display. The following example shows that it is early in the morning and you have not exercised yet.

  .. figure:: images/emulator-idle.png
    :scale: 35 %

  You can swipe vertically to scroll through other notifications, swipe to the right to delete the current notification, and swipe to the left to view any associated actions. The notifications displayed on the wearable are the same as those listed in the handheld, in the action bar pull down.
  
Android Emulator Setup
^^^^^^^^^^^^^^^^^^^^^^^

1. Start the Android AVD Manager.

  ::

     android avd
	 
  Note: The adb executable is located in the Tools directory of your Android SDK. Add it to your path if necessary.
  
2. Click **Create** to define a new Android Wear emulator.

    .. figure:: images/android-wear-square-vm.png
      :scale: 50 %

      Make sure to check the Keyboard option. You might need it later.

3. Click **OK** to save your changes.

4. Click **Start** to launch the the emulator.

    Initially the emulator screen shows the time and two icons on a cloud background.

    .. figure:: images/android-wear-square-blank.png
      :scale: 35 %

Start an Emulator Debug Session
********************************

1. Enter the following command in a command window.

  ::

    adb -d forward tcp:5601 tcp:5601


2. In the Android Wear companion app, choose **Pair with emulator**.

  When the Android Wear companion app successfully pairs with the emulator, the action bar displays Emulator Connected.

  .. figure:: images/open.png
    :scale: 25 %

  On the emulator, the cloud icon disappears, and notifications are displayed as they are received.
  
Wearable Device Setup
^^^^^^^^^^^^^^^^^^^^^^^^^

1. Pair a wearable device with your handheld using the Android Wear companion app. 

   When you first pear with your wearable, the Android Wear companion app provides a short tutorial that introduces the Wearable UI and basic functionality. From the main UI where you can then change the watchface, enable voice actions, and browse suggested apps.
  
2. Enable bluetooth debugging on the wearable. 
  
   Your wearable device disables USB debugging by default, and the option to enable it can be hidden as well. You must open Settings, select About, and then click Build Number seven times. The Settings menu then includes Developer options, where you can enable debugging over bluetooth.  
   
Start a Device Debug Session
*****************************


1. On the handheld, open the Android Wear companion app.

2. Tap the Settings icon. The Settings dialog is displayed.

3. Enable Debugging over Bluetooth. The following is displayed initially:

   :: 
   
     Host: disconnected
     Target: connected

4. Enter the following command on your computer.

   ::
   
     adb forward tcp:4444 localabstract:/adb-hub
     adb connect localhost:4444
   
5. The companion app changes  to the following:

   ::
   
     Host: connected
     Target: connected

 
 
Next Steps
-----------

You are now up and running with Android Wear, and ready to move on to your first wearable app. Initially, you write an app that can display notifications and receive user inputs from a wearable device, but runs code on the handheld device only. Later you can develop more powerful software that runs Android code on the wearable device as well.