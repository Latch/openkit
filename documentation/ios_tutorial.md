# iOS Tutorial

The iOS SDK allows you to initialize and unlock a Latch-supported lock.

## Setup and Installation

1. Go to File > Add Packages... > Add Local...
1. Navigate to LatchSDK Swift Package
1. Select your project in "Add to Project" pop-up button
1. Click "Add Package"

## Initialization

Async/Await

```
let token = ... // fetched from Auth0
let latch = try await Latch.initialize(withToken: token)
```

Completion Block

```
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

```
let locks = await latch.locks()
```

Completion Block

```
latch.locks { locks in
...
}
```

## Unlock

Async/Await

```
try await latch.unlock(lockID: lock.id.uuidString)
```

Completion Block

```
latch.unlock(lock) { result in
  switch result {
  case .success:
    ...
  case .failure(let error)
    ...
  }
}
```

## Proximity Unlock

```
latch.proximityUnlockHandler = { unlock in
  print("Unlock Status: \(unlock.lockID) \(unlock.status)")
}
latch.startProximityUnlock()
...
latch.stopProximityUnlock()
```




