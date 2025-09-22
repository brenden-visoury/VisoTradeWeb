**Bronze Layer**

The bronze layer is implemented as Azure Data Lake Storage. This layer receives information from the topic and stores messages in Azure Data Lake Storage.

Why a bronze layer?  
A bronze layer is an immutable "camera roll" of every event exactly as published.  
This allows us to: replay/backfill data, audibility, and give us schema change saftey without depending on downstream logic in snowflake.

Events from the publisher is sent to this storage bucket along with metadata to help with debugging.