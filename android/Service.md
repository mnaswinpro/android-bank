# Service

## What is a Service?
Services handle long-running operations in the background, such as playing music or fetching data over the network.
They have no UI.

Purpose: Perform long-running operations or background tasks without a user interface.
Example: Playing music, downloading files, processing data in the background.
Types:
Foreground Services: Perform operations that are noticeable to the user (e.g., music playback) and require ongoing notification.
Background Services: Perform operations that are not directly visible to the user. (Note: Restrictions on background execution have become stricter in recent Android versions.)

## On which thread does Service run? Why?
By default, on Main thread.
For Simplicity (no thread management) and UI updates.

## What are the types of Services?

### Foreground Services
Purpose: Perform tasks that are noticeable to the user and require continuous operation, even if the user switches to a different app.
Visibility: Display a persistent notification to indicate that the service is running.
Use Cases: Music playback, location tracking, file uploads/downloads.
Key Feature: Higher priority, less likely to be killed by the system

### Background Services
Purpose: Perform operations that are not directly visible to the user.
Visibility: No persistent notification.
Use Cases: Data synchronization, periodic tasks.
Key Feature: Lower priority, more likely to be killed by the system if resources are low.

### Bound Services
Purpose: Allow components (like activities) to bind to the service and interact with it.
Lifecycle: Tied to the binding component. When all components unbind, the service is destroyed.
Use Cases: Music playback, Inter-process communication (IPC), sharing data or functionality between components.

```
// In your activity or other component
val serviceIntent = Intent(this, MyService::class.java)
bindService(serviceIntent, connection, Context.BIND_AUTO_CREATE)

// ServiceConnection object
private val connection = object : ServiceConnection {
    override fun onServiceConnected(className: ComponentName, binder: IBinder) {
        // Get service instance and interact with it
    }

    override fun onServiceDisconnected(className: ComponentName) {
        // Service has disconnected
    }
}
```

## What are the restrictions on background service?

## What are IntentService?
A specialized service that handles tasks sequentially on a worker thread and finished when task is completed.
Deprecated in favour of WorkManager.

## What are Started Services?
Services that are started using startService() and run indefinitely until stopped using stopService() or stopSelf().
Foreground and background services are both started services, but they differ in their visibility to the user and their priority in the system.

## How to convert a started service to foreground service?
Use startForeground() to promote a started service to the foreground when necessary.

## startService vs bindService
| Feature       | startService()                                                                    | bindService()                                                                               | 
|---------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------| 
| Purpose       | Start a service for background operations                                         | Establish a connection to a component for interaction                                       | 
| Lifecycle     | Service runs independently until explicitly stopped (stopService() or stopSelf()) | Service is created when the first component binds and destroyed when all components unbind  |
| Lifecycle     | onCreate() - onStartCommand() - onDestroy()                                       | onCreate() - onBind() - onServiceConnected() - onUnbind() - onDestroy()                     |
| Communication | Typically one-way; component starts service and may send data via intents         | Two-way; component can directly interact with the service using a binder                    | 
| Use Cases     | Background tasks (e.g., playing music, downloading files)                         | Inter-process communication (IPC), sharing data/functionality (e.g., music player controls) | 
| Example       | startService(Intent(this, MyService::class.java))                                 | bindService(Intent(this, MyService::class.java), connection, Context.BIND_AUTO_CREATE)      |

## When is onRebind() called?
onRebind() is called in scenario where a client re-establishes a connection with a service after previously unbinding. 
It's useful for restoring state and notifying the client about the rebinding event.

## What happens when service is called multiple times?
First startService() Call:
The system creates the service and calls its onCreate() method.
Then, it calls onStartCommand(), passing the Intent used to start the service.

Subsequent startService() Calls:
onCreate() is not called again (the service is already created).
onStartCommand() is called for each startService() call, receiving the corresponding Intent.