# Fragment

## What is a Fragment?
A Fragment represents a reusable portion of your app's UI. It is a self-contained component with its own layout, 
lifecycle, and input events. You can combine multiple fragments in a single activity to build a multi-pane UI and 
reuse a fragment in multiple activities.

Modular UI
Fragments encapsulate UI components and their associated logic, promoting code organization and reusability.

Independent Lifecycle
Fragments have their own lifecycle stages, tied to the lifecycle of the hosting activity.

Dynamic Behavior
Fragments can be added, removed, replaced, or hidden within an activity at runtime.

Reusability
A single fragment can be used in multiple activities, facilitating code sharing and consistency.

## What are the lifecycles of a fragment?

onAttach()
Called when the fragment is associated with its host activity.
Use this method to get a reference to the activity and perform initial setup.
onCreate()
Called to do initial creation of the fragment.
Initialize essential components of the fragment that you want to retain when the fragment is paused or stopped, then resumed.
onCreateView()
Called to have the fragment instantiate its user interface view.
Inflate the layout for the fragment's UI and return it from this method.
onActivityCreated()
Called when the activity's onCreate() method has returned.
Access UI elements and perform actions that require the activity to be fully created.
onStart()
Called when the fragment is becoming visible to the user.
Make the fragment visible (based on its containing activity being started).
onPause()
Called when the fragment is no longer interacting with the user either because its activity is being paused or a fragment operation is modifying it in the activity.
Commit unsaved changes, but only to persistent data (like to a database), because the process may be killed by the system without another chance to save.
onStop()
Called when the fragment is no longer visible to the user.
This may happen because its activity is being stopped or a fragment operation is modifying it in the activity.
onDestroyView()
Called when the view previously created by onCreateView() has been detached from the fragment.
Clean up resources associated with the view.
onDestroy()
Called to do final cleanup of the fragment's state.
onDetach()
Called when the fragment is being disassociated from its activity.
Perform final cleanup and release resources.

## Difference between Activity and Fragment.

| Feature      | Activity                                                                                                               | Fragment                                                                                                          | 
|--------------|------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------| 
| Role         | Serves as the entry point for an interaction with the user. It represents a single, focused task the user can perform. | Represents a modular section of an activity, contributing to the overall UI.                                      | 
| Lifecycle    | Has its own lifecycle, independent of other activities.                                                                | Has its own lifecycle, but it's closely tied to the lifecycle of its host activity.                               | 
| UI           | Typically represents a full-screen window or a major portion of the screen.                                            | Represents a portion of the UI within an activity.                                                                | 
| Navigation   | Used for top-level navigation between different screens or tasks.                                                      | Can be used for navigation within an activity, such as switching between tabs or content sections.                | 
| Back Stack   | Managed by the system's back stack, allowing users to navigate back through previously visited activities.             | Can be added to the back stack within an activity, allowing users to navigate back through fragment transactions. | 
| Context      | Provides its own context, used for accessing system resources and services.                                            | Operates within the context of its host activity.                                                                 | 
| Independence | Can exist independently of other activities.                                                                           | Cannot exist without a host activity.                                                                             |

## add() vs replace() usages

| Feature                                      | add()                                       | replace()                                     | 
|----------------------------------------------|---------------------------------------------|-----------------------------------------------| 
| Fragment Stacking                            | Creates a stack of fragments                | Replaces the current fragment                 | 
| View Lifecycle                               | Existing fragment's view might be preserved | Existing fragment's view is destroyed         |
| Back Stack Behavior (if added to back stack) | Pressing back removes the top most fragment | Pressing back recreates the replaced fragment |

If you need to display multiple fragments simultaneously or want to create a back stack of fragments, use add().
If you need to switch between different content views and don't need to preserve the previous fragment's state, use replace().

## Why fragment needs a default constructor?

Fragment Recreation:
The system might destroy and recreate fragments during the activity lifecycle (e.g., due to configuration changes like screen rotation).
When a fragment is recreated, the system uses a default constructor (no arguments). If you rely on a constructor with 
arguments, those arguments won't be preserved during recreation, leading to unexpected behavior or crashes. 

Fragment Manager:
The FragmentManager is responsible for managing fragments and their lifecycles.
It needs to be able to instantiate fragments using reflection, which requires a default constructor.

## How to make sure fragment data is preserved in recreation?
Use the setArguments() method to pass data to a fragment through bundles. This ensures that the data is preserved during
fragment recreation.

