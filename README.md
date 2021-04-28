# Summary

[In this blog](https://blogs.oracle.com/nosql), we discussed about how important conflict detection and resolution is in active-active replication. 
*	Oracle NoSQL Multi-Region solution enabling predictable low latency and response time, from anywhere in the world 
*	Oracle continues to improve this solution by adding CRDT support. 
A Multi-Region table is a global, logical table that eliminates the problematic and error-prone work of replicating data between regions, enabling a developer 
to focus on application business logic.


# Conflict Detection and Resolution 

Implementing an active-active replication solution is not trivial. The key to success lies in real-time data movement, conflict detection and resolution, and support 
for heterogeneous environments. **Of the three, conflict detection and resolution introduces the most complexity.** 
When multiple systems are processing data transactions and the activity is shared across the systems, detecting and addressing conflicts across them becomes an essential 
requirement for any active-active replication configuration. Conflict detection and resolution options can be implemented globally, object by object, based on data values 
and complex filters, and through event-driven criteria including database error messages.


A CRDT is a data structure that can be replicated across processes, where replicas are updated independently without synchronization and the replicas can always converge 
to a correct common state. There are two types of CRDTs: operation-based CRDTs (CmRDTs) and State-based CRDTs (CvRDTs). 
This blog is about the CmRDTs implementation and more specific [Positive-Negative (PN) Counter](https://docs.oracle.com/en/database/other-databases/nosql-database/21.1/concepts/multi-region-architecture.html).

# MR_COUNTER data type   
MR_COUNTER data type is a counter CRDT. CRDT stands for Conflict-free Replicated Data Type. In a multi-region setup of an Oracle NoSQL database, a CRDT is a data type 
that can be replicated across servers where regions can be updated independently, and it is always possible to converge on a correct common state. Changes in the 
regions are concurrent and not synchronized with one another. In short, CRDTs provide a way for concurrent modifications to be merged across regions without user 
intervention. Oracle NoSQL Database currently supports the counter CRDT type which is called MR_COUNTER. The MR_COUNTER datatype is a subtype of the INTEGER or 
LONG or NUMBER data type.



# Use-Case 
in our first blog of this series [here](https://blogs.oracle.com/nosql/oracle-nosql-database-multi-region-table-part1-v2). We talked about
 **The Telco Problem – Offering a Family Plan use case.** One such service is a "Family Plan" option where a customer and their family share the Data Usage plan. 
 The customer is allocated a free data usage limit for a month which the customer's entire family collectively uses. When the total usage of your customer's family 
 reaches 90 percent of the data limit, the telecom provider sends you the customer an alert. Say there are four members in your customer's family plan who are spread
 across different physical regions. You The customer needs to get an alert from the telecom provider once the total consumption of their family reaches 90 percent of 
 the free usage. The data is replicated in different regions to cater to latency, throughput, and better performance. That means there are four regions, and each has 
 a kvstore containing the details of the customer's data usage. The usage of their family members needs to be updated in different regions and at any point in time,
 the total usage should be monitored, and an alert should be sent if the data usage reaches the limit.
**An MR_COUNTER data type** is ideal in such a situation to do conflict-free tracking of the data usage across different regions. In the above example, an increment 
counter in every data region's data store will track the data usage in that region. The consolidated data usage for all regions can be determined by the system at 
any point without any user intervention. That is the total data usage at any point in time can be easily determined by the system using an MR_COUNTER datatype.

# Goal
This project contains the snippets used when building this blog
