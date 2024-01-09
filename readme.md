Concept

> $match

```json
// data
[
    { "_id": 1, "title": "Book 1", "author": "Author A", "genre": "Fiction", "price": 20 },
    { "_id": 2, "title": "Book 2", "author": "Author B", "genre": "Non-Fiction", "price": 15 },
    { "_id": 3, "title": "Book 3", "author": "Author A", "genre": "Fiction", "price": 25 },
    { "_id": 4, "title": "Book 4", "author": "Author C", "genre": "Non-Fiction", "price": 18 },
    { "_id": 5, "title": "Book 5", "author": "Author B", "genre": "Fiction", "price": 22 }
]
```

1) match title and author

```json
// query
[
  {
    $match: {
        "title":"Book 1",
        "author":"Author A"
    }
  }
]
```


2. find title Book 1 or Book 3

```json
// query
[
  {
    $match: {
      $or: [
        { "title": "Book 1" },
        { "title": "Book 3" },
      ],
    },
  },
]
```

3. fetch all data except Author C
```json
{
    $match: {
        "author": {
            "$ne": "Author C"
        }
    }
}
```

>  $group

```
{
    $group: {
        _id: <expression>,   // Grouping criteria
        field1: { <accumulator1>: <expression1> },
        field2: { <accumulator2>: <expression2> },
        // ...
    }
}
```
* _id: Specifies the field or expression by which the documents will be grouped. If you set _id: null, it groups all documents into a single group.

* field1, field2, etc.: These are the fields you want to include in the result, either as the grouping criteria or the result of aggregate operations.

* accumulator1, accumulator2, etc.: These are aggregate functions or operators applied to the grouped data. Common accumulators include $sum, $avg, $min, $max, and $addToSet, among others.

* expression1, expression2, etc.: These are the fields or expressions used as input for the corresponding accumulator.


```json
// query
db.books.aggregate([
    {
        $group: {
            _id: "$author",
            totalPrice: { $sum: "$price" },
            averagePrice: { $avg: "$price" }
        }
    }
])

```

```json
// result
[
    { "_id": "Author A", "totalPrice": 45, "averagePrice": 22.5 },
    { "_id": "Author B", "totalPrice": 37, "averagePrice": 18.5 },
    { "_id": "Author C", "totalPrice": 18, "averagePrice": 18 }
]

```


> $lookup

It allows you to retrieve documents from one collection that match the specified conditions and include them as an array in the output documents of the primary (input) collection.

```json
{
    $lookup: {
        from: "foreignCollection",
        localField: "localField",
        foreignField: "foreignField",
        as: "outputArray"
    }
}

```

* from: Specifies the name of the foreign collection to perform the join with.

* localField: Specifies the field from the input documents (primary collection) that will be used for matching.

* foreignField: Specifies the field from the foreign documents (specified collection) that will be used for matching.

* as: Specifies the name of the new array field that will contain the joined documents in the output.

Example: 
```json
// orders collection
[
    { "_id": 1, "orderNumber": "ORD001", "productID": 101 },
    { "_id": 2, "orderNumber": "ORD002", "productID": 102 },
    { "_id": 3, "orderNumber": "ORD003", "productID": 101 }
]

// products collection
[
    { "_id": 101, "productName": "Laptop", "price": 1200 },
    { "_id": 102, "productName": "Smartphone", "price": 800 }
]

```


```json
// query

db.orders.aggregate([
    {
        $lookup: {
            from: "products",
            localField: "productID",
            foreignField: "_id",
            as: "productDetails"
        }
    }
])

```

```json
// result

[
    { "_id": 1, "orderNumber": "ORD001", "productID": 101, "productDetails": [{ "_id": 101, "productName": "Laptop", "price": 1200 }] },
    { "_id": 2, "orderNumber": "ORD002", "productID": 102, "productDetails": [{ "_id": 102, "productName": "Smartphone", "price": 800 }] },
    { "_id": 3, "orderNumber": "ORD003", "productID": 101, "productDetails": [{ "_id": 101, "productName": "Laptop", "price": 1200 }] }
]

```


