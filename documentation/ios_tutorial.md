# iOS Tutorial

The iOS SDK allows you to initialize and unlock a Latch-supported lock.

## Setup and Installation

1. Go to File > Add Packages... > Add Local...
1. Navigate to LatchSDK Swift Package
1. Select your project in "Add to Project" pop-up button
1. Click "Add Package"

## Initialization

Initialization of the LatchSDK is done by calling Latch.initialize() function
This function takes two parameters:

`token` - representing optional String value of the Auth0 token

`allAccesses` - flag that indicate if we should load all accesses (partner and non-partner) of the user. (introduced in the version `1.44.0`)

Async/Await

```swift
let token: String? = ... // fetched from Auth0

/* 
Optional flag that indicate if we should load all accesses (partner and non-partner) of the user.
 - If not provided, the default value is `false` and will behave as it was previously.
 - Setting this flag to `true` will load all devices that user has access to.
*/
@available(*, introduced: 1.44.0)
let allAccesses: Bool = true

/// initialize with all accesses
let latchWithAllAccesses = try await Latch.initialize(withToken: token, loadAllAccesses: allAccesses)

/// initialize with default value (previous implementation)
let latch = try await Latch.initialize(withToken: token)
```

Completion Block

```swift
let token = ... // fetched from Auth0

/* 
Optional flag that indicate if we should load all accesses (partner and non-partner) of the user.
 - If not provided, the default value is `false` and will behave as it was previously.
 - Setting this flag to `true` will load all devices that user has access to.
*/
@available(*, introduced: 1.44.0)
let allAccesses: Bool = true

/// initialize with all accesses
Latch.initialize(withToken: token, loadAllAccesses: allAccesses) { result in
  switch result {
  case let .success(latch):
    ...
  case let .failure(error):
    ...
  }
}

/// initialize with default value (previous implementation)
Latch.initialize(withToken: token) { ... }
```

## Doors and Locks

Async/Await

```swift
let locks = await latch.locks()
```

Completion Block

```swift
latch.locks { locks in
  ...
}
```

## Unlock

Async/Await

```swift
try await latch.unlock(lockID: lock.id.uuidString)
```

Completion Block

```swift
latch.unlock(lockID: lock.id.uuidString) { result in
  switch result {
  case .success:
    ...
  case let .failure(error);
    ...
  }
}
```

## Proximity Unlock

```swift
latch.proximityUnlockHandler = { unlock in
  print("Unlock Status: \(unlock.lockID) \(unlock.status)")
}
latch.startProximityUnlock()
...
latch.stopProximityUnlock()
```

## Sync

Sync allows your mobile client to act as a bridge to the Latch backend for uplink and downlink data requests, including battery, timestamp, activity logs, and engineering logs. In times of troubleshooting, a sync is recommended to either resolve the issue or provide Latch with full information around the issue.

After each unlock, the SDK will passively sync data with the Latch ecosystem to keep user data as up to date as possible. Explicitly calling `.sync()` will initiate a longer sync operation that attempts to sync all critical data, including the data synced after unlock, along with non-critical data. The `.sync()` operation takes about 10 seconds on average and will cancel any passive sync operations initiated after the unlock operation.

Async/Await

```swift
try await latch.sync(lockID: lock.id.uuidString)
```

Completion Block

```swift
latch.sync(lockID: lock.id.uuidString) { result in
  switch result {
  case .success:
    ...
  case let .failure(error):
    ...
  }
}
```

## Access Logs

Access Logs are providing access logs information for a given lock.

Async/Await

```swift
/* Retrieve all access logs for the selected lock
 - Parameter lockUuid: UUID of the Lock
 - Returns: Array of `LatchAccessLog`
 */
@available(*, introduced: 1.44.0)
try await latch.getAccessLogs(lockUuid: lock.id)
```

Completion Block

```swift
/* Retrieve all access logs for the selected lock
 - Parameter lockUuid: UUID of the Lock
 */
@available(*, introduced: 1.44.0)
latch.getAccessLogs(lockUuid: lock.id) { result in
  switch result {
  case let .success(logs):
    ...
  case let .failure(error):
    ...
  }
}
```
