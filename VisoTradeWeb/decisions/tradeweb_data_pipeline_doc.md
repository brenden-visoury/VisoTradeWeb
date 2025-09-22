**Trade Web Data Pipeline Overview**

This pipeline will be broken down into three different parts: 

FIX Server -> Fix Client -> Service Bus Topic (bronze, silver, quarantine)

**Simulated Trade Fix Acceptor**  
Module that simulates TradeWeb fix protocols to receive and send messages to a client.

**Fix Client/Publisher**  
This FIX client maintains the (logon) connection to the TradeWeb and allows TradeWeb to send information to the client.  
This process sends logon and market data request, which Tradeweb sends a snapshot and then incremental refreshes based on what was requested in the initial log on.

This process takes raw price updates and hands off the information to the pub/sub layer (service bus topic). Not only does this process takes raw data, but also formats and integrates metadata information
that records necessary schema fields. These fields will include: Envelope (who, when, identity information) and market data information.

Data will be normalized/formatted based on business rules. Dedupe keys will also be created in this process to prevent duplication downstream.

**Buffer & Splitter (Azure Service Bus Topic)**

This process receives normalized events or messages from the FIX client and sends messages to it's subscribers (bronze, silver, quarantine).
The subscribers pull its copy from the topic and process it based on what the job (upserts, write file) of the subscriber is. If a subscriber has an error with processes, the message is sent to the dead letter queue.  

The service bus topics allow the messages to be fanned out to subscribers and allows subscribers to deal with errors on their own. If retry logic fails, messages are sent to the DLQ so it won't block the stream of data going to subscribers.

**Subscribers**

    - Bronze Lander: Storage bucket for raw fix data for audit, replay, and auditing purposes (Azure Data Lake Storage)
    - Silver Lander: Upserts events into a snowflake data w/ a unique dedupe key for idempotency via Snowflake Streaming.
    - Quarantine / DQL:
        - Quarantine: Holds data quality failures that allows streaming to continulously while also capture failed processes.
        - Dead Letter Queue: catches transporting failures to avoid pipeline failures.