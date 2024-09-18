# Os Versions

## what were the restrictions implemented on background services from Oreo version?

Background Execution Limits
Restriction: Apps running in the background have limited access to start services.
Impact: You generally cannot start a service from an activity that is not in the foreground or from a broadcast receiver that receives an implicit broadcast.

Location Limits
Restriction: Background apps have limited access to location updates.
Impact: You cannot request frequent location updates in the background.

Background Service Limitations in Doze Mode
Restriction: Background services have further restrictions when the device is in Doze mode.
Impact: The system might defer or restrict network access and CPU usage for background services.