> $count

The $count stage in MongoDB's aggregation pipeline is used to count the number of documents that pass through the pipeline. It is often used at the end of the aggregation pipeline to get a count of the documents that match certain criteria.


```json
// data

[
    { "_id": 1, "name": "Alice", "score": 85 },
    { "_id": 2, "name": "Bob", "score": 92 },
    { "_id": 3, "name": "Charlie", "score": 78 },
    { "_id": 4, "name": "David", "score": 95 },
    { "_id": 5, "name": "Eva", "score": 88 }
]

```

```json
// query 

db.students.aggregate([
    {
        $match: {
            score: { $gt: 90 }
        }
    },
    {
        $count: "highScorersCount"
    }
])

```


```json
// result

[
    { "highScorersCount": 2 }
]

```

> $sum


The $sum operator in MongoDB's aggregation framework is used to calculate the sum of numeric values for documents in a collection. It is commonly used in the $group stage to perform aggregation operations on grouped documents.

```json
[
    { "_id": 1, "product": "A", "quantity": 10, "price": 5 },
    { "_id": 2, "product": "B", "quantity": 5, "price": 10 },
    { "_id": 3, "product": "A", "quantity": 8, "price": 8 },
    { "_id": 4, "product": "B", "quantity": 12, "price": 7 },
    { "_id": 5, "product": "A", "quantity": 15, "price": 6 }
]

```

```json
// query

db.sales.aggregate([
    {
        $group: {
            _id: "$product",
            totalQuantity: { $sum: "$quantity" }
        }
    }
])

```

```json
// result 

[
    { "_id": "A", "totalQuantity": 33 },
    { "_id": "B", "totalQuantity": 17 }
]

```

> $addFields

The $addFields stage in MongoDB's aggregation pipeline is used to add new fields to documents.

```javascript
{
    $addFields: {
        newField: <expression>,
        anotherNewField: <expression>,
        // ... additional fields
    }
}
```


```json
// data

[
    { "_id": 1, "name": "Alice", "mathScore": 90, "englishScore": 85 },
    { "_id": 2, "name": "Bob", "mathScore": 75, "englishScore": 92 },
    { "_id": 3, "name": "Charlie", "mathScore": 88, "englishScore": 78 }
]
```

```json
// query

db.students.aggregate([
    {
        $addFields: {
            totalScore: { $add: ["$mathScore", "$englishScore"] }
        }
    }
])
```

```json
// result 

[
    { "_id": 1, "name": "Alice", "mathScore": 90, "englishScore": 85, "totalScore": 175 },
    { "_id": 2, "name": "Bob", "mathScore": 75, "englishScore": 92, "totalScore": 167 },
    { "_id": 3, "name": "Charlie", "mathScore": 88, "englishScore": 78, "totalScore": 166 }
]

```

> $project 

```javascript
{
    $project: {
        field1: 1,  // Include field1
        field2: 1,  // Include field2
        // ... additional fields to include

        field3: 0,  // Exclude field3
        field4: 0   // Exclude field4
        // ... additional fields to exclude
    }
}
```


```json
// data

[
    { "_id": 1, "name": "Laptop", "category": "Electronics", "price": 1200 },
    { "_id": 2, "name": "Bookshelf", "category": "Furniture", "price": 150 },
    { "_id": 3, "name": "Smartphone", "category": "Electronics", "price": 800 }
]
```


```json
// query

db.products.aggregate([
    {
        $project: {
            _id: 0,  // Exclude _id
            name: 1, // Include name
            category: 1 // Include category
        }
    }
])
```

```json
// result 

[
    { "name": "Laptop", "category": "Electronics" },
    { "name": "Bookshelf", "category": "Furniture" },
    { "name": "Smartphone", "category": "Electronics" }
]

```


> $unwind

The $unwind stage in MongoDB's aggregation pipeline is used to deconstruct an array field from the input documents, creating a new document for each element in the array. 

