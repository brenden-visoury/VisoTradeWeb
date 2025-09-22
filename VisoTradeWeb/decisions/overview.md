FIX Protocol TradeWeb Engine

FIX (Financial Information eXchange) is an electronic messaging standard used by trading firms, venues, and brokers to exchange real-time market data and trading workflows. Messages are tag=value and cover subscriptions to prices, order/RFQ and execution reports, and post-trade messages across multiple asset classes.

FIX Functionality:

Works in two roles, the initiator (client) and TradeWeb (server). There's a session layer that tracks sessions between Client and Server to ensure that pipeline is healthy between the client and the server.
In the application layer, the client can send a market data request, where TradeWeb sends a snapshot and incremental refreshes with bid/offer/trade updates.

Example Input/Output:

Inputs

Log On:
8=FIX.4.4|9=000|35=A|49=SCM_CLIENT|56=TRADEWEB_SIM|34=1|52=20250922-22:15:00.000|98=0|108=30|141=Y|10=000|

Market Data Request:

8=FIX.4.4|9=000|35=V|49=SCM_CLIENT|56=TRADEWEB_SIM|34=2|52=20250922-22:15:01.000|
262=REQ-1|263=1|264=1|265=1|267=2|269=0|269=1|146=1|55=T_10Y|10=000|

Outputs:

Logon:

8=FIX.4.4|9=000|35=A|49=TRADEWEB_SIM|56=SCM_CLIENT|34=1|52=20250922-22:15:00.100|98=0|108=30|10=000|

Market Data Incremental Refresh:
8=FIX.4.4|9=000|35=X|49=TRADEWEB_SIM|56=SCM_CLIENT|34=3|52=20250922-22:15:02.000|
262=REQ-1|268=2|
269=0|270=99.51|271=1000000|55=T_10Y|279=0|
269=1|270=99.56|271=1000000|55=T_10Y|279=0|
10=000|


Key Considerations:
1. Fault Tolerant
2. Portable
3. Highly Available

Solution Overview:

The proposed solution would be a pipeline to receive and publish data to three different landing zones. A bronze (raw layer), silver (normalized, real time layer), and quarantine layer/ dead letter queue layer (records faulty records/hiccups).

The key considerations are as follows to allow a fault-tolerant architecture:

If a fix client writes directly to snowflake/adls, there could be the possibility that there will a slowdown from either snowflake or tradewebside processesing. To reduce this issue, a decoupled solution is proposed to allow the system to process data
independent of other modules in the system. This allows our system to act as a mailroom, rather than a phone call. The solution can serve many connections at once without having to worry about being blocked on downstream processes.