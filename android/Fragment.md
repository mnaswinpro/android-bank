# Fragment

## What is a Fragment?

## When to use a fragment?

## What are the lifecycles of a fragment?

## Difference between Activity and Fragment.

## How are fragments initialized?

## What is a FragmentManager?

## fragmentManager vs childFragmentManager?

## What are the different ways to add a fragment using FragmentManager?

## What is addToBackStack?

## What are the fragment lifecycles when added through replace()?

## What are the fragment lifecycles when added through add()?

## Why fragments do not have their own context?

## When to use getActivity() vs getContext()?

## Why are fragments lightweight compared to activities?

## commit() vs commitAllowingStateLoss()?

## How to communicate between fragments?

## Which lifecycle method of Fragment gets called only once in the entire lifecycle?

## Is onCreate() of fragment called multiple times?

## How to implement a fragment without any UI? When is this useful?


1. From Fragment A to Fragment B
   onPause() (Fragment A): Fragment A is no longer in the foreground but is still visible.
   onStop() (Fragment A): Fragment A is no longer visible.
   onDestroyView() (Fragment A): The view hierarchy associated with Fragment A is destroyed.
   onCreateView() (Fragment B): The view hierarchy for Fragment B is created.
   onViewCreated() (Fragment B): Called after onCreateView, you can access the view hierarchy here.
   onActivityCreated() (Fragment B): Called after the activity's onCreate() has completed.
   onStart() (Fragment B): Fragment B is visible.
   onResume() (Fragment B): Fragment B is in the foreground and interacting with the user. 
2. Back from Fragment B to Fragment A
   onPause() (Fragment B): Fragment B is no longer in the foreground.
   onStop() (Fragment B): Fragment B is no longer visible.
   onDestroyView() (Fragment B): The view hierarchy associated with Fragment B is destroyed.
   onCreateView() (Fragment A): If Fragment A's view was destroyed, it will be recreated here.
   onViewCreated() (Fragment A): Called after onCreateView.
   onActivityCreated() (Fragment A): Only called if Fragment A was newly created.
   onStart() (Fragment A): Fragment A is visible.
   onResume() (Fragment A): Fragment A is in the foreground


1. Using add Transaction
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

2. Using replace Transaction
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