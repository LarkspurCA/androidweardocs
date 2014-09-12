Wearable Applications
==============================

By Michael Hahn, August 2014

Android wearable devices have an Android operating system, so you can develop  applications for wearables much as you do for handheld devices. Simple handheld apps can potentially run in a standalone mode on the wearable, without being paired with a handheld. More commonly however, wearable apps work in conjunction with a handheld for voice recognition, Internet access, and other services.

Android Studio creates a very simple "Hello World" wearable app when you use the new project wizard. If you select Wearable as an option, the wizard creates both a handheld and a wearable app. Typically you install and launch this app using Android SDK tools.

Voice Activation
------------------	

An easy way to launch a wearable app is with a voice command. Either say "OK Google" or touch the screen. At the "Speak Now" prompt tell Google to start your app, for example  "start first wearable". The wearable then confirms your voice command and launches the the app.

 .. figure:: images/hello-sequence.png
      :scale: 70
	  
Menu Activation
-----------------	  

To manually launch the app, touch the watch face and scroll to the last action, which is "Start...". Then select your app from the list of installed apps, in this case First_Wearable. 

 .. figure:: images/wear-menu.png
      :scale: 35 

So far, not a single line of new code was necessary; the Android Studio new project wizard created it all. The upcoming sections show how to add more interesting content and actions.