## What is a FragmentManager?

FragmentManager is a core component in Android development that plays a crucial role in managing fragments within your activities.

Adding, Removing, and Replacing Fragments:
You use the FragmentManager to perform transactions like adding, removing, or replacing fragments within your activity's layout.
It provides methods like beginTransaction(), add(), replace(), remove(), and commit() to handle these operations.

Fragment Back Stack:
The FragmentManager manages the back stack of fragment transactions.
When you add a transaction to the back stack using addToBackStack(), the FragmentManager keeps track of the changes so that the user can navigate back through previous fragment states using the back button.

Fragment Lifecycle:
The FragmentManager is involved in the lifecycle of fragments, calling the appropriate lifecycle methods (e.g., onCreate(), onCreateView(), onResume(), etc.) as fragments are added, removed, or their state changes.

Finding Fragments:
You can use the FragmentManager to find fragments by their ID or tag, allowing you to access and interact with specific fragments within your activity.

Handling Configuration Changes:
The FragmentManager helps preserve the state of fragments during configuration changes (e.g., screen rotation) to prevent data loss and ensure a smooth user experience.

```
val fragmentManager = supportFragmentManager
fragmentManager.beginTransaction()
    .add(R.id.fragment_container, myFragment)
    .addToBackStack(null)
    .commit()
```

## fragmentManager vs childFragmentManager?

| Feature | fragmentManager                                         | childFragmentManager                                     | 
|---------|---------------------------------------------------------|----------------------------------------------------------| 
| Scope   | Associated with an activity                             | Associated with an Parent Fragment                       | 
| Usage   | Manage fragments directly in activity                   | Manage nested fragments within a parent fragment         | 
| Access  | supportFragmentManager or fragmentManager (in activity) | childFragmentManager (in parent fragment)                | 
| Example | Adding a fragment to an activity's layout               | Adding a fragment to a container within another fragment |

## What are the different ways to add a fragment using FragmentManager?
add()
replace()
Combination of add() and hide()/show()
Add multiple fragments initially and then use hide() and show() to control their visibility without destroying their views.

## What is addToBackStack?
This manages the history of fragment transactions. It doesn't directly affect how fragments are 
displayed but rather controls how the back button behaves in relation to fragment changes.

