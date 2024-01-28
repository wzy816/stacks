# mongodb

## basic

```bash
# install on centos
sudo vi /etc/yum.repos.d/mongodb-org-3.4.repo
# add
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=<https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/3.4/x86_64/>
gpgcheck=1
enabled=1
gpgkey=<https://www.mongodb.org/static/pgp/server-3.4.asc>

sudo yum install -y mongodb-org

# dump
./mongodump --host <host> --port <port> --username <username> --password <password> --out <output_dir> --db <db>

# restore
mongorestore --host <host> --port <port> --username <username> --password <password> <backup_dir>

# start service
sudo service mongod start

./mongod --maxConns 20000  --config /root/mongodb/bin/mongodb.conf
./mongod -f /root/mongodb/bin/mongodb.conf --shutdown
```

## shell

### basic

```js
// switch db
use my_db
```

### user & role

```js
// show users
db.getUsers();

// create
db.createUser({
  user: "new_user",
  pwd: "new_password",
  roles: [{ role: "dbOwner", db: "my_db" }],
  mechanisms: ["SCRAM-SHA-1"],
});
// updat
db.updateUser("user_name", {
  pwd: "password",
  roles: [{ role: "read", db: "my_db" }],
});

// create role
db.getSiblingDB("my_db").createUser({
  user: "new_user",
  pwd: "new_password",
  roles: [{ role: "readWrite", db: "my_db" }],
});

// get role
db.getRole("new_user");
db.getRoles();

// grant role
```

### db

```js
db.copyDatabase("source_db", "target_db");
```

### collection

```js
// create collection
db.createCollection("new_collection");

// batch rename collections with prefix
db.getCollectionInfos().forEach((col) =>
  db.getCollection(col.name).renameCollection("prefix" + col.name)
);

// batch delete collection with prefix
db.getCollectionInfos().forEach((col) => {
  if (col.name.startsWith("prefix")) {
    db.getCollection(col.name).drop();
  }
});

// copy from source_collection to target_collection
db.source_collection.find().forEach(function (v) {
  db.target_collection.insert(v);
});
```

### document

```js
// distinct
db.my_collection.distinct("key.nested_key",{$find})

view_date:{$gte: ISODate("2018-01-25T00:00:00.000Z"),$lt: ISODate("2018-03-23T00:00:00.000Z")}})

// update nested value
db.my_collection.updateOne({"key.nested_key":"value"},{$set:{"key.nested_key":"new_value"}})

// update new array item
db.my_collection.updateOne({"key":"value"},{$push:{"array_key":"new_item"}})

// update many
db.my_collection.updateMany({'key':'value'},{$set:{'key':'new_value'}});

// rename key
db.my_collection.updateMany({}, {$rename: {"old_key": "new_key"}})

// delete key
db.my_collection.updateMany({}, {$unset: {"key": ""}})

// delete key that not ends with 2
db.my_collection.deleteMany({'key':{$not:/2$/}})

// swap key
db.my_collection.find({}).forEach( function (doc) {
  var col1 = doc.col2;
  var col2 = doc.col1
  doc.col1 = col1;
  doc.col2 = col2
  db.my_collection.save(doc);
});
```

### aggregate operation

