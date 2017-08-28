# DynamoDb

If you have necessity in NoSQL database with fast performance and effective scalability, and you don't want to reinvent the wheel for management of such elements as 

* commissioning 
* equipment setting and configuration
* patching of software 
* replication
* cluster scaling

Amazon DynamoDb is a great decision.

DynamoDB is a key-value (work on data occurs with a help of key) and document data structures (querying and updating items in a document format such as JSON, XML, and HTML).

>DynamoDB as the representative of non-relational database doesn't support composite queries based on joins or complex transactions.

Distinctive feature of this database is the principle of payment based on throughput but not on the storage size capacity of data. However, you should interact with the size of objects between 1 byte and 400KB for effective queries (writing, reading). In case of larger objects (for example not structured BLOB-objects) you can work with the following bundle of services: Amazon S3 - for storage of wealth of information, but DynamoDB - pointer on it.

## DynamoDB Customizable Services

In contrast to many others databases, the addition of any improvements for working with database is possible to organize in a few clicks. Here are some examples.

### FGAC - Fine-Grained Access Control
You can use FGAC to restrict access to your data based on top-level attributes in your document. It works through the secret access token.
There is no additional payment.

### Consistency of Data
It determines by the time in which the result of writing the data is successfully done in a subsequent reading of the element.
* eventual consistency reads option maximizes read throughput

  >As a rule, data is become consistent in a second.

*  strongly consistent reads return a result that reflects all writes that received a successful response prior to the read.
  
### Cross-region Replication

Cross-region replication allows you to maintain identical copies (called replicas) of a DynamoDB table (called master table) in one or more AWS regions. Can be used for
1. Efficient disaster recovery
2. Faster reads for users in regions distinct from the main is situated
3. Easier traffic management
4. Easy regional migration with live data migration

  >While there is no additional charge for the cross-region replication library, you pay the usual prices for the following resources used by the process.  

### DynamoDB Streams

DynamoDB Streams provides a time-ordered sequence of item-level changes made to data in a table in the last 24 hours. At the end of this time data is deleted.

You should make it enable for each table. 

Changes made to any individual item will appear in the correct order.

**Example**

```
You have following order of updating
	1. First player has 100 points
	2. Second player - 50 points
	3. First player - 70 points
The third updating must be after the first, but there are no guarantees that the second will be between the 1st and 3rd. 
```
You can read updates from your stream in DynamoDB Streams at up to twice the rate of the provisioned write capacity of your DynamoDB table. 

You can manage data in console in such a way: the name of the key, the item before (old item) and after (new item) and the type of updating (INSERT, REMOVE or MODIFY).

### Triggers
DynamoDB Triggers is a feature which allows you to execute custom actions based on item-level updates on a DynamoDB table. You can specify the custom action in following:
* The custom logic (code of trigger) is stored in AWS function as code
* Associate an AWS Lambda function to the stream (via DynamoDB Streams) on a DynamoDB table
* AWS Lambda reads the updates from the associated stream and executes the code in the function.

>Payment is for the time execution

### Time-to-Live (TTL)
Time-to-Live is a mechanism that lets you set a specific timestamp to delete expired items from your tables, when timestamp is reached.  This attribute is in POSIX format - storage time in seconds from January 1, 1970г.

For example, it is used for deleting event logs, usage history, session data, etc. 

Such mechanism works on the principle of the least cost and tries not to divert resources from other more important tasks.

>Deletion occurs in 2 days in background
 
### Throughput Capacity

- Read Capacity
- Write Capacity 

##### Table of the calculation of throughput capacity
| read/write capacity unit | Number of item per second | Units of Capacity|
| :---         |     :---:      |          ---: |
| Write  | 1     | less or equal 1КВ    |
| Strongly Consistent Read  | 1      | less or equal 4КВ |
| Eventual Consistency Reads  | 2       | less or equal 4КВ     |

##### Formula
```
Units of Capacity required for assignment = Number of item per second * Math.ceil( block you need / table capacity unit)
```
##### Example

```
So, if we write blocks of the size less than 1КВ and need execute 100 operation per second, that needless amount of resources for writing is 100. 
In case if we read 1.5Kb - we need 200 units of resources for provision 100 operation per second. 200 = 100 * Math.ceil(1.5 / 1) = 100 * 2.
```

The least throughput capacity - 1 write unit and 1 read unit. You can increase your provisioned throughput as often as you want, decrease - 4 times.

>Note that the required number of units of Read Capacity is determined by the number of items being read per second, not the number of API calls. For example, if you need to read 500 items per second from your table, and if your items are 4KB or less, then you need 500 units of Read Capacity. It doesn’t matter if you do 500 individual GetItem calls or 50 BatchGetItem calls that each returns 10 items.

### Global and Local Indexes

Because the requests are made only with primary key (except of 'scan' query), for effective management on tables you should 
pay attention to such a flexible mechanism as indexes. It allows queries based on not-primary attributes.

Primary key can be:
 - partition key
 - partition and sort key.

Amazon DynamoDB supports two types of indexes.

| Type of indexes| Partition key | Sort key | Amount| Supported  queries|
| :---         |     :---      |         :--- |:---:|:---: |
| Local   | Coincide with partition key of table  | Has different sort key of table  |Until 5 indexes|Scan|
| Global     |Can differ from partition key of table  | Can differ from sort key of table  |Until 5 indexes|Query and Scan|

Thanks to the fact that elements appear in the index only when they exist in the table (they are rare objects relative to the rest of the table elements) for which an index is specified, queries facing the index have a very high ** efficiency **.

Local index consumes allotted resources as the part of connected table. It allows execute elements by elements, that have equal partition key value, but different sort key. 

You can tune throughput capacity independently from the table for global indexes. It allows reallocating the load of table.

All scalar data types (number, string, binary, and boolean) can be used for the sort key element of the local secondary index key. Set, list, and map types cannot be indexed.

## Getting Started With DynamoDb

### Data Types DynamoDb

|Tittle| Types| Additional description|
|--|--|--|
|Scalar Types |String, Number, Binary, Boolean, Null |  |
|Document Types |List [ … ], Map { … } - until 32 nesting level |At the same time, it can contain different types|
|Set Types |String set, Number set, Binary set| Contains the same type elements|

### The Beginning of Work for Node.js

Examples can be taken from the site [Amazon](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/GettingStarted.NodeJs.html).

>It should be noted that great importance is attached to keys in working with this database. Almost all requests must contain explicitly specified keys for performing operations. Otherwise, you must use Scan.
>>When formulating queries of various kinds, the conditions for key and non-key terms are set separately. 

### SDK
For convenient work with DynamoDB, you should pay attention not to SDK [AWS.DynamoDB](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB.html), but on [AWS.DynamoDB.DocumentClient](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html). That allows not to use the cumbersome structure of the description of data types in the code. JavaScript objects are automatically converted to Amazon DynamoDB objects and back.
