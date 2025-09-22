```mermaid
flowchart LR
  A[Tradeweb Sim / FIX Acceptor] --> B[FIX Initiator - Publisher]
  B --> T[Service Bus Topic - fix-prices]
  T --> S1[Bronze Lander to ADLS raw]
  T --> S2[Silver Loader to Snowflake rows]
  T --> S3[Quarantine / DLQ]

