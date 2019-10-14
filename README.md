# Flutter PubNub Plugin

A cross platform plugin for implementing real time messaging applications using PubNub (https://www.pubnub.com/).
This plugin is not provided nor supported by PubNub. It was implemented part of a real time messaging application developed by Ingenio (https://www.ingenio.com/).

## Features

* Multi client support allowing to handle messaging and presence from multiple PubNub clients defined in the PubNub environment.
* Auth key handling
* Filter expression handling
* Presence capabilities
* Publish and Subscribe/Unsubscribe capabilities
* Channel Groups
* History
* Push notifications
* Status 
* Error handling

**IMPORTANT NOTES**:

* The plugin can only be used on iOS and Android and not supported on Flutter Web. In order to support Web, Desktop and mobile platforms, PubNub would have to create a pure Dart plugin that directly accesses their low level REST API.

## Roadmap

The plugin does not cover all functionalities offered by PubNub but is functional for implementing already complex real time messaging apps on iOS and Android.
We have items we will be working on soon:

PNConfig features:
- cipher key
- custom origin

## Getting Started

The plugin follows as much as possible the naming conventions PubNub exposes via their respective iOS and Android SDK.

Note: version 0.1.2 has breaking changes. unsubscribe, presence and publish methods are now accepting an array of channels rather than just one channel.

## Easy setup

```dart
import 'package:flutter_pubnub/pubnub.dart';


final PubNub _client = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx'));

```

The PubNubConfig constructor allows to pass a PubNub auth key, presence timeout in seconds, a filter expression and finally a UUID (note a default UUID is generated if not provided)

```dart

PubNubConfig(this.publishKey, this.subscribeKey, {this.authKey, this.presenceTimeout, this.uuid, this.filter});

```

## Table of contents

* [Creating one or more clients](#Creating-one-or-more-clients)
* [Subscribe to channels and unsubscribe from channels](#Subscribe-to-channels-and-unsubscribe-from-channels)
* [Publishing to channels](#Publishing-to-channels)
* [Handle presence](#Handle-presence)
* [Subscribe to PubNub events](#Subscribe-to-PubNub-Events)
* [Adding channels to a channel group](#Adding-channels-to-a-channel-group)
* [List channels belonging to a channel group](#List-channels-belonging-to-a-channel-group)
* [Remove channels from a channel group](#Remove-channels-from-a-channel-group)
* [Delete a channel group](#Delete-a-channel-group)
* [Subscribe to channel groups](#Subscribe-to-channel-groups)
* [Unsubscribe from channel groups](#Unsubscribe-from-channel-groups)
* [Retrieve history](#Retrieve-history)
* [Push Notifications](#Push-Notifications)
* [Signals](#Signals)
* [Cleanup](#Cleanup)

## Creating one or more clients

Creating one or more clients with PubNub subscribe and publish keys

```dart

// Note that the proper key values for PubNub are found in the client configuration dashboard under your PubNub account
final PubNub _client1 = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx'));
final PubNub _client2 = PubNub(PubNubConfig('pub-c-yyyy', 'sub-cyyy'));

```

Creating a client with a PubNub auth key

```dart

// Note that the proper key values for PubNub are found in the client configuration dashboard under your PubNub account
final PubNub _client = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx', authKey: 'auth-xxxx'));

```

Creating a client with a UUID

```dart

// Note that the proper key values for PubNub are found in the client configuration dashboard under your PubNub account
final PubNub _client = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx', uuid: '127c1ab5-fc7f-4c46-8460-3207b6782007'));

```

Creating a client with a presence timeout in seconds

```dart

// Note that the proper key values for PubNub are found in the client configuration dashboard under your PubNub account
final PubNub _client = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx', presenceTimeout: 120));

```

Creating a client with a filter expression

```dart

// The filter expression here is done, as an example on the uuid, so any messages sent from the client with that UUID will not be received in the subscribe method
final PubNub _client = PubNub(PubNubConfig('pub-c-xxxx', 'sub-cxxx', filter: 'uuid != "127c1ab5-fc7f-4c46-8460-3207b6782007"'));

```

Then in order to publish a message with the proper metadata that will trigger the filtering

```dart

// The filter expression here is done, as an example on the uuid, so any messages sent from the client with that UUID will not be received in the subscribe method
_client.publish(['Test-Channel1'], {'message': 'Hello World'},
               metadata: {
                'uuid': '127c1ab5-fc7f-4c46-8460-3207b6782007'
                }
              );

```

## Retrieve the client UUID

In some cases, especially when the UUID is not setup during client creation, it can be helpful getting the UUID that was automatically generated by PubNub.

```dart

_client.uuid().then((uuid) => print('UUID: $uuid'));

```

## Subscribe to channels and unsubscribe from channels

A client can subscribe to one or many channels. It can also unsubscribe from one or many/all channels

```dart

 _client.subscribe(['Test-Channel1', 'Test-Channel1']);

 _client.unsubscribe(['Test-Channel1']);
 
 _client.unsubscribeAll();

```


## Publishing to channels

A client can publish messages to one or many channels

```dart

 _client.publish((['Test-Channel1'], {'message': 'Hello World!'});

```

As previously described, if a filter has been set and if we need to react to it, metadata related to the configured filter must be passed during publication.

```dart

_client.publish(['Test-Channel1'], {'message': 'Hello World'},
               metadata: {
                'uuid': '127c1ab5-fc7f-4c46-8460-3207b6782007'
                }
              );

```

## Handle presence

A client can respond to presence events generated by PubNub (client disconnection, ...) but can also send custom presence events to one or many channels.

```dart

_client.presence(['Test-Channel1'], {'state': 'busy'});

```

## Subscribe to PubNub events

In order to build a chat service for example, the client does not only send messages but must also consume messages, be aware if the client on the other end disconnected, status.

``` dart

_client.onStatusReceived.listen((status) => print('Status:${status.toString()}'));

_client.onPresenceReceived.listen((presence) => print('Presence:${presence.toString()}'));

_client.onMessageReceived.listen((message) => print('Message:$message'));

_client.onErrorReceived.listen((error) => print('Error:$error'));

```

## Adding channels to a channel group

Channel groups access are handled by the PubNub Access Manager.

``` dart

_client.addChannelsToChannelGroup('Group1', ['Channel', 'Channel2']);

```

## List channels belonging to a channel group

``` dart

_client.listChannelsForChannelGroup('Group1').then((channels) {
                          print("Channels in Group 1: $channels");
                        });
                        
```

## Remove channels from a channel group

``` dart

 _client.removeChannelsFromChannelGroup('Group1', ['Channel']);
                        
```

## Delete a channel group

``` dart

_client.deleteChannelGroup('Group1');

```

## Subscribe to channel groups

``` dart

_client.subscribeToChannelGroups(['Group1']);

```

## Unsubscribe from channel groups

``` dart

_client.unsubscribeFromChannelGroups(['Group1']);

```

## Retrieve history

``` dart

_client.history('Channel', 1).then((items) {
                          if (items != null && items.isNotEmpty) {
                            print("Last Item: $items");
                          } else {
                            print('No items');
                          }
                        });
                        
```

## Push Notifications

Adding channels for push notifications using a Firebase messaging plugin push token

``` dart

_firebaseMessaging.getToken().then((token) {
    print("Token: $token");
    _client.addPushNotificationsOnChannels(PushType.FCM, token, ['Channel']);
    });
},

```

Listing channels enrolled in push notifications.

``` dart

_client.listPushNotificationChannels(PushType.FCM, token).then((channels) {
    print("Push Notes Channels: $channels");
});

```

Removing channels from push notifications.

``` dart

_client.removePushNotificationsFromChannels(PushType.FCM, token, ['Channel']);

```

Removing all channels from push notifications.

``` dart

_client.removeAllPushNotificationsFromDeviceWithPushToken(PushType.FCM, token);

```

## Signals

Signals allow to send small payloads (30 butes max) in a very efficient and cost effective way.

``` dart

_client.signal(['Channel2'], {'signal': 'Hello Signal'});

```


## Cleanup

When done with a PubNub client, the client can be closed and cleaned up.

```dart

_client.dispose();

```

## License

This plugin is under an Apache 2.0 license

## Features and bugs

Please file feature requests and bugs at the issue tracker. https://github.com/Ingenio/flutter_pubnub/issues
