# General

## What are the main components of Android?
Activity
Service
Broadcast Receivers
Content Providers

## Serialization vs Parcelable

| Feature        | Serialization                         | Parcelable                             | 
|----------------|---------------------------------------|----------------------------------------| 
| Mechanism      | Reflection                            | Custom code                            | 
| Implementation | java.io.Serializable                  | android.os.Parcelable                  | 
| Performance    | Slower                                | Faster                                 | 
| Overhead       | More garbage objects                  | Less overhead                          | 
| Use cases      | General serialization, files, network | Inter-component communication, Bundles |


## what is reflection, marshalling and unmarshalling?
Reflection is the ability of a program to inspect and manipulate its own structure and behavior at runtime.

Dependency injection: Libraries like Hilt or Koin use reflection to instantiate and inject dependencies into classes.
Serialization: Libraries like Gson or Moshi use reflection to convert objects to and from JSON or other formats.
Event buses: Libraries like EventBus use reflection to find and invoke methods annotated with @Subscribe.
Testing: Reflection can be used to access private members of classes for testing purposes.

Marshalling and unmarshalling are processes used to convert data between different types, often for the 
purpose of transmitting data across a network or storing it in a file.

Consider an app that communicates with a server using JSON. When sending data to the server, the app needs to convert 
its objects into JSON format (marshalling). When receiving data from the server, the app needs to convert the JSON 
data back into objects (unmarshalling). Libraries like Gson or Moshi handle this marshalling and unmarshalling 
process using reflection to inspect the objects and convert them to and from JSON.

## What are Intents? What are the different types?
Intents are powerful mechanisms for communication between different components of your app and even with components 
of other apps.

Explicit intents explicitly specify the component (Activity, Service, or Broadcast Receiver) that should receive the intent.
```
val intent = Intent(this, SecondActivity::class.java)
startActivity(intent)
```
Implicit intents specify an action to be performed, and the Android system determines which component is best suited to handle that action.
```
val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://www.example.com"))
startActivity(intent)

val intent = Intent(Intent.ACTION_SEND)
intent.type = "message/rfc822"
intent.putExtra(Intent.EXTRA_TEXT, message)
startActivity(intent)
```
When using implicit intents, it's possible that no app on the device can handle the requested action. Always check if 
there's an activity available to handle the intent before starting it.
```
if (intent.resolveActivity(packageManager) != null) {
    startActivity(intent)
} else {
    // Handle the case where no activity can handle the intent
}
```

## What are Intent filters? 
Intent filters allow Android components to declare the kinds of intents they are capable of responding to.
Declaring intent filters in the manifest makes them known to the Android system. This allows other apps and system 
components to target your components with implicit intents, even if your app isn't currently running.
```
<activity android:name=".WebActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http" />
        <data android:scheme="https" />
    </intent-filter>
</activity>
```

