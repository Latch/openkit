# Android Tutorial

The Android SDK allows you to initialize and unlock a Latch-supported lock. This tutorial corresponds with version 0.6.0 of the SDK.

## Setup

1. Declare SDK as a dependency
1. Initialize the library
1. View the locks and select one to unlock

### Declare SDK as a dependency

Include your local maven repository where the Latch SDK is stored at

```
repositories {
  //(...)
  maven {
    url "[your/path/to/sdk]"
  }
}
```

Note that we will be delivering the SDK as a zipped artifact during beta. The entire decompressed folder needs to be held at `[your/path/to/sdk]` described above. For example, the current folder structure for version 0.6.0 is `com/latch/sdk/0.6.0`.

In your **application module’s** build.gradle file. Declare latch-sdk as a dependency.

```
dependencies {
  implementation('com.latch:sdk:1.0.3')
  //(...)
}
```

### Initialize the library

Use your Auth0 token retrieved from Latch’s Auth endpoint, call `initialize()`.

```
LatchClient.initialize(context, token)
```

The Latch SDK uses RxJava to perform actions asynchronously. Each of the functions the SDK provides will return a Single that provides the result non-linearly.
Note that `context` here must not be the `applicationContext`.

```
// Initialize with lock inclusion preference
LatchClient.initialize(context, includeAllLocks = true)

// Set up with token
LatchClient.setupWithToken(token, includeAllLocks = true)
  .subscribe { setupResult ->
    when(setupResult) {
      SetupResult.Success -> // SDK ready
      SetupResult.InvalidToken -> // Handle token issues
      // Handle other cases
    }
  }
```

The `includeAllLocks` parameter determines whether to show:
- `true`: All locks that user can access (partner and non-partner)
- `false`: Only partner-managed locks

### View the locks and select one to unlock

Similar to `initialize()`, `locks()` is also a Single providing the results asynchronously.

```
LatchClient
  .locks()
  .subscribe
    { locksResult ->
      when(locksResult) {
        is LocksResult.Success -> {
          //Latch locks are available under locksResult.locks
        }
        //(handle other cases...)
      }
    }
```

With the locks retrieved from `LocksResult.Success.locks`, we can now call `unlock()` to unlock a Latch lock.

```
LatchClient
  .unlock(lock.uuid)
  .subscribe
    { unlockResult ->
      when(unlockResult) {
        UnlockResult.Success -> //lock is unlocked!
        //(handle other cases...)
      }
    }
```

Your Latch lock should be unlocked now!

## Unlock the closest lock that’s available

Another way to unlock the door is through “Proximity Unlock”. It will continuously unlock the closest lock that’s available.

First, we subscribe to the continuous listener.

```
LatchClient
  .proximityUnlockListener()
  .subscribe 
    { proximityUnlockStatus ->
      when(proximityUnlockStatus) {
        is ProximityUnlockStatus.Success -> // The unlocked door can be identified with proximityUnlockStatus.lockUuid
		
        // (stopProximityUnlock() is invoked for all other cases)
      }
    }
```

Then, use the `startProximityUnlock()` and `stopProximityUnlock()` function we have provided to control proximity unlock.

```
LatchClient
  .startProximityUnlock()
```

```
LatchClient
  .stopProximityUnlock() // The unlock listener is disposed
```

Note that, after proximity unlock is stopped, the previous listener is no longer available and a new listener is needed. The following code snippet is an example usage.

```
LatchClient
  .proximityUnlockListener()
  .subscribe 
  ...

LatchClient
  .startProximityUnlock() // Starts Proximity Unlock

LatchClient
  .stopProximityUnlock()  // Stops Proximity Unlock and dispose the listener

LatchClient
  .proximityUnlockListener()  
  .subscribe 
  ...
  
LatchClient
  .startProximityUnlock() // Restarts proximity unlock with a new listener
```

We also provide a one-shot proximity unlock functions, similar to `unlock()` and terminates after it unlocks the closest lock.

```
LatchClient
  .proximityUnlock()
  .subscribe 
    { proximityUnlockStatus ->
      when(proximityUnlockStatus) {
        is ProximityUnlockStatus.Success -> // The unlocked door can be identified with proximityUnlockStatus.lockUuid

	  //(handle other cases...) 
      }  
    }
```

## Sync

Sync allows your mobile client to act as a bridge to the Latch backend for uplink and downlink data requests, including battery, timestamp, activity logs, and engineering logs. In times of troubleshooting, a sync is recommended to either resolve the issue or provide Latch with full information around the issue.

After each unlock, the SDK will passively sync data with the Latch ecosystem to keep user data as up to date as possible. Explicitly calling `.sync()` will initiate a longer sync operation that attempts to sync all critical data, including the data synced after unlock, along with non-critical data. The `.sync()` operation takes about 10 seconds on average and will cancel any passive sync operations initiated after the unlock operation.

```
LatchClient
  .sync(lock.uuid)
  .subscribe
  { syncResult ->
    when(syncResult) {
      SyncResult.Success -> //lock sync is success!
      //(handle other cases...)
    }
  }
```

Your Latch lock is synced now!

## Access logs
Retrieve access logs for a lock.

```
LatchClient.accessLogs(lockUuid)
  .subscribe
  { accessLogsResult ->
    when(accessLogsResult) {
      is AccessLogsResult.Success -> {
        // Access logs are available under accessLogsResult.accessLogs
      }
      //(handle other cases...)
    }
  }
```

### Guest Access
Invite guests

```
LatchClient.inviteGuests(
  firstName = "John",
  lastName = "Doe",
  email = "john@example.com",
  phone = "+1234567890",
  startTime = LocalDateTime.now(),
  endTime = null, // No expiration
  deviceUuids = listOf(lockUUID.toString()),
  passcodeType = PasscodeType.PERMANENT
).subscribe { inviteResult ->
  // Handle invitation result
}
```

Retrieve guest list

```
LatchClient.guests()
  .subscribe { guestsResult ->
    when(guestsResult) {
      is GuestsResult.Success -> {
        val guestList = guestsResult.guests
      }
      // Handle error cases
    }
  }
```