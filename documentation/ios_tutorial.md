# iOS Tutorial

The iOS SDK allows you to initialize and unlock a Latch-supported lock.

## Setup and Installation

1. Go to File > Add Packages... > Add Local...
1. Navigate to LatchSDK Swift Package
1. Select your project in "Add to Project" pop-up button
1. Click "Add Package"

## Initialization

Async/Await

```swift
let token = ... // fetched from Auth0
let latch = try await Latch.initialize(withToken: token)
```

Completion Block

```swift
let token = ... // fetched from Auth0
Latch.initialize(withToken: token) { result in
  switch result {
  case .success(let latch):
    ...
  case .failure(let error)
    ...
  }
}
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
  case .failure(let error)
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
  case .failure(let error)
    ...
  }
}
```