```javascript
{
    $unwind: {
        path: "$arrayField",
        includeArrayIndex: "indexField",
        preserveNullAndEmptyArrays: <boolean>
    }
}

```

* path: The field path to the array you want to unwind.

* includeArrayIndex (optional): If specified, a new field will be added to each unwound document to store the index of the array element.

* preserveNullAndEmptyArrays (optional): If set to true, the $unwind stage will include documents where the array field is null or an empty array. If set to false or omitted, such documents will be excluded.

```json
// data

[
    { "_id": 1, "name": "Alice", "scores": [85, 92, 78] },
    { "_id": 2, "name": "Bob", "scores": [75, 88, 90] },
    { "_id": 3, "name": "Charlie", "scores": [95, 80, 87] }
]

```

```json
// query 

db.students.aggregate([
    {
        $unwind: {
            path: "$scores",
            includeArrayIndex: "examNumber"
        }
    }
])

```


```json
// result

[
    { "_id": 1, "name": "Alice", "scores": 85, "examNumber": 0 },
    { "_id": 1, "name": "Alice", "scores": 92, "examNumber": 1 },
    { "_id": 1, "name": "Alice", "scores": 78, "examNumber": 2 },
    { "_id": 2, "name": "Bob", "scores": 75, "examNumber": 0 },
    { "_id": 2, "name": "Bob", "scores": 88, "examNumber": 1 },
    { "_id": 2, "name": "Bob", "scores": 90, "examNumber": 2 },
    { "_id": 3, "name": "Charlie", "scores": 95, "examNumber": 0 },
    { "_id": 3, "name": "Charlie", "scores": 80, "examNumber": 1 },
    { "_id": 3, "name": "Charlie", "scores": 87, "examNumber": 2 }
]

```

> $sort

```javascript
{
    $sort: {
        field1: 1,  // ascending order
        field2: -1, // descending order
        // ... additional fields for sorting
    }
}

```


```json
// data

[
    { "_id": 1, "name": "Laptop", "category": "Electronics", "price": 1200 },
    { "_id": 2, "name": "Bookshelf", "category": "Furniture", "price": 150 },
    { "_id": 3, "name": "Smartphone", "category": "Electronics", "price": 800 }
]
```


```json
// query

db.products.aggregate([
    {
        $sort: {
            price: -1
        }
    }
])
```

```json
// result

[
    { "_id": 1, "name": "Laptop", "category": "Electronics", "price": 1200 },
    { "_id": 3, "name": "Smartphone", "category": "Electronics", "price": 800 },
    { "_id": 2, "name": "Bookshelf", "category": "Furniture", "price": 150 }
]
```

> $limit

It limits the output to a specified number of documents.

```json
 // data

 [
    { "_id": 1, "name": "Alice", "score": 85 },
    { "_id": 2, "name": "Bob", "score": 92 },
    { "_id": 3, "name": "Charlie", "score": 78 },
    // ... (more documents)
]
```



```json
// query 

db.students.aggregate([
    {
        $limit: 2
    }
])
```


```json
// result

[
    { "_id": 1, "name": "Alice", "score": 85 },
    { "_id": 2, "name": "Bob", "score": 92 }
]
```


> $add

```json
// data

[
    { "_id": 1, "name": "Laptop", "price": 1200, "tax": 120 },
    { "_id": 2, "name": "Bookshelf", "price": 150, "tax": 15 },
    { "_id": 3, "name": "Smartphone", "price": 800, "tax": 80 }
]

```

```json
// query

db.products.aggregate([
    {
        $project: {
            name: 1,
            totalCost: { $add: ["$price", "$tax"] }
        }
    }
])
```


```json
// result

[
    { "_id": 1, "name": "Laptop", "totalCost": 1320 },
    { "_id": 2, "name": "Bookshelf", "totalCost": 165 },
    { "_id": 3, "name": "Smartphone", "totalCost": 880 }
]

```

> $size