add(): Placing a new paper on the stack. You're adding a new element (fragment) to the visible pile (activity).
addToBackStack(): Taking a photo of the stack before making any changes (whether it's adding or replacing). You're recording a snapshot of its state, so you can revert to it later.
replace(): Taking the top paper off the stack and putting a different one in its place. You're replacing the visible element (fragment) with a new one.

If you take a photo (addToBackStack()) before replacing the paper, you can later use that photo to:
Remove the replacement paper.
Put the original paper back on the stack in the same position.

## Why fragments do not have their own context?
Fragments in Android are designed to be reusable components that can be used in multiple activities and layouts.
Fragments use the context of the hosting activity to perform various tasks such as view initialization, resource access
and launching other components.

## When to use getActivity() vs getContext()?

| Feature      | getActivity()                                                                        | getContext()                                                      | 
|--------------|--------------------------------------------------------------------------------------|-------------------------------------------------------------------| 
| Return Type  | Activity                                                                             | Context                                                           | 
| Lifecycle    | Tied to activity lifecycle                                                           | Tied to fragment lifecycle                                        | 
| Usage        | access system services that require an activity context (e.g., starting an activity) | general operations like inflating layouts or accessing resources. |
| Interactions | accessing activity-level methods or properties                                       | operations related to the fragment's UI or view hierarchy         |

## Why are fragments lightweight compared to activities? 

Lifecycle
Fragments have a more streamlined lifecycle than activities. While they still go through various states (attached, created, resumed, etc.), their lifecycle is tied to the host activity. This means they don't have the full overhead of managing an independent application context and process.

View Hierarchy
Fragments manage a portion of the user interface within an activity. They don't represent an entire screen or window like activities. This smaller scope of responsibility contributes to their lighter weight nature.

Modularity
Fragments are designed to be modular and reusable UI components. You can combine multiple fragments within a single activity to create complex layouts, reducing the need for separate activities for each screen or interaction.

Resource Management
Fragments can share resources and data with their host activity, avoiding unnecessary duplication and overhead.

Back Stack
The fragment back stack is managed within the activity's back stack, further reducing the complexity and resource consumption associated with fragment navigation.

## What are the benefits of Fragments for being lightweight?
Improved Performance
Fragments can lead to smoother and more responsive user interfaces, especially when dealing with complex layouts or frequent transitions.

Reduced Memory Usage
Their smaller footprint and shared resources can help minimize memory consumption, improving app stability and efficiency.

Enhanced Flexibility
Fragments promote modularity and code reuse, making it easier to create dynamic and adaptable UIs.

## commit() vs commitAllowingStateLoss()?

activity state is saved means onSaveInstanceState() has been called.

| Feature    | commit()                                                  | commitAllowingStateLoss()                                                        | 
|------------|-----------------------------------------------------------|----------------------------------------------------------------------------------| 
| Execution  | Schedules the transaction asynchronously                  | Similar to commit(), but allows execution even if activity state is saved        | 
| State Loss | Throws an exception if activity state is saved            | Risks potential state loss if executed after state is saved                      | 
| Use Cases  | Most common scenario, ensures safe execution              | Use sparingly when prioritizing execution over state loss risk                   | 
| Example    | Adding or replacing fragments during normal activity flow | Handling transactions after asynchronous operations or delayed user interactions |

## How to communicate between fragments?
Shared ViewModel

Interface Listener

Fragment Result API

Broadcast Receivers

## How to communicate between Activity and its Fragment?
Shared ViewModel

Interface Listener

Accessing Activity Members Directly
```
(activity as? MyActivity)?.someMethodInActivity()
```

## How can activity call methods in child fragment?
Fragment manager to find fragment by Id

```
val childFragment = supportFragmentManager.findFragmentById(R.id.child_fragment_container) as? MyChildFragment
childFragment?.someMethodInChildFragment()
```

## Which lifecycle method of Fragment gets called only once in the entire lifecycle?
onAttach()
It's called only once during the entire lifecycle of the fragment. Even if the fragment is detached and reattached (e.g., due to configuration changes)

## Is onCreate() of fragment called multiple times?
Only when fragment is recreated completely after configuration changes.

## How to implement a fragment without any UI? When is this useful?
By returning null in onCreateView().

Background Operations:
Perform tasks like network requests, database operations, or other long-running processes without blocking the main thread.
Retain the task's state across configuration changes using a retained fragment.

Centralized Logic:
Encapsulate logic or data that needs to be shared between multiple fragments or activities.
Example: A fragment that manages a music player or location updates.

Event Handling:
Listen for system events (like connectivity changes or sensor readings) and trigger actions in other components.

```
class DownloadFragment : Fragment() {

    // ... download logic using a DownloadManager or other mechanisms

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Retain the fragment across configuration changes
        retainInstance = true 
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return null
    }
}
```

## How can we preserve fragment state without recreation?
if retainInstance = true is set, the fragment instance itself is retained across configuration changes

The fragment's onDestroy() and onCreate() methods are not called.
The fragment's view hierarchy is still rebuilt (as the activity's view hierarchy changes).
The fragment keeps its existing state and data without relying on the saved instance state bundle.


## What are the fragment lifecycles when added through add()?
   When you add Fragment B on top of Fragment A, Fragment A moves to the back stack but is not destroyed.

   Fragment A Lifecycle:
   onPause()
   onStop()
   Fragment B Lifecycle:
   onCreateView()
   onViewCreated()
   onActivityCreated()
   onStart()
   onResume()
   When navigating back from Fragment B to Fragment A:
   onPause() (Fragment B)
   onStop() (Fragment B)
   onDestroyView() (Fragment B)
   onStart() (Fragment A)
   onResume() (Fragment A) 

## What are the fragment lifecycles when added through replace()?
   When you replace Fragment A with Fragment B, Fragment A is removed and Fragment B takes its place.

   Fragment A Lifecycle:
   onPause()
   onStop()
   onDestroyView()
   onDestroy()
   onDetach()
   Fragment B Lifecycle:
   onCreateView()
   onViewCreated()
   onActivityCreated()
   onStart()
   onResume()
   When navigating back (if Fragment A was added to the back stack during replacement):
   onPause() (Fragment B)
   onStop() (Fragment B)
   onDestroyView() (Fragment B)
   onCreateView() (Fragment A) - If the view was destroyed earlier
   onViewCreated() (Fragment A)
   onActivityCreated() (Fragment A) - If the fragment was destroyed earlier
   onStart() (Fragment A)
   onResume() (Fragment A)