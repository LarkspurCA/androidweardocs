How Does Android Wear Work?
===========================

The easiest way to learn how Android Wear works is to install the Android Wear Preview app on your Android phone and and try it out. For now, we have to use an Android Wear emulator instead of an actual Android Wear device to see how it works. 

The Android Wear Preview app displays all phone notifications on the emulator. You can swipe the screen up or down to scroll through the list of notifications, swipe right to delete a notification, or swipe left to select a demand icon. The functionality is fairly basic at this point, but it is a good stepping off point for more interesting examples later.

To try out the Android Wear Preview, perform the following tasks:

* Sign up for the `Android Wear Developer Preview <http://developer.android.com/wear/preview/start.html>`_.

  After you receive your confirmation email, you can install the Android Wear Preview Application and download the support library and code samples.

* Install the `Android Studio Preview <http://developer.android.com/sdk/installing/studio.html>`_.

  To try out the preview app, you only need the Android Tools. However it is much easier to install a full IDE than individual components. You can use either Eclipse or Android Studio as your IDE, but I recommend Android Studio because the sample applications are Studio projects.

* Install the Android Wear system image using the Android SDK Manager. 


   * Android SDK Tools revision 22.6 or later
   * Android Wear ARM EABI v7a System Image
   * Android Support Library
   * Android Support Repository

* Set up an Android Wear emulator using the Android AVD Manager.

  .. figure:: images/android-wear-square-vm.png
    :scale: 35 %

* Start the emulator.

  Initially the screen shows only the time and a disconnected phone icon.

  .. figure:: images/android-wear-square-blank.png
    :scale: 35 %

* Install the Android Wear Preview app on your smartphone.

  .. figure:: images/apps.png
    :scale: 35 %

* Connect your smartphone to the computer with a USB cable.

  The smartphone and emulator communicate over TCP port 5601, so enter the following command each time you connect the phone to the computer:

  ``adb -d forward tcp:5601 tcp:5601``

* Launch the Android Wear Preview app and click Connect to establish a link to the emulator.

  The preview app displays Connecting... and then Connected.

  .. figure:: images/open.png
    :scale: 35 %

  The emulator icon changes to the Google icon, and the most recent notification is displayed. 

  .. figure:: images/emulator-notify.png
    :scale: 35 %

  You can swipe vertically to scroll through other notifications, swipe to the right to delete the current notification, and sweep to the left to view notification actions.

  These notifications are exactly the same as those listed in the action bar pull-down on the phone.

  .. figure:: images/phone-notify.png
    :scale: 35 %

You are now up and running with Android Wear, and ready to move on to more interesting examples. The preview includes three example projects you can try next. 