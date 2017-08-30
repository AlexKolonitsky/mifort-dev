# DynamoDb

If you have necessity in fast NoSQL database with effective scalability, and you don't want to reinvent the wheel for management of such elements as

* commissioning
* equipment setting and configuration
* software patching
* replication
* cluster scaling

Amazon DynamoDb will be a good solution.

DynamoDB is key-value (work on data occurs with a help of key) and has document data structure (you can query and update items in a document format such as JSON, XML, HTML, etc.).

> As the representative of non-relational databases, DynamoDB doesn't support composite queries based on joins or complicated transactions.

Distinctive feature of this database is the principle of payment based on the limit of volume held but not on the storage size data capacity. However, you should interact in the size of objects between 1 byte and 400KB for effective queries (writing, reading). If there are larger objects (for example not structured BLOB-objects) you may work with the following bundle of services: Amazon S3 - for storaging information, but DynamoDB - pointer on it.

## DynamoDB Customizable Services

In contrast to many other databases, the addition of any improvements for working there is possible to organize in few clicks. Here are some examples.

### FGAC - Fine-Grained Access Control
It's comfortable to implement FGAC in purpose to restrict access to your DB on top-level attributes in your document. It works through the secret access token.
There's no additional payment.

### Consistency of Data
It determines by the time in which the result of writing the data is successfully done in a subsequent reading of the element.
* eventual consistency of data is intended for increasing throughput of reading

> Generally, data is become consistent in a second.

* strongly consistent of data returns all records that received successful response after updating operations.

### Cross-region Replication

If you want to work with replicas (exact copies) of master table, it will be a good decision to pay attention to this feature. There is no need to add, that Amazon provides the opportunity to make replicas in great variety of regions. Benefits of using:
1. Effective disaster recovery
2. Faster reads for users situated in regions distinct from the main one
3. Easier traffic management
4. Comfortable regional migration without distracting from work
> Furthermore, schema of prices for replications is identical as for master table: only for used resources, no additional charge.

### DynamoDB Streams

Items in your table are changeable and you don't want to throttle table with requests? You may appreciate one more good possibility - DynamoDB Streams, which saves data in chronological sequence format (in the last 24 hours). So you can have access to it. At the end of this time data is deleted.

Don't forget to activate this feature separately for each table.

Changes made to any individual item will appear in the correct order.

**Example**

You have the following order of updating:
1. First player has 100 points
2. Second player - 50 points
3. First player - 70 points
The third updating must be after the first, but there are no guarantees that the second will be between the 1st and 3rd.

Reading updates from the stream occurs with the speed twice as fast as the speed of the provisioned write capacity within your table.

Also there is good opportunity to manage data in console in such a way: the name of the key, the item before (old one) and after (new one) and the type of updating (INSERT, REMOVE or MODIFY).

### Triggers
DynamoDB Triggers is a mechanism that admits to execute custom actions if there were any updates on item level in the table. So, you can specify it with the help of this algorithm:
* The custom logic (code of trigger) is stored in AWS function as a code
* Associate an AWS Lambda function to the stream (via DynamoDB Streams) on a table
* AWS Lambda reads the updates from the associated stream and executes the code in the function.

> Payment is made only for execution time

### Time-to-Live (TTL)
Time-to-Live is a mechanism which lets you define a specific timestamp to delete expired items from your tables, when timestamp is reached. This attribute is in POSIX format - storage time in seconds from January 1, 1970.

For example, it is used for deleting event logs, usage history, session data, etc.

Such mechanism works on the principle of least cost and tries not to divert resources from other more important tasks.

> Deletion occurs in 2 days in background

### Throughput Capacity

- Read Capacity
- Write Capacity

##### Capacity Calculation Table
| read/write capacity unit | Number of item per second | Units of Capacity|
| :---        |    :---:      |          ---: |
| Write  | 1    | less or equal 1KB  |
| Strongly Consistent Read  | 1      | less or equal 4KB |
| Eventual Consistency Reads  | 2      | less or equal 4KB    |

##### Formula

Required units = Amount of items per second * Math.ceil( necessary block  / table capacity unit)

##### Example


So, if we write blocks with the size less than 1KB and need to execute 100 operations per second, that needless quantity of resources for writing is 100.
In case if we read 1.5Kb - we need 200 units of resources for provision of 100 operations per second. 200 = 100 * Math.ceil(1.5 / 1) = 100 * 2.


The least throughput capacity - 1 write unit and 1 read unit. Amazon provides possibility of increasing provisioned throughput as often as you want, as for decreasing it's only 4 times.

> Note that if you are willing to determine units of any capacity you should pay your attention not to the quantity of items have been read per second, but the quantity of API calls. For instance, your application should read 300 items per second from table, and if each item is 4KB or less, then you need 300 units of read capacity. It makes no difference to do 300 separate GetItem calls or 30 BatchGetItem ones each returning 10 items.

### Global and Local Indexes

Since the requests are made only with primary key (except of 'scan' query), for effective tables' management you should
pay attention to such flexible mechanism as indexes. It allows queries based on not-primary attributes.

Primary key can be:
- partition key
- partition and sort key.

Also there are two types of indexes in DynamoDB.

| Type of indexes| Partition key | Sort key | Amount| Supported  queries|
| :---        |    :---      |        :--- |:---:|:---: |
| Local  | Coincide with partition key of table  | Has various sort key of table  |Until 5 indexes|Scan|
| Global    |Can differ from partition key of table  | Can differ from sort key of table  |Until 5 indexes|Query and Scan|

Attributes will appear in indexes only if they are in the table (so, their amount is less in comparison with other attributes). Thanks to this fact queries facing the index have a very high **efficiency**.

Local index consumes allotted resources as a part of connected table. It allows to execute element that have equal partition key value, but different sort key one by one.

It is a good possibility to tune throughput capacity of global indexes independently between tables. And you should take into consideration this idea, cause it helps to reallocate the load of table.

Closely to ways of keeping data, there can be found following types: number, string, binary, and boolean. But set, list, and map - cannot be indexed.

## Getting Started With DynamoDb

### Data Types DynamoDb

|Title| Types| Additional description|
|--|--|--|
|Scalar Types |String, Number, Binary, Boolean, Null |  |
|Document Types |List [ ... ], Map { ... } - until 32 nesting level |At the same time, it can contain different types|
|Set Types |String set, Number set, Binary set| Contains the same type elements|

### The Beginning of Work for Node js

Examples can be taken from the site [Amazon](http://docs.aws.amazon.com/amazondynamodb/latest/gettingstartedguide/GettingStarted.NodeJs.html).

> It should be noted that great importance is attached to keys in working with this database. Almost all requests must contain explicitly specified keys for performing operations. Otherwise, you must use Scan.
>> When formulating queries of various kinds, the conditions for key and non-key terms are set separately.

### SDK
For convenient work with DynamoDB, you should pay attention not to SDK [AWS.DynamoDB](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB.html), but on [AWS.DynamoDB.DocumentClient](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html). That allows not to use the cumbersome structure of the description of data types in the code. JavaScript objects are automatically converted to Amazon DynamoDB objects and back.
