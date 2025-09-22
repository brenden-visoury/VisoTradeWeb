**Quarantine**

This is a landing zone where silver subscriber events occurred and were delivered fine but 
failed validation or business rules. This layer can live in a ADLS storage where files.

Some examples of items that would land here:

- Missing or invalid fields
- Undesired values (wrong values, currency, symbols)
- Bad timestamps (unparsable utc, or past/future times)
- Schema mismatches

Process:

- When silver processes an event and it fails its validation checks
- It writes a record to quarantine and complete the bus message.

**Dead Letter Queue**

This is a landing zone that logs information on transport/processing errors after retries. These errors include: connection issues and
code errors. It is a broker that moves the message the DLQ for debugging while also allowing the flow of data to continue.

When we fix the root cause of the error, we can on idempotent merges to avoid duplication.