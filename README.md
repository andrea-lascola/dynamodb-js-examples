# DynamoDB js examples Use Cases:

- [Query](#Query)
    - [Get Items using **begins_with** on Sort Key](#Get-Items-using-begins_with-on-Sort-Key)
- [Put](#Put)
    - [Create or Update an Item](#Create-or-Update-an-Item)
- [BatchWriteItem](#BatchWriteItem)
    - [Delete a list of items](#Delete-a-list-of-items)

## Query 

#### _Get Items using **begins_with** on Sort Key_

##### Data Model

| pk | sk     | order    | jsonObj |
|----|--------|----------|---------|
| 0  | 4#10#7 | port     | {}      |
| 0  | 5#2#1  | x        | {}      |
| 1  | 1#1#0  | z        | {}      |
| 0  | 4#10#9 | firewall | {}      |
| 2  | 5#1#2  | y        | {}      |

```typescript
// Query
const params = {
    TableName: "test-t",
    KeyConditionExpression: "pk = :pk AND begins_with(sk, :sk)",
    ExpressionAttributeValues: {
        ":pk": "0",
        ":sk": "4#10",
    },
};
return await db.query(params).promise();

// Output
{ Items:
   [ { sk: '4#10#7', order: 'port', pk: '0', jsonObj: [Object] },
     { sk: '4#10#9', order: 'firewall', pk: '0', jsonObj: [Object] } ],
  Count: 2,
  ScannedCount: 2 }
```

## Put

#### _Create or Update an Item_

##### Data Model

| pk | sk     | order    | jsonObj |
|----|--------|----------|---------|
| 0  | 4#10#7 | port     | {}      |
| 0  | 5#2#1  | x        | {}      |
| 1  | 1#1#0  | z        | {}      |
| 0  | 4#10#9 | firewall | {}      |
| 2  | 5#1#2  | y        | {}      |

```typescript
// Put Operation
const params = {
    TableName: this.tableName,
    Item: {
        pk: "0"
        sk: "4#10#3",
        order: "k",
        jsonObj: {} // Can be a standard js object
    }
};
await this.db.put(params).promise();

// Output
// ...
```


## BatchWriteItem

#### _Delete a list of items_

##### Data Model

| pk | sk     | order    | jsonObj |
|----|--------|----------|---------|
| 0  | 4#10#7 | port     | {}      |
| 0  | 5#2#1  | x        | {}      |
| 1  | 1#1#0  | z        | {}      |
| 0  | 4#10#9 | firewall | {}      |
| 2  | 5#1#2  | y        | {}      |

```typescript
// BatchWriteItem Operation
const objects = [
    {
        "pk" : "0",
        "sk" : "5#2#1",
    }, 
    {
        "pk" : "0",
        "sk" : "4#10#7",
    }
]

const params = {
    RequestItems: {} as any
};
// Add Delete Requests on this.tableName
params.RequestItems[this.tableName] = objects.map(obj => ({
    DeleteRequest: {
        Key: {
            "pk": obj.pk,
            "sk": obj.sk
        }
    }
}));
await this.db.batchWrite(params).promise();

// Output
// ...
```
