
## Show example of implementing a Workmanager to update a room db after receiving an event for system reboot. The db has a table which keeps count of how many times phone is restarted.
```
// 1. Define a Worker class
class RebootWorker(appContext: Context, workerParams: WorkerParameters) : Worker(appContext, workerParams) {

    override fun doWork(): Result {
        val database = AppDatabase.getInstance(applicationContext) 
        val rebootDao = database.rebootDao()
        val currentCount = rebootDao.getRebootCount()
        rebootDao.insert(RebootEntity(count =currentCount + 1))
        return Result.success()
    }
}

// 2. Create a WorkRequest
val rebootWorkRequest = OneTimeWorkRequestBuilder<RebootWorker>()
    .build()

// 3. Define a BroadcastReceiver for BOOT_COMPLETED
class BootReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action == Intent.ACTION_BOOT_COMPLETED) {
            WorkManager.getInstance(context).enqueue(rebootWorkRequest)
        }
    }
}

// 4. Register the BroadcastReceiver in your Manifest
<manifest ...>
    <application ...>
        <receiver android:name=".BootReceiver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
    </application>
</manifest>

// 5. (Optional) Create a function to get the reboot count
fun getRebootCount(context: Context): Int {
    val database = AppDatabase.getInstance(context)
    val rebootDao = database.rebootDao()
    return rebootDao.getRebootCount()
}

// --- Room Database Entities and DAO ---
@Entity
data class RebootEntity(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val count: Int = 0
)

@Dao
interface RebootDao {
    @Insert
    fun insert(rebootEntity: RebootEntity)

    @Query("SELECT count FROM RebootEntity ORDER BY id DESC LIMIT 1")
    fun getRebootCount(): Int
}
```

## How to configure a db entry weekly, every monday at 10 am.

## How to set Constraints to a Worker?
```
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .setConstraints(Constraints.Builder()
        .setRequiredNetworkType(NetworkType.CONNECTED) // Requires network connection
        .setRequiresCharging(true) // Requires device to be charging
        .setRequiresDeviceIdle(true) // Requires device to be idle
        .build())
    .build()

WorkManager.getInstance(context).enqueue(workRequest)
```

## How to run a Worker immediately?
enqueue() Directly
WorkManager will schedule it to run as soon as possible, taking into account any constraints and system conditions.
```
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
         .build()
     WorkManager.getInstance(context).enqueue(workRequest)
```

Set setExpedited()
Expedited work requests are given higher priority and are less likely to be delayed. This can impact battery life.
```
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
         .setExpedited(OutOfQuotaPolicy.RUN_AS_NON_EXPEDITED_WORK_REQUEST) // Handle quota issues
         .build()
     WorkManager.getInstance(context).enqueue(workRequest)
```

Minimize Constraints
Avoid setting unnecessary constraints on your work request if you want it to run immediately. Constraints like network 
availability or charging state can cause delays.
