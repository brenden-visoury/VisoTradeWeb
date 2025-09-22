# VisoTradeWeb

Disclaimer: There is no usable code in this repository but rather documentation of overall system design and explanation of the solution within this folder. To dig a bit further, please see the VisoTradeWeb directory.

**Research done for this project**

I reviewed materials to understand both the business shape of market data and the technical shape of a resilient pipeline.

FIX protocol concepts: session vs application messages, market-data message types (snapshot/incremental), tag/value format, resend/duplicate semantics.

Venue-specific nuances: subscribing to instruments, typical tags (symbol/security IDs, price/size, timestamps), and session behavior.

Azure messaging & storage: Azure Service Bus Topics (pub/sub, retries, DLQ, sessions/ordering), ADLS Gen2 (hierarchical namespace, partitioning, lifecycle).

Snowflake ingestion patterns: micro-batch MERGE idempotency, Snowpipe Streaming.

Data engineering: data contracts, schema versioning, idempotency keys, observability, DLQ/quarantine, replay runbooks.

https://javarevisited.blogspot.com/2011/04/fix-protocol-tutorial-for-beginners.html

I've only created barebones solution documentation / explanation of the solution I'm proposing

**If you were on-site, working on this project, as a team, what steps would you take. What would your thought process be.**

1. Align on goals of the overall project, asks a lot of questions about the data at hand and the nature of the where we're getting the data from, the nature of the engine that is feeding the data. Generally just getting an idea of the specific behaviors of the data and what's going to be available to us as users. It's also key to ask around and use resources available to us if anyone has used the FIX engine before. We'd also get clarity from stakeholders of the finalized formatting of the data needed and availability that would be shown to downstream application or analytics.
2. Create potential POC with the fewest moving parts. Start researching a potential solution with tools that are readily available to us and begin creating a system design diagram of a potential solution.
3. Start development by standing up a POC that will test the ability to move data into snowflake using sample data from a simple client to service bus and into a snowflake.
4. Once sample data is proved viable, and we can land data into snowflake via service bus + client, we can start moving into further development of the normalization process of the data. This process includes adding stamp headers, deduplication keys.
5. Once data has been normalized and metadata headers have been created, we can create the different landing zone processes to load data into their destinations (bronze, silver, quarantine/dlq).
6. Testing of the pipeline with real data and fix or adjust errors, add validation or business rules defined by the business.
7. Create environments for dev, qa, uat, and production. Establish monitoring and alerts.
8. Expose users to UAT for user testing - getting feedback and making fixes / adjustments.
9. Incremental release to production.

Where does your head go when you see this coding exam, what is the process you would take?

This was quite challenging due to the lack knowledge I have in regards to FIX protocols. My first step was to understand what a FIX protocol was and how it is used by financial firms.
After it made sense as to why it would be use, I had to do a bit more research on how solutions would be built around something like this and how important fault-tolerant and resilient systems are the financial world. 
Once I understood how FIX protocols worked, I needed to dive into what the data looked like but more importantly, how it would be received, processed, and used. I needed to create a viable solution that would be capable of handling large volumes of data but also something that would be traceable due to the financial industry - this was the reason why it was important for me to introduce a raw bronze layer outside just writing data to snowflake. Since this data can also be prone to failures, hiccups, or even network issues, I implemented a layer that captured failed transmissions or code errors. 

Few things I needed here:
1. Understanding the data and the engine that feeds the data (since we are getting live streams)
2. Understanding what needs validation or normalization based on business rules
3. How to process errors, if errors occur (whether it be networked or code issues), while also continuing the flow of data in a pipeline
4. Understand how resilient are data systems are built
5. Understanding how service-client systems work, in this case specifically FIX protocol via TradeWeb.