The $size operator is used in the aggregation pipeline to determine the number of elements in an array. When applied in the aggregation pipeline, it returns the size of the specified array.

```javascript
// data

{
  "_id": 1,
  "items": ["item1", "item2", "item3"]
}
```

```json
// query

db.orders.aggregate([
  {
    $project: {
      _id: 1,
      numberOfItems: { $size: "$items" }
    }
  }
])
```

```json
// result

{
  "_id": 1,
  "numberOfItems": 3
}
```


* TIPS

1) Always think TWICE when you are using operators like "$unwind' because it is very costly operator because it creates multiple copies of a same documents. Try to think some other possible solution. Some costly operators are: "$lookup", "$unwind", "$facet", etc.
2) It is a best practice to use $project after the $group stage in the pipeline because "by default, mongodb return a BSON object and not a JSON object" and BSON object have more properties than a JSON object. So using the $project, you can get the JSON object You can study this point for more knowledge.
3) If the size of your document is big, then always use ".allowDiscUse(true)" in your aggregation pipeline.
4) When dealing with larger chunks of data, use "cursor" and "batchSize" approach.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++





Q1. find all the user that are active?

```json
[
  {
    // Stage 1: Match documents where '"isActive"' is true
    "$match": {
      "isActive": true
    }
  },
  {
    // Stage 2: Count the number of matched documents and create an alias 'activeUsers'
    "$count": "activeUsers"
  }
]

//  "activeUsers": 516
```

Q2. what is the average age of all users?

```json
[
  {
    // Stage 1: Group all documents into a single group (null)
    "$group": {
      "_id": "null", // The grouping key is set to null, meaning all documents will be grouped together
      "averageAge": {
        // Calculate the average of the 'age' field for the grouped documents
        "$avg": "$age"
      }
    }
  }
]
```

Q3. List top 5 most common favorite popular fruits among the users?

```json
[
  {
    // Stage 1: Group documents based on the 'favoriteFruit' field
    "$group": {
      "_id": "$favoriteFruit", // Group by the 'favoriteFruit' field
      "count": {
        "$sum": 1 // Calculate the count for each group
      }
    }
  },
  {
    // Stage 2: Sort the results in descending order based on the count
    "$sort": {
      "count": -1 // Sort by the 'count' field in descending order
    }
  },
  {
    // Stage 3: Limit the results to the top (most frequent) document
    "$limit": 1
  }
]
```

Q4. find the total number of males and female?

```json
[
  {
    // Stage 1: Group documents based on the 'gender' field
    "$group": {
      "_id": "$gender", // Group by the 'gender' field
      "number": {
        "$sum": 1 // Calculate the count for each group
      }
    }
  }
]
```

Q5. which country has the highest number of registered users?

```json
[
  {
    // Stage 1: Group documents based on the 'company.location.country' field
    "$group": {
      "_id": "$company.location.country", // Group by the 'company.location.country' field
      "count": {
        "$sum": 1 // Calculate the count for each group
      }
    }
  },
  {
    // Stage 2: Sort the results in descending order based on the count
    "$sort": {
      "count": -1 // Sort by the 'count' field in descending order
    }
  },
  {
    // Stage 3: Limit the results to the top 2 documents (countries with the highest counts)
    "$limit": 2
  }
]
```

Q6. List all unique eye colors present in the collection?

```json
[
  {
    "$group": {
      "_id": "$eyeColor"
    }
  }
]
```

Q7. what is average number of "tags" per user?

```json
[
  {
    // Stage 1: Unwind the '"tags"' array, creating a separate document for each tag.
    //  This is useful when you have documents with arrays, and you want to perform operations on each element of the array separately.
    "$unwind": {
      "path": "$tags", // Unwind the '"tags"' array field
    },
  },
  {
    // Stage 2: Group the documents by their original '_id', counting the number of "tags" per document
    "$group": {
      "_id": "$_id", // Group by the original '_id' to keep track of each document
      "countNo": {
        "$sum": 1, // Count the number of "tags" for each document
      },
    },
  },
  {
    // Stage 3: Group the documents to calculate the average count of "tags" across all documents
    "$group": {
      "_id": null, // Group all documents together (no specific key)
      "counting": {
        "$avg": "$countNo", // Calculate the average count of "tags"
      }
    }
  }
]

```

