.. _begin:

What About the Sample Apps?
===========================

The Android Wear Preview includes three sample applications that demonstrate possible use cases and UI conventions. 

* ElizaChat
* RecipeAssistant
* WearableNotificationsSample

Open and Launch a Preview App
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This section explains how to open and run any of the preview apps. You need an Android development environment (Android Studio here), your own Android device running the Android Wear Preview App, and an Android Wear emulator.

To launch Android Wear preview samples:

#. If you have not already done so, :ref:`setup`.
#. Start Android Studio.
#. Select **Open Project** from the **File** Menu.
#. Navigate to the desired sample project, select the ``gradle.build`` file, and click **OK**.
#. Verify that the project opens without errors.
#. :ref:`start` on your Android Device.
#. In Android Studio, select **Run** to install the sample app on your Android device. When prompted to choose a device, make sure to select your Android device, not the Android Wear emulator.

   The sample app opens on your Android device. If you hare having problems, make sure  the Android Wear Preview App is running and connected to the Android Wear emulator.

Try Eliza Chat
^^^^^^^^^^^^^^^

The Eliza Chat preview app shows how you might implement an Android Personal Digital Assistant on a wearable device. Eliza is the assistant in this example. All interactions between you and Eliza are done on the wearable device emulator. Eliza's comments are displayed as notifications and your inputs are entered by tapping a reply reply. Normally you would provide voice inputs, but that is not implemented in the preview software. For now, you simply type a what you have to say.

When Eliza Chat opens, the first question from Eliza is displayed on the Android device.

 .. figure:: images/eliza-app1.png
    :scale: 35 %

The Android Wearable emulator also displays the same question. To reply, swipe the screen then select the reply icon. The reply form opens and you can type your response. The following screens show the sequence for entering a reply, which is "Need a sample".

 .. figure:: images/eliza-sequence.png
    
Eliza responds and you can continue with the dialog. The entire session is recorded on the Android device. The following screen shows the transcript for several exchanges with Eliza.

 .. figure:: images/eliza-app2.png
    :scale: 35 %

Try Recipe Assistant
^^^^^^^^^^^^^^^^^^^^^

The Recipe Assistant app is an example of a fully-functional Android application that extends it capabilities to a wearable device. On the Android device, you can scroll through the full recipe and steps. On the wearable device you view the recipe steps one at a time on separate pages.

The app starts on the mobile device with three recipes from which to choose. Select the first one and you get the guacamole recipe.

 .. figure:: images/recipe-app1.png
    :scale: 35 %

 Click Start in the upper right corner, and the recipe is displayed on the wearable emulator.


 .. figure:: images/recipe-sequence.png

Swipe left to move through the steps. For each step you can tap to view more and swipe up or down to move through the content.

Try Wearable Notifications
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Wearable Notifications preview app provides a simple way to try out different combinations of wearable UI notifications and patterns. The onscreen choices closely match the capabilities of the ``WearableNotifications`` class, so this is a good opportunity to learn about available displays. Later we will experiment with the underlying code.

  .. figure:: images/example-notify.png
    :scale: 35 %

The UI is basically a smorgasbord of wearable notification possibilities. The best way to use this app is simply to try out all the choices. You can select a basic notification type (preset), set its priority, and attach action icons.  