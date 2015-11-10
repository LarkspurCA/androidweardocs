Always-On Wearable
====================

By Michael Hahn, November 2015

Some Wearable apps are most useful when they remain in the foreground for an extended period of time. For example, a golf app needs to be available for a round of golf and a travel app is needed for the whole trip. At the same time, such apps need to conserve power by changing to a low-power mode when inactive. The Always-On feature makes this all possible. Your wear app becomes the primary activity, rather than the watchface, and it conserves power by changing to the ambient mode during periods of inactivity.  

An always-on app has two modes of operation:

* Interactive: A bright full-color screen where a user views information and enters demands

* Ambient: A power-saving screen with minimal color and graphics that displays information only

Always on always keeps your app in the foreground in both interactive and ambient modes, so it basically becomes the home screen of the watch.

Note: This is a new and unreleased feature, except to developers.
 
First Always On App
--------------------
 
First :ref:`new_wear_app`. The new project has two applications, one for the handheld device and another for the wearable. specifying SDK 22 as the target SDK. The new project wizard in Android Studio creates a project with two main activities, one for the handheld device and another for the wearable. 

The default theme displays white text on a black background, which is a typical ambient mode display. To try out the always-on feature, modify the wear layout to specify black text on a cyan background, and have the view fill the parent:

  .. code-block:: html

    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:text="@string/hello_square"
        android:textColor="@color/activeText"
        android:background="@color/activeBackground"
        />
 
Define the colors accordingly in a new colors.xml file:

  .. code-block:: html

    <resources>
      <item name="activeBackground" type="color">#FF00FFFF</item>
      <item name="activeText" type="color">#FF000000</item>
    </resources>
	 
Verify the SDK Versions
^^^^^^^^^^^^^^^^^^^^^^^^

Verify the versions of the SDK and build tool in the build.gradle (Module:mobile) file in Gradle Scripts folder.

* compileSdkVersion: 23
* minSdkVersion: 22
* targetSdkVersion: 22
* buildToolsVersion: 23.0.0

  .. code-block:: java
  
    android {
      compileSdkVersion 23
      buildToolsVersion "23.0.0"

      defaultConfig {
          applicationId "com.androidweardocs.alwayson"
          minSdkVersion 21
          targetSdkVersion 22
	  ...
	}
	 
Add Build Dependencies
^^^^^^^^^^^^^^^^^^^^^^^
	 
Add dependencies to the build.gradle (Module:mobile) file, if missing.

  .. code-block:: java

    dependencies {
      compile 'com.google.android.support:wearable:1.2.0'
      provided 'com.google.android.wearable:wearable:1.0.0'

      compile 'com.google.android.gms:play-services-wearable:7.8.0'
    }
	
Add the WAKE_LOCK Permission
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Add the WAKE_LOCK permission at the top level of the manifest. To run this app on a pre-22 APK, you set the wearable library requirement to false in the application section of the manifest.

  .. code-block:: java
  
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.androidweardocs.alwayson" >
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <application
	    <uses-library android:name="com.google.android.wearable" android:required="false" />
        ,,,
      </application>
    </manifest>


Enable the Always On Feature
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To enable the Always On feature, extend your main activity from WearableActivity, and invoke setAmbientEnabled in the onCreate method.

  .. code-block:: java
  
    public class AlwaysOn extends WearableActivity {
      @Override
      public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_always_on);
        setAmbientEnabled();
        ...
      }
    }

Handle Ambient Mode Transitions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the watch enters ambient mode you change the display to a minimum power configuration. Informational content is still displayed, but with white or grey content on a black background. On return to the interactive mode, you reverse the changes to display full colors and brightness. You handle entry into ambient mode by implementing onEnterAmbient, and return to interactive mode by implementing onExitAmbient. The following example changes the background to black ant the text to white with antialias disabled.

  .. code-block:: java

    @Override
    public void onEnterAmbient(Bundle ambientDetails) {
        super.onEnterAmbient(ambientDetails);
        mTextView.setBackgroundColor(Color.BLACK);
        mTextView.getPaint().setAntiAlias(false);
        mTextView.setTextColor(Color.WHITE);
    }
	
On return to interactive mode reverse all your ambient mode changes.

  .. code-block:: java

    @Override
    public void onExitAmbient(){
        mTextView.setBackgroundColor(Color.CYAN);
        mTextView.getPaint().setAntiAlias(true);
        mTextView.setTextColor(Color.BLACK);
        super.onExitAmbient();
    }

Update Content During Ambient Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the ambient mode you can update the display with current content by implementing onUpdateAmbient. This method is called every 60 seconds. For more frequent updates you can respond to incoming data, use the alarm manager, or implement a Handler. To benefit from the power-saving capabilities of the ambient mode, keep the update interval greater than 10 sec.

This simple example appends a number to the hello message, which it increments every 60 seconds.

  .. code-block:: java
  
    @Override
    public void onUpdateAmbient() {
        super.onUpdateAmbient();
        mTextView.setText("Hello Square World! " + i.toString());
        i++;
    }
   
Try the App
--------------

Verify the app on an emulator or device. You need Lollipop 5.1 as a minimum to use Always On. Android Studio starts the app when you select Run -> Wear from the Run menu. The interactive screen is displayed showing the Hello message in black text on a cyan background. 

  .. figure:: images/always-on.png
     :scale: 50
	 
When the watch enters the power-saving ambient mode, the text color changes to white and the background to black. Had there been any buttons or controls, they would have been hidden.

  .. figure:: images/ambient.png
     :scale: 50
	 
After one minute the ambient display is modified, in this example to include a number.

  .. figure:: images/ambient-update.png
     :scale: 50

To stop this app, tap screen for to enter the interactive mode and press watch button.

Example
--------

The working example for this section is at https://github.com/LarkspurCA/WearableAlwaysOn.

