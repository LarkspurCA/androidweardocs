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

Android creates and compiles a new project. Sometimes there are compile errors related to missing packages. If so, verify that the project build.gradle script correctly references the maven url.

  .. code-block:: java

    allprojects {
      repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
      }
    }