```js
// count total number of matched documents
db.my_collection.aggregate([
  {
    $match: {
      "time": {
        $gt: 1535760000000,
        $lt: 1538352000000,
      }
    }
  },
  {
    $count: "total"
  }
]).toArray();

// group by $user and accumulate each time
db.my_collection.aggregate([
  {
    $match: {
      "time": {
    		$gt: 1525363200000,
  			$lt: 15260832000000,
      }
    }
  },
  {
    $group: {
      _id: "$user",
      uniqueCount: {$addToSet: "$time"}
    }
  }
]).toArray()

// group by key and formatted time, sort
db.my_collection.aggregate([
  // use regex
  {
    $match: {
      "key":{$regex:/abc.*/,$options:'i'}
    }
  },
  // convert date
  {
    $project: {
      key:"key",
      date: { $add: [new Date(0), "$time"] }
    }
  },
  // formate date
  {
    $project: {
      key: "$key",
      day: {
        $dateToString: { format: " %Y-%m-%d", date: "$date" }
      }
    }
  },
  // group by day and key
  {
    $group: {
        _id: { day:"day", key:"key"},
        count: { $sum: 1 }
    }
  },
  // flatten
  {
    $project: {
      _id: 0,
      key: "$_id.key",
      day: "$_id.day",
      count: "$count"
    }
  },
  // sort
  {
    $sort: {
      "count": -1,
      "key": 1,
      "day": -1
    }
  }
]).toArray()

// lookup other collection
db.collection.aggregate([
  {
    $group:{
      _id: "$key",
      times: {$addToSet:"$time"}
    }
  },
  {
    $lookup:{
      from: "another_collection",
      localField: "_id",
      foreignField: "lookup_key",
      as: "another_key"
    }
  },
  {
    $project: {
      _id: 1,
      total: {$size: "$times"},
      key: {$arrayElemAt:["another_key", 0]},
    }
  }
]).toArray()

// print to csv
var cursor = db.my_collection.aggregate([
  ...
])
if (cursor && cursor.hasNext()) {
  print('key1,key2,key3,key4');
  while (cursor.hasNext()) {
    var item = cursor.next();
    print(tem.key1 + ',' + item.key2 + ',' + item.key3 + ',' + item.key4);
  }
}

// print to csv
db.my_collection.aggregate([
  ...
]).to_csv()

// find unique and dedup
var duplicates = []

db.getCollection('my_collection').aggregate([
{
  $group: {
    _id: {key1:"key1", key2:"key2"},
    uniqueIds: {$addToSet: "$_id"},
    count: {$sum: 1}
  }
},
{
  $match: {
    count: {"$gt": 1}
  }
}
])
.toArray()
.forEach(function(doc) {
  doc.uniqueIds.shift();
  doc.uniqueIds.forEach( function(dupId){
    duplicates.push(dupId);
  })
})
db.getCollection('my_collection').remove({_id:{$in:duplicates}})

// find all keys
var allKeys = {};
function traverse(obj,prefix){
    Object.keys(obj).forEach(function(k){
        var key = prefix + k
        if(allKeys.hasOwnProperty(key)){
            allKeys[key] = allKeys[key] +1
        }else{
            allKeys[key]=1
        }
        if (typeof obj[k] === 'object'){
            traverse(obj[k], key+".")
        }
    })
}
var docs = db.getCollection('my_collection').find({}).toArray()
for (let doc of docs){
    traverse(doc,'')
}
```

## ~/.robomongorc.js

```javascript
// DBCommandCursor is for aggregate, DBQuery is for .find()
DBCommandCursor.prototype.to_csv = DBQuery.prototype.to_csv = function (
  deliminator,
  textQualifier
) {
  var count = -1;
  var headers = [];
  var data = {};
  var cursor = this;
  deliminator = deliminator == null ? "," : deliminator;
  textQualifier = textQualifier == null ? '"' : textQualifier;
  while (cursor.hasNext()) {
    var array = new Array(cursor.next());
    count++;
    for (var index in array[0]) {
      if (headers.indexOf(index) == -1) {
        headers.push(index);
      }
    }
    for (var i = 0; i < array.length; i++) {
      for (var index in array[i]) {
        data[count + "_" + index] = array[i][index];
      }
    }
  }
  var line = "";
  for (var index in headers) {
    line += textQualifier + headers[index] + textQualifier + deliminator;
  }
  line = line.slice(0, -1);
  print(line);
  for (var i = 0; i < count + 1; i++) {
    var line = "";
    var cell = "";
    for (var j = 0; j < headers.length; j++) {
      cell = data[i + "_" + headers[j]];
      if (cell == undefined) cell = "";
      line += textQualifier + cell + textQualifier + deliminator;
    }
    line = line.slice(0, -1);
    print(line);
  }
};
```

## Index

```js
db.collection.getIndexes();

db.collection.dropIndex("my_index_name");
```

## stat

```js
db.stats(1024);

// all collection stats
db.getCollectionNames().forEach((col) => {
  print(col);
  print(db.getCollection(col).stats(1024 * 1024 * 1024));
});
```
