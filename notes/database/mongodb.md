# **MongoDB Cheat Sheet - `$` Operators**

## **1. Query Operators**

Used in the `find()` method to filter documents.

|Operator|Description|Example|
|---|---|---|
|`$eq`|Matches values that are equal to a specified value|`{ age: { $eq: 25 } }`|
|`$ne`|Matches values that are not equal to a specified value|`{ status: { $ne: "active" } }`|
|`$gt`|Matches values greater than a specified value|`{ age: { $gt: 18 } }`|
|`$gte`|Matches values greater than or equal to a specified value|`{ age: { $gte: 21 } }`|
|`$lt`|Matches values less than a specified value|`{ age: { $lt: 30 } }`|
|`$lte`|Matches values less than or equal to a specified value|`{ age: { $lte: 65 } }`|
|`$in`|Matches any of the values in an array|`{ status: { $in: ["active", "pending"] } }`|
|`$nin`|Matches none of the values in an array|`{ status: { $nin: ["inactive", "banned"] } }`|

---

## **2. Logical Operators**

Used to combine multiple query conditions.

|Operator|Description|Example|
|---|---|---|
|`$and`|Joins multiple conditions (all must be true)|`{ $and: [ { age: { $gt: 18 } }, { age: { $lt: 30 } } ] }`|
|`$or`|Matches documents that satisfy at least one condition|`{ $or: [ { age: { $lt: 18 } }, { age: { $gt: 65 } } ] }`|
|`$nor`|Matches documents that fail both conditions|`{ $nor: [ { status: "active" }, { age: { $gt: 50 } } ] }`|
|`$not`|Inverts the effect of a query expression|`{ age: { $not: { $gt: 18 } } }`|

---

## **3. Element Operators**

Check for the existence or type of a field.

|Operator|Description|Example|
|---|---|---|
|`$exists`|Checks if a field exists|`{ phone: { $exists: true } }`|
|`$type`|Matches field values based on their BSON type|`{ age: { $type: "int" } }`|

---

## **4. Evaluation Operators**

Used for advanced queries like regular expressions and expressions.

| Operator      | Description                                    | Example                                          |
| ------------- | ---------------------------------------------- | ------------------------------------------------ |
| `$expr`       | Allows aggregation expressions in queries      | `{ $expr: { $gt: ["$spent", "$budget"] } }`      |
| `$jsonSchema` | Validates documents against a schema           | `{ $jsonSchema: { required: ["name", "age"] } }` |
| `$mod`        | Matches numbers divisible by a given divisor   | `{ age: { $mod: [5, 0] } }`                      |
| `$regex`      | Matches strings using a regular expression     | `{ name: { $regex: "^A", $options: "i" } }`      |
| `$text`       | Performs a text search                         | `{ $text: { $search: "MongoDB" } }`              |
| `$where`      | Matches documents using JavaScript expressions | `{ $where: "this.age > 18" }`                    |

---

## **5. Array Operators**

Used to filter documents based on array values.

|Operator|Description|Example|
|---|---|---|
|`$all`|Matches arrays containing all specified elements|`{ tags: { $all: ["mongodb", "database"] } }`|
|`$elemMatch`|Matches documents where an array field satisfies multiple conditions|`{ grades: { $elemMatch: { score: { $gt: 80 }, subject: "math" } } }`|
|`$size`|Matches arrays with a specified number of elements|`{ tags: { $size: 3 } }`|

---

## **6. Projection Operators**

Used to include or exclude fields from query results.

|Operator|Description|Example|
|---|---|---|
|`$`|Positional projection for array elements|`{ "grades.$": 1 }`|
|`$elemMatch`|Projects only the matching array element|`{ grades: { $elemMatch: { score: { $gt: 80 } } } }`|
|`$meta`|Retrieves metadata from a text search query|`{ score: { $meta: "textScore" } }`|
|`$slice`|Limits the number of array elements returned|`{ comments: { $slice: 2 } }`|

---

## **7. Aggregation Operators**

Used in aggregation pipelines (`db.collection.aggregate()`).

|Operator|Description|Example|
|---|---|---|
|`$match`|Filters documents|`{ $match: { age: { $gt: 18 } } }`|
|`$group`|Groups documents by a specified field|`{ $group: { _id: "$status", count: { $sum: 1 } } }`|
|`$project`|Shapes the returned documents|`{ $project: { name: 1, age: 1 } }`|
|`$sort`|Sorts documents|`{ $sort: { age: -1 } }`|
|`$limit`|Limits the number of documents returned|`{ $limit: 10 } }`|
|`$skip`|Skips a specified number of documents|`{ $skip: 5 } }`|
|`$unwind`|Deconstructs an array field|`{ $unwind: "$tags" }`|
|`$lookup`|Performs left outer joins|`{ $lookup: { from: "orders", localField: "_id", foreignField: "userId", as: "orderDetails" } }`|

---

## **8. Update Operators**

Used in `update()` and `updateMany()` methods.

|Operator|Description|Example|
|---|---|---|
|`$set`|Sets the value of a field|`{ $set: { status: "active" } }`|
|`$unset`|Removes a field from a document|`{ $unset: { status: "" } }`|
|`$inc`|Increments a field by a value|`{ $inc: { age: 1 } }`|
|`$mul`|Multiplies a field by a value|`{ $mul: { salary: 1.1 } }`|
|`$rename`|Renames a field|`{ $rename: { oldName: "newName" } }`|
|`$addToSet`|Adds an element to an array if it doesn't exist|`{ $addToSet: { tags: "mongodb" } }`|
|`$pop`|Removes the first or last element from an array|`{ $pop: { tags: -1 } }`|
|`$pull`|Removes elements that match a condition|`{ $pull: { tags: "mongodb" } }`|
|`$push`|Adds an element to an array|`{ $push: { tags: "newTag" } }`|

---

## **9. Miscellaneous Operators**

Additional operators for special cases.