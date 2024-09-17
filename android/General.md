# General

## What are the main components of Android?

Activities are the presentation layer of an Android app. 
Each screen in an app is an Activity. 
They handle user interaction and display the UI.

Purpose: The primary way users interact with your app. They represent a single screen with a user interface. Think of them as individual pages or windows within your app.
Example: A login screen, a settings page, a screen for displaying a list of items.
Lifecycle: Managed by the system. Activities transition through various states (created, started, resumed, paused, stopped, destroyed) based on user interaction and the overall state of the device.

Services handle long-running operations in the background, such as playing music or fetching data over the network. 
They have no UI.

Purpose: Perform long-running operations or background tasks without a user interface.
Example: Playing music, downloading files, processing data in the background.
Types:
Foreground Services: Perform operations that are noticeable to the user (e.g., music playback) and require ongoing notification.
Background Services: Perform operations that are not directly visible to the user. (Note: Restrictions on background execution have become stricter in recent Android versions.)

Broadcast Receivers respond to system-wide broadcast announcements. 
For example, an app can register a receiver to listen for incoming SMS messages.

Purpose: Listen for and respond to system-wide broadcast announcements (events).
Example: Receiving an incoming SMS message, a low battery alert, a change in network connectivity.
Types:
Static: Declared in your AndroidManifest.xml file, they can receive broadcasts even when your app isn't running.
Dynamic: Registered programmatically within your app and are active only while your app is running.
They provide flexibility in listening for specific broadcasts only when needed and unregistering them when they're no longer required
```
// 1. Create a BroadcastReceiver
val myReceiver = object : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        // Handle the received broadcast
        if (intent.action == Intent.ACTION_POWER_CONNECTED){
            // Do something when the device is connected to power
        }
    }
}

// 2. Register the receiver
val filter = IntentFilter(Intent.ACTION_POWER_CONNECTED)
registerReceiver(myReceiver, filter)

// 3. Unregister the receiver (e.g., in onStop() or onDestroy())
unregisterReceiver(myReceiver)
```

Content Providers manage access to a structured set of data, such as contacts or media files. 
They can be used to share data between apps.
Purpose: Manage and share app data with other apps. They provide a structured mechanism for accessing and modifying data.
Example: Accessing the device's contacts, calendar events, or media files.
Data Access: Use a standardized interface (ContentResolver) to interact with content providers, allowing for data sharing between different apps in a secure and controlled manner.


## Serialization vs Parcelable

| Feature        | Serialization                         | Parcelable                             | 
|----------------|---------------------------------------|----------------------------------------| 
| Mechanism      | Reflection                            | Custom code                            | 
| Implementation | java.io.Serializable                  | android.os.Parcelable                  | 
| Performance    | Slower                                | Faster                                 | 
| Overhead       | More garbage objects                  | Less overhead                          | 
| Use cases      | General serialization, files, network | Inter-component communication, Bundles |


## what is reflection, marshalling and unmarshalling?

