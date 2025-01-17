*   Add a `@server` instance variable referencing the `ActionCable.server`
    singleton to `ActionCable::Channel::ConnectionStub`

    This lets us delegate the `pubsub` and `config` method calls
    to the server. This fixes `NoMethodError` errors when testing
    channel logic that call `pubsub` (e.g. `stop_stream_for`).

    *Julian Foo*

*   Added `health_check_path` and `health_check_application` config to
    mount a given health check rack app on a given path.
    Useful when mounting Action Cable standalone.

    *Joé Dupuis*

*   `assert_broadcasts` now returns the messages that were broadcast.

    This makes it easier to do further analysis on those messages:

    ```ruby
    message = assert_broadcasts("test", 1) do
      ActionCable.server.broadcast "test", "message"
    end
    assert_equal "message", message

    messages = assert_broadcasts("test", 2) do
      ActionCable.server.broadcast "test", { message: "one" }
      ActionCable.server.broadcast "test", { message: "two" }
    end
    assert_equal 2, messages.length
    assert_equal({ "message" => "one" }, messages.first)
    assert_equal({ "message" => "two" }, messages.last)
    ```

    *Alex Ghiculescu*

*   Display broadcasted messages on error message when using `assert_broadcast_on`

    *Stéphane Robino*

*   The Action Cable client now supports subprotocols to allow passing arbitrary data
    to the server.

    ```js
    const consumer = ActionCable.createConsumer()

    consumer.addSubProtocol('custom-protocol')

    consumer.connect()
    ```

    See also:

    * https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#subprotocols

    *Guillaume Hain*

*   Redis pub/sub adapter now automatically reconnects when Redis connection is lost.

    *Vladimir Dementyev*

*   The `connected()` callback can now take a `{reconnected}` parameter to differentiate
    connections from reconnections.

    ```js
    import consumer from "./consumer"

    consumer.subscriptions.create("ExampleChannel", {
      connected({reconnected}) {
        if (reconnected) {
          ...
        } else {
          ...
        }
      }
    })
    ```

    *Mansa Keïta*

*   The Redis adapter is now compatible with redis-rb 5.0

    Compatibility with redis-rb 3.x was dropped.

    *Jean Boussier*

*   The Action Cable server is now mounted with `anchor: true`.

    This means that routes that also start with `/cable` will no longer clash with Action Cable.

    *Alex Ghiculescu*

*   `ActionCable.server.remote_connections.where(...).disconnect` now sends `disconnect` message
    before closing the connection with the reconnection strategy specified (defaults to `true`).

    *Vladimir Dementyev*

*   Added command callbacks to `ActionCable::Connection::Base`.

    Now you can define `before_command`, `after_command`, and `around_command` to be invoked before, after or around any command received by a client respectively.

    *Vladimir Dementyev*

Please check [7-0-stable](https://github.com/rails/rails/blob/7-0-stable/actioncable/CHANGELOG.md) for previous changes.
