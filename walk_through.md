# aggregation pipelines

it has stages
defining it looks like
[
{first stage does something to data},
{second stage does something to data given by previous/first stage},...
]

## questions

1. how many users are active

````[
  {
    $match: {
      isActive: true,
    },
  },
  {
    $count: "string",
  },
]```
````

2. avreage age of all users

```
[
  {
    $group: {
      _id: null,
      averageAge:{
        $avg:"$age" // avg is an accumulator, which means it builds on top of the previous value
      }
    }
  }
]
```

3. list top 5 common fruits among users

````[
  {
    $group: {
      _id: "$favoriteFruit",
      count:{
        $sum:1 // this states that whenever a fav fruit value is found increment 1 to the count value
      }
    } // whenever we want to operate on all users we need to group them
  },
  {
    $sort: {
      count: -1 // 1 is for ascending order
    }
  },
  {
    $limit: 2
  }
]```
````
