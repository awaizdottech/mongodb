# aggregation pipelines

it has stages
defining it looks like
[
{first stage does something to data},
{second stage does something to data given by previous/first stage},...
]

## questions

1. how many users are active

```
[
  {
    $match: {
      isActive: true,
    },
  },
  {
    $count: "string",
  },
]
```

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

```
[
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
]
```

4. which coutnry has the highest number of users

```
[
  {
    $group: {
      _id: "$company.location.country",
      ucount: {
        $sum: 1,
      },
    },
  },
  {
    $sort: {
      ucount: -1
    }
  },{
    $limit: 1
  }
]
```

5. list all unique eyecolors - just group it!
6. avg no of tags per user

```
[
  {
    $unwind: "$tags", // it creates as many duplicate documents as the number of elements in the array then have only the array feild unique with an element value for it
  },
  {
    $group: {
      _id: "$_id",
      totalTags: {
        $sum: 1,
      },
    },
  },{
    $group: {
      _id: null,
      avgTags: {
        $avg: "$totalTags"
      }
    }
  }
]
```

this was one way, another is

```
[
  {
    $addFields: {
      totalTags: {
        $size:{$ifNull:["$tags",[]]} // if tags feild doesnt exist it'll be treated as an empty array
      }
    }
  },
  {
    $group: {
      _id: null,
      avgTags: {
        $avg: "$totalTags"
      }
    }
  }
]
```

7. how many users have 'enim' as one of their tags

```
[
  {
    $match:{
      tags:'enim'
    }
  },
  {
    $count:"usersWithEnimTags"
  }
]
```

8. what are the names & age of users who are inactive & has 'velit' in their tag

```
[
  {
    $match: {
      isActive: false,tags:"velit"
    },
  },
  {
    $project: {
      name:1,
      age:1
    }
  },
]
```

9. how many users have mobile numbers starting with '+1(940)'

```
[
  {
    $match: {
      "company.phone":/^\+1 \(940\)/
    },
  },{
    $count: 'usersWithSpecifiedMobileNumber'
  }
]
```

9. who has registered most recently

- pretty simple

10. categorise users by their fav fruit

```
[
  {
    $group: {
    _id: "$favoriteFruit",
    users: {
      $push: "$name"
    	}
  	}
	}
]
```

11. how many suers have 'ad' as second tag

```
[
  {
    $match: {
      "tags.1":"ad"
    }
	},{
    $count: 'secondTagAd'
  }
]
```

12. how many users have both 'enim' & 'id' as tags

```
[
  {
    $match: {
      tags:{
        $all:['enim','id']
      }
    }
	},...
]
```

13. list all companies located in usa with their corresponding user count

```
[
  {
    $match: {
      "company.location.country": "USA",
    },
  },
  {
    $group: {
      _id: "$company.title",
      USAusers: {
        $sum: 1,
      },
    },
  },
]
```

## lookup

```
[
  {
    $lookup: {
      from: "authors",
      localField: "author_id",
      foreignField: "_id",
      as: "author_details"
    }
  },
  {
    $addFields: {
      author_details: {
        $first:"$author_details" // or we can also use $arrayElemAt:["$author_details",0]

      }
    }
  }
]
```
