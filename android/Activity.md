# Activity

## What is an Activity?

## What are the lifecycles of an activity?

1. onCreate()
   Called when the activity is first created.
   Use this to set up the activity's user interface (using setContentView()), initialize variables, and perform any other one-time setup operations.
   You receive the savedInstanceState Bundle, which contains data the activity may have saved in onSaveInstanceState() if it was previously destroyed.
2. onStart()
   Called when the activity becomes visible to the user.
   The activity might not be in the foreground yet and the user may not be able to interact with it.
   A good place to start animations or other visual elements.
3. onResume()
   Called when the activity starts interacting with the user.
   The activity is in the foreground and has focus.
   Use this to start tasks that need to run while the activity is visible, such as playing music or animations.
4. onPause()
   Called when the activity is no longer in the foreground, but is still visible (e.g., when a dialog or another activity partially covers it).
   Use this to pause animations, release resources like the camera, or save any unsaved data.
5. onStop()
   Called when the activity is no longer visible to the user (e.g., when another activity takes over the entire screen).
   Release any resources that are no longer needed and stop tasks that don't need to run in the background.
6. onRestart()
   Called when an activity is being restarted after being stopped.
   This happens when the user navigates back to the activity after it was previously stopped.
7. onDestroy()
   Called before the activity is destroyed.
   This is the final call you receive.
   Use this to release any remaining resources.
8. onSaveInstanceState()
   Called before the activity is stopped, but only if the system is potentially going to destroy the activity.
   Allows you to save the activity's state in a Bundle so it can be restored later if the activity is recreated.

## When to save activity data?
The system can destroy an activity at any time if it needs to free up resources. 
You should always save important data in onPause() or onSaveInstanceState() to prevent data loss.

## When is onRestart called?

## When is onSaveInstance called?
onSaveInstanceState() is called in situations where the system might destroy the activity, but it's not a guarantee 
that the activity will be destroyed.

When the activity goes into the background: 
If the user presses the Home button, switches to another app, or a new activity covers the entire screen, the 
current activity goes into the background. The system might destroy the background activity to free up resources, 
so it calls onSaveInstanceState() as a precaution.

Configuration changes: 
When the device's configuration changes (e.g., screen rotation, keyboard availability), the activity is destroyed and 
recreated by default. onSaveInstanceState() is called before the destruction to allow you to save the activity's state.

When the user uses the "Recents" screen to switch apps: 
If the user navigates away from your app using the "Recents" screen and then returns, the activity might be recreated, 
and onSaveInstanceState() is called. Important points:

onSaveInstanceState() is not called when the user explicitly closes the activity (e.g., by pressing the back button) 
or when the activity finishes itself using finish().

The system doesn't guarantee that onSaveInstanceState() will be called every time an activity might be destroyed. 
It's an optimization mechanism, and the system might decide not to call it if it believes the activity will be 
immediately recreated.

You should only use onSaveInstanceState() to save the UI state of the activity (e.g., text in an EditText, 
scroll position). Don't use it to store persistent data, as there's no guarantee it will be called. 
For persistent data, use methods like Shared Preferences, files, or databases. 

In summary, onSaveInstanceState() is called when the system believes the activity might be destroyed, 
but it's not a guarantee of destruction. It's primarily used to preserve UI state across configuration changes 
and temporary destruction due to system constraints.

## When is onRestoreInstanceState called?
If the system destroys your activity (e.g., due to low memory) and the user navigates back to it, the activity is 
recreated. During recreation, the system calls onRestoreInstanceState() to allow you to restore the activity's 
state to how it was before it was destroyed.
The system calls onRestoreInstanceState() after onStart(). This method also receives the same Bundle containing 
the saved state as onCreate().

## Why use onRestoreInstanceState() bundle instead of onCreate()?
To separate the state restoration logic from the initial creation logic in onCreate()
onRestoreInstanceState() is only called when there is a saved state to restore, making the code more efficient.

## Write the order of lifecycles called when activity moves from A to B on a button click and back to A?

1. From Activity A to Activity B
   onPause() (Activity A): Activity A is partially visible as Activity B starts to take over.
   onCreate() (Activity B): Activity B is created and starts its setup.
   onStart() (Activity B): Activity B becomes visible to the user.
   onResume() (Activity B): Activity B is in the foreground and ready for user interaction.
   onStop() (Activity A): Activity A is no longer visible.
2. Back from Activity B to Activity A
   onPause() (Activity B): Activity B is partially visible as Activity A starts to come back.
   onRestart() (Activity A): Activity A is being restarted after being stopped.
   onStart() (Activity A): Activity A becomes visible.
   onResume() (Activity A): Activity A is in the foreground and ready for interaction.
   onStop() (Activity B): Activity B is no longer visible.
   onDestroy() (Activity B): Activity B is destroyed and its resources are released.



## Why are activity not initialized using constructors?

## When do we need to open an Activity in a new task?

Creating a new task for certain activities in your e-commerce app can enhance the user experience and handle specific scenarios more effectively.

Notifications and Deep Links:
When a user clicks on a notification or a deep link that should open a specific part of your app (e.g., a product page, order details), you might want to launch that activity in a new task. This ensures that the user has a clear entry point into that specific flow and can easily navigate back to their previous context.
For example, a notification about a flash sale could open the sale page in a new task.
Independent Flows:
If your app has distinct sections or flows that are largely independent of each other (e.g., browsing products, managing account settings, tracking orders), launching these sections in separate tasks can improve navigation and prevent the back stack from becoming cluttered.
For example, opening the "Order History" section could launch a new task, allowing the user to browse their orders without affecting their product browsing history.
Special Activities:
Certain activities, like a login screen or a settings screen, might be better suited to run in their own task. This can prevent them from being included in the back stack of other flows and ensure they are presented in a consistent way.
Sharing Content:
When your app allows users to share content (e.g., product links, promotions) with other apps, the sharing activity might be launched in a separate task to provide a clean separation between your app and the external app.

## What is taskAffinity?

## Why do launcher activity have exported flag set as true by default? 

## How to save the UI state of an activity?

## What are the different launch modes to launch an activity?

1. standard (default)
   Each time you start the activity, a new instance is created and added to the task's back stack.
   If you start the same activity multiple times, you'll have multiple instances of it in the back stack.
   Example: If Activity A starts Activity B (which has standard launch mode), a new instance of B is created and added to the stack above A. Pressing back goes back to A, then another press destroys A.
2. singleTop
   If an instance of the activity already exists at the top of the current task, the system routes the intent to that instance through onNewIntent() instead of creating a new one.
   If an instance doesn't exist at the top, a new instance is created.
   Example: If Activity A is at the top of the stack and starts Activity A again (with singleTop), onNewIntent() is called in the existing instance of A. No new instance is created.
3. singleTask
   The system creates a new task and instantiates the activity at the root of the new task. However, if an instance of the activity exists in a separate task, the system routes the intent to the existing instance through onNewIntent(). Only one instance of the activity can exist at a time.
   Example: If Activity A starts Activity B (with singleTask), and B is already running in a different task, the system brings that task to the foreground and calls onNewIntent() in B.
   A -> B -> C -> D
   B with SingleTask
   A -> B
4. singleInstance
   Similar to singleTask, but the system doesn't launch any other activities into the task holding the instance. The activity is always the single and only member of its task.
   Example: If Activity A starts Activity B (with singleInstance), B is launched in its own separate task, and no other activity can be added to that task.
