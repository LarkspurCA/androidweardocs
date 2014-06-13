How Does Android Wear Work?
===========================

By: Michael Hahn, June 2014

The easiest way to learn how Android Wear works is to install the Android Wear Preview application on your handheld device and and try it out. For now, we have to use an Android Wear emulator instead of an actual Android Wear device to see how it works. 

The Android Wear Preview application forwards all handheld device notifications to the Android Wear emulator. You can swipe the screen up or down to scroll through the list of notifications, swipe right to delete a notification, or swipe left to select a demand icon. The functionality is fairly basic at this point, but it is a good stepping off point for more interesting examples later.

.. _setup:

Set Up the Development Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To try out the Android Wear Preview, perform the following tasks:

1. Sign up for the `Android Wear Developer Preview <http://developer.android.com/wear/preview/start.html>`_.

  After you receive your confirmation email, you can install the Android Wear Preview application and download the support library and code samples.

2. Install the `Android Studio Preview <http://developer.android.com/sdk/installing/studio.html>`_.

  To try out the preview app, you only need the Android Tools. However it is much easier to install a full IDE than individual components. You can use either Eclipse or Android Studio as your IDE, but I recommend Android Studio because the sample applications are Studio projects.

3. Install the Android Wear system image using the Android SDK Manager. 


   * Android SDK Tools revision 22.6 or later
   * Android Wear ARM EABI v7a System Image
   * Android Support Library
   * Android Support Repository

4. Set up an Android Wear emulator using the Android AVD Manager.

  .. figure:: images/android-wear-square-vm.png
    :scale: 50 %

    Make sure to check the Keyboard option. You will need it later.

5. Start the emulator.

  Initially the screen shows only the time and a disconnected phone icon.

  .. figure:: images/android-wear-square-blank.png
    :scale: 35 %

.. _start:

Start the Android Wear Preview App
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Install the Android Wear Preview app on your handheld device.

  .. figure:: images/apps.png
    :scale: 35 %

2. Enable USB debugging on your handheld device.

  Your handheld device likely disables USB debugging by default, and the option to enable it can be hidden as well. For Samsung Galaxy, you must open Options, select About Phone, and then click Build Number several times. The Options menu then includes Developer options, where you can enable USB debugging.

3. Connect your handheld device to the computer with a USB cable. 

  Accept any warning or security messages displayed on either the handheld device or computer.

4. Verify that the handheld device successfully connected to the computer using the following command:

  ::

     ./adb devices
     List of devices attached 
     a1b2c3d5 device
	
  If a device is not displayed or it displays with an error such as unauthorized, you must resolve that problem before proceeding.
  
  Note: The adb utility is located in the Platform Tools directory of your SDK installation.
  

  
5. Open TCP port 5601. The Android Wear Emulator uses this port to communicate with the handheld device.

  ::

  ./adb -d forward tcp:5601 tcp:5601

6. ON the handheld device, launch the Android Wear Preview app and click Connect.

  The preview app initially displays Connecting... then changes to Connected when it establishes a link to the emulator.

  .. figure:: images/open.png
    :scale: 35 %

  The emulator icon changes to the Google icon, and the most recent notification is displayed. 

  .. figure:: images/emulator-notify.png
    :scale: 35 %

  You can swipe vertically to scroll through other notifications, swipe to the right to delete the current notification, and swipe to the left to view associated actions.

  The displayed notifications are exactly the same as those listed in the action bar pull-down on the handheld device.

  .. figure:: images/phone-notify.png
    :scale: 35 %

You are now up and running with Android Wear, and ready to move on to more interesting examples. The preview includes three example projects you can try next. 