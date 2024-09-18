# Broadcast Receiver

## What is a BroadcastReceiver?
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

## What are the types of receivers?

### Static
These receivers are registered in your app's AndroidManifest.xml file.
They are active even when your app is not running, and can therefore receive broadcasts from the system and other apps at any time.
```
<manifest ...>
    <application ...>
        <receiver android:name=".MyBroadcastReceiver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

### Dynamic
These receivers are registered programmatically within your app's code.
Their lifecycle is tied to the component that registered them (e.g., an activity or service).
Unregister them when they are no longer needed.
```
val myReceiver = MyBroadcastReceiver()
val filter = IntentFilter(Intent.ACTION_BATTERY_LOW)
registerReceiver(myReceiver, filter)

// ... later, when no longer needed
unregisterReceiver(myReceiver)
```

## How to perform long-running operations on onReceive of receiver?
goAsync() allows you to extend the lifetime of a broadcast receiver beyond the usual time limit.
Recommended : Use WorkManager for long-running operations
```
class MyBroadcastReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        val pendingResult = goAsync() 
        Thread {
            // Perform long-running task here
            // ...
            pendingResult.finish() 
        }.start()
    }
}
```