**Silver Layer**

The silver layer is a subscriber that pulls events from the Topic, validates and fans them out into rows, and performs an idempotent MERGE into a Snowflake table for low-latency analytics.

Processes:

- Pulls in a copy of a normalized event from Topic (stamp headers, market fields, dedupekey, time data)
- Data Validation: Validates required fields while also running type/range checks.
- Idempotent merge info fix_prices on dedupe key, if there's a match no OP, if there isn't insert the data.

Considerations for Snowflake Streaming:

Streaming in Snowflake is a low latency, process to write data into snowflake via an API. It is a direct path to write rows
into snowflake with low maintenance. However, it does not take into of hiccups / network errors when writing data to snowflake. This does not take care of retries
/back pressure that we have when we use a service bus / DQL.