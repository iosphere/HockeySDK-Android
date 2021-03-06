This README describes on how to integrate HockeyApp into you Android apps. The client allows testers to update your app to another beta version right from within the application. It will notify the tester if a new update is available. It also allows to send crash reports right from within the application. If a crash has occured, it will ask the tester on the next resume of the main activity whether he wants to send information about the crash to the server. The latter feature works for both beta apps and apps for the Android Market.

## Requirements

The SDK runs on devices with Android 2.1 or higher, but you need Android SDK 3.0 (Level 11) or higher to build the library.

## Integration Into Your Own App

### Download the Library

* Download the latest release from [here](https://github.com/bitstadium/HockeySDK-Android/downloads). We also offer [nightly builds](#nightly) from our develop branch.
* Unzip the file.
* Copy the file libs/HockeySDK.jar to the libs folder of your Android project.
* If you use ADT 16 or older, then add the .jar file to your classpath. With ADT 17 or newer, this is done automatically.

### Change Code

* Open your AndroidManifest.xml.
* Add the following line as a child element of application: <pre>&lt;activity android:name="net.hockeyapp.android.UpdateActivity" /></pre>
* If you want to do beta distribution and crash reporting, add the following lines as child elements of your manifest: <pre>&lt;uses-permission android:name="android.permission.INTERNET" />
&lt;uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /></pre>
* If you want to do only crash reporting (e.g. for apps in the Android Market), add the following line as child elements of your manifest: <pre>&lt;uses-permission android:name="android.permission.INTERNET" />
* Save the AndroidManifest.xml.
* Open your main activity or the activity in which you want to integrate the update process and crash reporting.
* Add the following lines:

<pre>import net.hockeyapp.android.CrashManager;
import net.hockeyapp.android.UpdateManager;
               
public class YourActivity extends Activity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    // Your own code to create the view
    // ...
    
    checkForUpdates();
  }

  @Override
  public void onResume() {
    super.onResume();
    checkForCrashes();
  }

  private void checkForCrashes() {
    CrashManager.register(this, APP_ID);
  }

  private void checkForUpdates() {
    // Remove this for store builds!
    UpdateManager.register(this, APP_ID);
  }
  
  // Probably more methods
}</pre>

* The param APP_ID has to be replaced by your app's identifier. There are two options two create this: Either upload an existing .apk file of your app to HockeyApp or create a new app manually. The App ID can then be found an the app's page in the section App Info.
* Build your project, create an .apk file, upload it to HockeyApp and you are ready to go.

The above code does two things: 

1. When the activity is created, the update managed launched an async tasks which checks for new updates. If it finds a new update, an alert dialog is shown and if the user presses Show, another activity is launched.
2. When the activity is resumed, the crash manager is triggered and checks if a new crash was created before. If yes, it brings up a dialog to ask the user whether he wants to send the crash log to HockeyApp. The crash manage also registers a new exception handler, but only on the first launch.

The reason for the two different entry points is that the update check causes network traffic and therefore potential costs for your users. In contrast, the crash manager only searches for new files in the file system, i.e. the call is pretty cheap. 

## Checklist if Crashes Do Not Appear in HockeyApp

1. Check if the APP_ID matches the App ID in HockeyApp.

2. Check if the package name in your AndroidManifest.xml matches the Bundle Identifier of the app in HockeyApp. HockeyApp accepts crashes only if both the App ID and the package name equal their corresponding values in your app.

3. If your app crashes and you start it again, is the dialog shown which asks the user to send the crash report? If not, please crash your app again, then connect the debugger and set a break point in CrashManager.java, method [register](https://github.com/TheRealKerni/HockeyKit/blob/develop/client/Android/src/net/hockeyapp/android/CrashManager.java#L27) to see why the dialog is not shown.

5. If it still does not work, please [contact us](http://support.hockeyapp.net/discussion/new).

## <a name="nightly">Nightly Builds</a>

You can download nightly builds of the develop branch from [cisimple.com](https://www.cisimple.com/jobs/y85047ekwdj8jdjry). Builds are triggered at midnight (UTC) if we had pushed commits to our repository in the past 24 hours.

<a href="https://www.cisimple.com/jobs/y85047ekwdj8jdjry"><img src='https://www.cisimple.com/jobs/y85047ekwdj8jdjry/build_status.png'/></a>
