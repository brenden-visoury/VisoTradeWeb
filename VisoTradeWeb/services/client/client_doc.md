**Client Documentation**

The client is a service that listens to price updates from the FIX server.

There are a few steps the client takes before sending information off to the service bus:

- Takes in raw messages from the FIX server.
- Applies formatting changes, normalizes, and stamps headers (who, what, when, how).
- Creates a deduplication key for idempotency.
- Validates data before sending it downstream.
- Emits logging for debugging.

Once these processes have finished, it sends the event message to the topic for distribution.