Android Wearable Applications
==============================

By Michael Hahn, July 2014

Android wearable devices have an Android operating system, so you can develop  applications for wearables much as you do for handheld devices. Simple handheld apps can potentially run in a standalone mode on the wearable, without being paired with a handheld. More commonly however, wearable apps work in conjunction with a handheld for voice recognition, Internet access, and other services.

Android Studio creates a very simple "Hello World" wearable app when you use the new project wizard. If you select Wearable as an option, the wizard creates both a handheld and a wearable app. Typically you install and launch this app using Android SDK tools.	

Normally you launch a wearable app with a voice command. Either say "OK Google" or touch the screen. At the "Speak Now" prompt, say "start first wearable". The wearable then confirms the voice command and launches the First Wearable app.

 .. figure:: images/hello-sequence.png
      :scale: 70

To manually launch the app, touch the watch face and scroll to the last action, which is "Start...". Then select First_Wearable from the list of installed apps. 

 .. figure:: images/wear-menu.png
      :scale: 35 

So far, not a single line of new code was necessary; the Android Studio new project wizard created it all. The upcoming sections show how to add more interesting content and actions.