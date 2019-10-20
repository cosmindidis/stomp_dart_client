# Stomp Dart
This library provides an implementation for a STOMP client connecting to a remote server. 
It should work for both pure dart and flutter.

## Usage

#### Initialize
The client gets created the specified config, 
please see the Config section to see all available options
```dart
StompClient client = StompClient(
    config: StompConfig(
        url: 'wss://yourserver'
        onConnect: onConnectCallback
    )
);
```
The connect callback should be used to make sure that we are actually connected before we subscribe or send messages
```dart
void onConnectCallback(StompClient client, StompFrame connectFrame) {
    // use the client object passed.
}
```

#### Connect
```dart
client.activate();
```

#### Subscribe
```dart
client.subscribe(destination: '/foo/bar', headers: {}, callback: (frame) {
    // Received a frame for this subscription
    print(frame.body);
})
```

#### Unsubscribe
`client.subscribe(...)` returns a function which can be called with an optional map of headers
```dart
dynamic unsubscribeFn = client.subscribe(destination: '/foo/bar', headers: {}, callback: (frame) {
    // Received a frame for this subscription
    print(frame.body);
})
...
unsubscribeFn(unsubscribeHeaders: {});
```

#### Send
```dart
client.send(desintation: '/foo/bar', body: 'Your message body', headers: {});
```

#### Disconnect
```dart
client.deactivate();
```

## StompConfig
This table shows all available options in `the StompConfig`
| Option                                       | Description                                                                                                |
|----------------------------------------------|------------------------------------------------------------------------------------------------------------|
| url: String                                  | The url of the server you want connect to (required)                                                       |
| reconnectDelay: int                          | The time in milliseconds between reconnect attempts. Set to 0 if you don't want to reconnect automatically |
| heartbeatOutgoing: int                       | The time in milliseconds between outgoing heartbeat messages. Set to 0 to not send any heartbeats          |
| heartbeatIncoming: int                       | The time in milliseconds between incoming heartbeat messages. Set to 0 to not receive any heartbeats       |
| onConnect: Function(StompClient, StompFrame) | Function to be called when the client successfully connected to the server.                                |
| onDisconnect: Function(StompFrame)           | Function to be called when the client disconnects expectedly                                               |
| onStompError: Function(StompFrame)           | Function to be called when the stomp server sends an error frame                                           |
| onUnhandledFrame: Function(StompFrame)       | Function to be called when the server sends a unrecognized frame                                           |
| onUnhandledMessage: Function(StompFrame)     | Function to be called when a subscription message does not have a handler                                  |
| onUnhandledReceipt: Function(StompFrame)     | Function to be called when a receipt message does not have a registered watcher                            |
| onWebSocketError: Function(dynamic)          | Function to be called when the underyling WebSocket throws an error                                        |
| onWebSocketDone: Function()                  | Function to be called when the underyling WebSocket is done/disconnected                                   |
| onDebugMessage: Function(String)             | Function to be called for debug messages generated by the internal message handler                         |


## Development

#### Running unit tests
```dart
pub run test test/
```

#### Generating coverage data
```dart
pub global activate coverage
pub global run coverage:collect_coverage --port=8111 --out=coverage.json --wait-paused --resume-isolates & dart --disable-service-auth-codes --enable-vm-service=8111 --pause-isolates-on-exit test/test_all.dart
```
And to convert to lcov
```dart
pub global run coverage:format_coverage --lcov --in=coverage.json --out=lcov.info --packages=.packages --report-on=lib
```