```json
[
  {
    // Stage 1: Add a new field 'avgTag' representing the size of the '"tags"' array
    "$addFields": {
      "avgTag": {
        // Calculate the size of the '"tags"' array, handling cases where '"tags"' may be null or undefined
        "$size": { "$ifNull": ["$tags", []] },
      },
    },
  },
  {
    // Stage 2: Group the documents to calculate the average of the 'avgTag' field
    "$group": {
      "_id": null, // Group all documents together (no specific key)
      "avgOftags": {
        // Calculate the average value of the 'avgTag' field across all documents
        "$avg": "$avgTag",
      },
    },
  },
]
```

Q8. how many users have "tags" as enim?

```json
[
  {
    "$match" : {
       "tags": "enim"
    }
  },
  {
    "$count":"userwithenimtags"
  }
]
```

Q9. what are the age and name of the user that are not active and has "tags" as velit?

```json
[
  {
    // Stage 1: Match documents where both conditions are true
    "$match": {
      "$and": [
        { "tags": "velit" }, // Condition 1: Match documents where 'tags' is "velit"
        { "isActive": false } // Condition 2: Match documents where 'isActive' is false
      ]
    }
  },
  {
    // Stage 2: Project only the 'name' and 'age' fields
    "$project": {
      "name": 1, // Include the 'name' field in the output
      "age": 1   // Include the 'age' field in the output
    }
  }
]

```

```json
[
  {
    // Stage 1: Match documents where both conditions are true
    "$match": {
      "tags": "velit", // Condition 1: Match documents where 'tags' is "velit"
      "isActive": false // Condition 2: Match documents where 'isActive' is false
    }
  },
  {
    // Stage 2: Project only the 'name' and 'age' fields
    "$project": {
      "name": 1, // Include the 'name' field in the output
      "age": 1 // Include the 'age' field in the output
    }
  }
]
```

Q10. how many users have a phone number starting with '+1 (940)'?

```json
[
  {
    "$match" : {
       "company.phone": /^\+1 \(940\)/
    }
  },
  {
    "$count":"userWithSpecialPhoneNo"
  }
]
```

Q11. who has registered the most recently?

```json
[
  // Sort the documents in descending order based on the 'registered' field.
  {
    "$sort": {
      "registered": -1,
    },
  },
  // Limit the result set to the first 2 documents after sorting.
  {
    "$limit": 2,
  },
  // Project only specific fields ('name', 'registered', 'favoriteFruit') in the output.
  {
    "$project": {
      "name": 1,
      "registered": 1,
      "favoriteFruit": 1
    }
  },
]

```

Q12. Categorize user based on favorite fruits?

```json
[
  // Group documents by 'favoriteFruit', and create an array 'users' with corresponding 'name' values.
  {
    "$group": {
      "_id": "$favoriteFruit",
      "users": {
        "$push": "$name",
      },
    },
  },
  // Add a new field 'count' with the size of the 'users' array using $size operator.
  {
    "$addFields": {
      "count": { "$size": "$users" } // Calculate the size of the 'users' array.
    }
  }
]

```

Q13. how many users has tag "ad" as 2 second value?

```json
[
  {
    "$match" : {
       "tags.1": "ad"
    } 
  }
]
```

Q14. find users who has both enim and id as their tags?

```json
[
  {
    "$match": {
      "tags": { "$all" : ["enim", "id"] }
    },
  },
]
```

Q15. List all the companies located in the USA with their corresponding user account?

```json
[
  {
    "$match": {
      "company.location.country": "USA",
    },
  },
  {
    "$group": {
      "_id": "$company.title",
      "userCount": { "$sum": 1 },
    },
  },
]
```