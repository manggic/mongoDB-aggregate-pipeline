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
