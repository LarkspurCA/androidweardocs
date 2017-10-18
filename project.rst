.. _new_wear_app:

Create an Android Studio Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You create a new wearable project using the Android Studio new project wizard. This procedure describes an project that implements both a handheld and wearable app. Android Wear 2.0 watches (OS 7.1 or later) do not require a paired handheld app. However, the example code in some sections of this guide do utilize a handheld device.


1. Launch Android Studio.

2. Select **Start a New Android Studio Project** in the Welcome screen.

3. In the new project dialog, enter your Application Name, Company Domain, and a project Location. Click **Next**.

4. In the Target Android Devices dialog, select **Wear** and **Phone and Tablet**. 

5. Accept the default **Minimum SDK** values and Click **Next**. 
 
6. In the Add an Activity to Mobile dialog, select **Empty Activity**. Click **Next**.

7. In the Customize Activity dialog enter an Activity Name for the handheld. The example code uses the name **HandheldActivity**.  Accept default values for the other fields and click **Next**.

8. In the Add an Activity to Wear dialog, select **Always On Wear Activity**. Then click **Next**.

9. In the Customize Activity dialog enter an Activity Name for the wearable. The example code uses the name **WearActivity**. Accept default values for the other fields and click **Finish**.

Android Studio creates and compiles the new project.

Verify the New Project Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Verify that the build.gradle(project) script correctly references the maven url.

  .. code-block:: html

    allprojects {
      repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
      }
    }

2. Verify that the build.gradle(wear) script has the following dependencies. Use later versions, as needed.

  .. code-block:: html

    compile 'com.google.android.support:wearable:2.0.5'
    compile 'com.google.android.gms:play-services-wearable:11.0.4'
    provided 'com.google.android.wearable:wearable:2.0.5'

    compile 'com.android.support:wear:26.0.0'

      Note: This last dependency provides the most current wearable packages.

    
3. For stand-alone Android Wear 2 apps, add the following meta-data to the <Application> section of the wear manifest file.

  .. code-block:: html

     <application>
       ...
       <meta-data
        android:name="com.google.android.wearable.standalone"
        android:value="true" />
       ...
    </application>
