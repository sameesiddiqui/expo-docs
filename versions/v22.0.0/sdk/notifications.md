---
title: Notifications
---

Provides access to remote notifications (also known as push notifications) and local notifications (scheduling and immediate) related functions.


## Remote (Push) Notifications

These are notifications that are sent remotely - meaning from your server, not app code. This means you can send notifications to your users when you need to tell them something important without them needing to take any action. They're a bit trickier than local notifications, but luckily Expo makes the whole process simple and straightforward. [Read more in the Push Notifications guide](../guides/push-notifications.html#push-notifications).

### `Expo.Notifications.getExpoPushTokenAsync()`

Gets the token that uniquely identifies this device. This token can be provided to the Expo notifications backend to send a push notification to this device. 

#### Returns

Returns a Promise that resolves to the token string. 


## Local Notifications

Local notifications happen entirely on the device, without needing to contact a server. If you need to alert the user of something right now or schedule something for the future (say, a calendar event), this is the way to go.

Creating a local notification is super easy:
```javascript
const notification = {
    title: 'This is a title',
    body: 'This is the body text of the local notification',
    android: {
      sound: true,
    },
    ios: {
      sound: true,
    }
}
```

Full parameter list:
#### LocalNotification
-  **title (_string_)** -- title text of the notification
-   **body (_string_)** -- body text of the notification.
-   **data (_optional_) (_object_)** -- any data that has been attached with the notification.
-   **ios (_optional_) (_object_)** -- notification configuration specific to iOS.
    -   **sound** (_optional_) (_boolean_) -- if `true`, play a sound. Default: `false`.
-   **android (_optional_) (_object_)** -- notification configuration specific to Android.
    -   **sound** (_optional_) (_boolean_) -- if `true`, play a sound. Default: `false`.
    -   **icon** (_optional_) (_string_) -- URL of icon to display in notification drawer.
    -   **color** (_optional_) (_string_) -- color of the notification icon in notification drawer.
    -   **priority** (_optional_) (_min | low | high | max_) -- android may present notifications according to the priority, for example a `high` priority notification will likely to be shown as a heads-up notification.
    -   **sticky** (_optional_) (_boolean_) -- if `true`, the notification will be sticky and not dismissable by user. The notification must be programmatically dismissed. Default: `false`.
    -   **vibrate** (_optional_) (_boolean_ or _array_) -- if `true`, vibrate the device. An array can be supplied to specify the vibration pattern, e.g. - `[ 0, 500 ]`.
    -   **link** (_optional_) (_string_) -- external link to open when notification is selected.


On Android, all we have to do now is schedule it and we're ready to go. Here's a working example:

![sketch](BkCZL5A6W)

iOS is slightly tougher. We need to follow a few steps:

 1. Create the notification and scheduling objects
 2. Setup permissions for iOS notifications
 3. Setup a listener so iOS users can get the notification.

Adding on to our Android example (step 1), we now need to request permissions from the user.

```javascript
async function getiOSNotificationPermission() {
  const { status } = await Permissions.getAsync(Permissions.NOTIFICATIONS);
  if (status !== 'granted') {
    await Permissions.askAsync(Permissions.NOTIFICATIONS);
  }
}
```

And then, we just need to subscribe for notifications with `Expo.Notifications.addListener` and display an [`Alert`](https://facebook.github.io/react-native/docs/alert.html). Here's the Android example expanded to work on iOS. Note: when testing out this example, make sure that you exit out of the app before time is up. The notification won't display on iOS if the app is being viewed. Also, make sure you don't have "Do Not Disturb" turned on.

![sketch](rk7VdlH6b)


## Subscribing to Notifications

Subscribing to notifications allows your app to respond to notifications being received or clicked.

### `Expo.Notifications.addListener(listener)`

Creates a subscription that listens for notification events.

#### Arguments

-   **listener (_function_)** -- A callback that is invoked when a remote or local notification is received or selected. Takes an object with three properties as its parameter:

    -   **origin (_string_)** -- Either `selected` or `received`. `selected` if the notification was tapped on by the user, `received` if the notification was received while the user was in the app.
    -   **data (_object_)** -- Any data that has been attached with the notification.
    -   **remote (_boolean_)** -- `true` if the notification is a push notification, `false` if it is a local notification.

#### Returns

An `EventSubscription` object that you can call remove() on when you would like to unsubscribe the listener from future notifications.

```javascript
// Subscribe to notifications after this component mounts
componentDidMount() {
    this._notificationSubscription = Notifications.addListener(this._handleNotification)
}

// Unsubscribe when unmounted
componentWillUnmount() {
    this._notificationSubscription && this._notificationSubscription.remove()
}

// Handle our notifications by printing if it's information
_handleNotification = ({ origin, data, remote }) => {
    let type = remote ? 'Push' : 'Local'
    console.log(
      `${type} notification ${origin} with data: ${JSON.stringify(data)}`
    )
}
```


## Scheduling Notifications

### `Expo.Notifications.presentLocalNotificationAsync(localNotification)`

Trigger a local notification immediately.

#### Arguments

-   **localNotification (_object_)** -- A [LocalNotification](#localnotification) object.

#### Returns

A Promise that resolves to a unique notification id.

### `Expo.Notifications.scheduleLocalNotificationAsync(localNotification, schedulingOptions)`

Schedule a local notification to fire at some specific time in the future or at a given interval.

#### Arguments

-   **localNotification (_object_)** --

      A [LocalNotification](#localnotification) object.

-   **schedulingOptions (_object_)** --

      An object that describes when the notification should fire.

    -   **time** (_date_ or _number_) -- A Date object representing when to fire the notification or a number in Unix epoch time. Example: `(new Date()).getTime() + 1000` is one second from now.
    -   **repeat** (_optional_) (_string_) -- `'minute'`, `'hour'`, `'day'`, `'week'`, `'month'`, or `'year'`.
    - (_Android only_) **intervalMs** (_optional_) (_number_) -- Repeat interval in number of milliseconds

#### Returns

A Promise that resolves to a unique notification id.

### `Expo.Notifications.dismissNotificationAsync(localNotificationId)`

_Android only_. Dismisses the notification with the given id.

#### Arguments

-   **localNotificationId (_number_)** -- A unique id assigned to the notification, returned from `scheduleLocalNotificationAsync` or `presentLocalNotificationAsync`.

### `Expo.Notifications.dismissAllNotificationsAsync()`

_Android only_. Clears any notifications that have been presented by the app.

### `Expo.Notifications.cancelScheduledNotificationAsync(localNotificationId)`

Cancels the scheduled notification corresponding to the given id.

#### Arguments

-   **localNotificationId (_number_)** -- A unique id assigned to the notification, returned from `scheduleLocalNotificationAsync` or `presentLocalNotificationAsync`.

### `Expo.Notifications.cancelAllScheduledNotificationsAsync()`

Cancel all scheduled notifications.

### Related types



## App Icon Badge Number (iOS)

### `Expo.Notifications.getBadgeNumberAsync()`

#### Returns

Returns a promise that resolves to the number that is displayed in a badge on the app icon. This method returns zero when there is no badge (or when on Android).

### `Expo.Notifications.setBadgeNumberAsync(number)`

Sets the number displayed in the app icon's badge to the given number. Setting the number to zero will both clear the badge and the list of notifications in the device's notification center on iOS. On Android this method does nothing.
