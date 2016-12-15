# MongoDB Tutorial

[TOC]

## MongoDB
- 몽고디비는 NoSQL 오픈소스 document database 
- written in C++
	- 특징 
		- 문서지향 
		- 스키마 x
		- 스케일아웃 
		- 인덱싱 
		- 집계 파이프라인 지원 
		- 컬렉션 타입 : ttl, 고정크기 등.
		- 조인 x, 트랜잭션 x
	- 쉬운 접근
		- document == row 
		- collection == table
        
		| RDBMS | MongoDB|
		|---|---|
		|Database	|Database|
		|Table	|Collection|
		|Tuple/Row	|Document|
		|column	|Field|
		|Table Join	|Embedded Documents|
		|Primary Key	|Primary Key (Default key _id provided by mongodb itself)|
		| Database Server and Client | |
		|Mysqld/Oracle|	mongod|
		|mysql/sqlplus|	mongo|
        
	- Warning
		- 트랜잭션은 구현하는것
		- 문서의 키:밸류 는 정열 되어 있다. ex) {x:2,y:1} <> {y:1,x:2}
		- 데이터형. ex) 100 <> "100"
		- 스키마 없음 = 강제로 지키는가 자율적으로 지키는가 (최소한의 규칙을 어떻게 지키면서 개발할지)

## MongoDB - create / drop database 

``` 
use DATABASE_NAME 
```
지정한 데이터베이스이름이 존재 하면 switched, 없으면 생성한다.
```
>db
mydb
>show dbs
local     0.78125GB
test      0.23012GB
```
현재 접속중인 데이터베이스 이름을 리턴한다. 
생성되어 있는 데이터베이스 리스트 
```
db.dropDatabase()
```
현재 선택된 데이터베이스를 drop.

## MongoDB - create/drop collection
- createCollection()
```
db.createCollection(name, options)
> show collections
mycollection
yourcollection
..
```

- options

 |Field	|Type	|Description|
 |-------|-------|-----------|
 |capped	|Boolean	|(Optional) If true, enables a capped collection. Capped collection is a fixed size collection that automatically overwrites its oldest entries when it reaches its maximum size. If you specify true, you need to specify size parameter also. |
 |autoIndexID	|Boolean	|(Optional) If true, automatically create index on _id field.s Default value is false.|
 |size	|number	|(Optional) Specifies a maximum size in bytes for a capped collection. If capped is true, then you need to specify this field also.|
 |max	|number	|(Optional) Specifies the maximum number of documents allowed in the capped collection.|
 
- mongodb 에서는 document 를 insert 하는 순간 collection 은 자동으로 생성되므로, create collection 이 필요 없다.
```
>db.mycollection.insert({"name" : "euzin"})
>show collections
mycollection
testcollection
yourcollection
>
>db.createCollection("mycol", { capped : true, autoIndexID : true, size : 
   6142800, max : 10000 } )
{ "ok" : 1 }
>
```

- db.COLLECTION_NAME.drop()
```
>db.mycollection.drop()
true
>
```


## MongoDB - datatypes 
- MongoDB 가 지원하는 데이터 타입

| 데이터타입 | 설명 |
|---------|-----|
|String | String in MongoDB must be UTF-8 valid.|
|Integer | This type is used to store a numerical value. Integer can be 32 bit or 64 bit depending upon your server.|
|Boolean |− This type is used to store a boolean (true/ false) value.|
|Double |− This type is used to store floating point values.|
|Min/ Max keys |− This type is used to compare a value against the lowest and highest BSON elements.|
|Arrays |− This type is used to store arrays or list or multiple values into one key.|
|Timestamp |− ctimestamp. This can be handy for recording when a document has been modified or added.|
|Object |− This datatype is used for embedded documents.|
|Null |− This type is used to store a Null value.|
|Symbol |− This datatype is used identically to a string; however, it's generally reserved for languages that use a specific symbol type.|
|Date |− This datatype is used to store the current date or time in UNIX time format. You can specify your own date time by creating object of Date and passing day, month, year into it.|
|Object ID |− This datatype is used to store the document’s ID.|
|Binary data |− This datatype is used to store binary data.|
|Code | This datatype is used to store JavaScript code into the document.|
|Regular expression |− This datatype is used to store regular expression.|

## MongoDB - insert / query / update / delete / projection / limiting
### insert()
- insert() or save() to insert data into MongoDB
```
db.COLLECTION_NAME.insert(document)
```
_id 는 12  bytes hexadecimal number 로 모든 도큐먼트에 유니크하게 생성된다. 
```
_id: ObjectId(4 bytes timestamp, 3 bytes machine id, 2 bytes process id, 
   3 bytes incrementer)
```
여러건 insert 하기 
```
>db.mycollection.insert([
   {
      name: 'MongoDB', 
      description: 'nosql database',
      tags: ['mongodb', 'database', 'NoSQL'],
      likes: 100
   },
   {
      name: 'mysql', 
      description: 'rdbms',
      tags: ['mongodb', 'database', 'NoSQL'],
      likes: 20, 
      comments: [	
         {
            user:'euzin',
            message: 'awesome!',
            dateCreated: new Date(2013,11,10,2,35),
            like: 0 
         }
      ]
   }
])
```

### find()

- collection 의 데이터 조회 method 
- find(query, projection)
	- query : document - 조건문 
	- projection : field 를 지정 
	- return cursor : find() 메소드는 기준에 맞는 document 를 선택하여 cursor 를 반환한다. cursor 는 쿼리 요청의 결과값을 가르키는 pointer. cursor 객체를 통해서 데이터 수를 제한 할 수 있고 sort 도 한다. 10분간 사용되지 않으면 expire 된다.
- 
```
db.COLLECTION_NAME.find()      -- 전체 패치
db.COLLECTION_NAME.findone()   -- 1건 패치
```
- where 절 

|operation | syntax | example | rdb equivalent |
|----------|--------|---------|----------------|
| =        | {key:value} | db.COLLECTION_NAME.find({"name":"euzin"})| where name = 'euzin'|
|< | {key:{$lt:value}}|db.COLLECTION_NAME.find({"cnt":{$lt:10}})| where cnt < 10 |
|<=| {key:{$lte:value}}|db.COLLECTION_NAME.find({"cnt":{$lte:10}})| where cnt <= 10 |
|> | {key:{$gt:value}}|db.COLLECTION_NAME.find({"cnt":{$gt:10}})| where cnt > 10 |
|>=| {key:{$gte:value}}|db.COLLECTION_NAME.find({"cnt":{$gte:10}})| where cnt >= 10 |
|!=| {key:{$ne:value}}|db.COLLECTION_NAME.find({"cnt":{$ne:10}})| where cnt != 10 |

- $regex 연산자
```
{ <field>: { $regex: /pattern/, $options: '<options>' } }
{ <field>: { $regex: 'pattern', $options: '<options>' } }
{ <field>: { $regex: /pattern/<options> } }
{ <field>: /pattern/<options> }
```
	- option : 
		- i -> 대소문자 무시
		- m	-> 정규식에서 anchor(^) 를 사용 할 때 값에 \n 이 있다면 무력화
		- x	-> 정규식 안에있는 whitespace를 모두 무시
		- s	-> dot (.) 사용 할 떄 \n 을 포함해서 매치
```
정규식 article0[1-2] 에 일치하는 값이 title 에 있는 Document 조회
> db.articles.find( { “title” : /article0[1-2]/ } )
```

- and / or 
```
db.COLLECTION_NAME.find({$AND:[{key1:val1},{key2:val2}]})
db.COLLECTION_NAME.find({$OR:[{key1:val1},{key2:val2}]})
```

- projection
```
-- article의 title과 content 만 조회
> db.articles.find( { } , { “_id”: false, “title”: true, “content”: true } )
-- $slice 연산자:  Embedded Document 배열을 읽을때 limit 설정.
-- title 값이 article03 인 Document 에서 덧글은 하나만 보이게 출력
> db.articles.find( { “title”: “article03” }, { comments: { $slice: 1 } } )
```

- sort(), limit(), skip() : 출력을 제한 가공 한다.
```
cursor.sort(document)
ex) db.ARTICLE.find().sort({"_id":1})
--> _id 값으로 오름차순 정렬  (-1 : 내림차순)
cursor.limit(value)
ex) db.ARTICLE.find().limit(10)
--> ARTICLE document 의 10건만 보이기
cursor.skip(value)
ex) db.ARTICLE.skip(10) 
--> 10건 생략 하고 그다음 부터 출력
ex) order document 의 최신순으로 2건씩 표시.
    var showPage = function(page){
        return db.orders.find().sort( { "_id": -1 } ).skip((page-1)*2).limit(2);
        }
```


### update()
- update(), save() 
```
>db.COLLECTION_NAME.update(SELECTION_CRITERIA, UPDATED_DATA)
```

-  

### delete()






## MongoDB - Index
- create index 
```
db.COLLECTION_NAME.createIndex({KEY_NAME: 1}) -- -1 내림차순 1 오름차순
db.COLLECTION_NAME.createIndex({KEY_NAME:1, KEY2_NAME:1})  -- 복합 인덱스 생성
```

- index 속성
```
-- unique index
db.COLLECTION_NAME.createIndex({KEY_NAME:1},{unique:true}) 
-- partial index 
db.COLLECTION_NAME.createIndex(
    {KEY_NAME:1},
    { partialFilterExpression: { visitors: { $gt: 1000 } } }
  ) 
-- ttl : index KEY_NAME 이 date 타입 이고 ttl 을 1시간으로 설정 ttl 검사 thread 는 60초 마다 실행됨.
db.COLLECTION_NAME.createIndex(
    {KEY_NAME:1},
    { expireAfterSeconds: 3600 }
  )
```

- index 확인
```
db.COLLECTION_NAME.getIndexes()
```

- index 제거 
```
db.COLLECTION_NAME.dropIndex({KEY:1})
```



## MongoDB - Replication / Sharding / backup
### MongoDB Sharding
- 몽고DB 샤드는 3가지 구성요소를 가진다.  
	- shard : 분산된 물리적 데이터 set 이며, 각각의 샤드는 replica set으로 구성할 수 있다. 
	- mongos : 클라이언트 어플리케이션과 샤드 클러스터 간의 인터페이스를 제공 하며, query router 처럼 동작한다.
	- config servers : 메타데이터와 설정을 저장한다. 3.4 버전 부터는 config server 또한 replica set으로 구성해야만 한다.(CSRS)

- 샤드 구성 과정 

```
# prepare shards 

mongod --shardsvr --dbpath /mongo/sdb01 --port 10000
mongod --shardsvr --dbpath /mongo/sdb02 --port 20000
mongod --shardsvr --dbpath /mongo/sdb03 --port 30000

# config server start with repl set

mongod --configsvr --dbpath /mongo/cdb01 --port 40000
mongod --configsvr --replSet cdb --dbpath /mongo/cdb01 --port 40001
mongod --configsvr --replSet cdb --dbpath /mongo/cdb02 --port 40002

## in mongo shell (primary configdb)
rsconf = {_id:"cdb", members:[{_id : 0, host:"localhost:40001"}]}
rs.initiate(rsconf)
rs.add("localhost:40002")

rs.status()

# mongos start
mongos --configdb cdb/localhost:40001,localhost:40002    --chunkSize 1

## connect mongos 27017 (default)

$mongo
> use admin
db.runCommand({addshard:'localhost:10000'}); 
db.runCommand({addshard:'localhost:20000'}); 
db.runCommand({addshard:'localhost:30000'}); 


# mongos 에서 db 생성 후 data insert 

use common; 
mongos> for(var i=0; i<100000;i++) {
... db.user.insert({_id:i, age:42, name:'euzin'+i });
... }

--> sdb01 에만 데이터가 존재한다. 

# shard 설정 
mongos> db.runCommand({enablesharding:'common'});
mongos> db.runCommand({shardcollection:'common.user', key:{_id:1}});
   ```


## MongoDB - manage mongod process
- start mongod process
```
mongod --dbpath $DB_PATH (default /data/db)
         --port $PORT (default 27017)
         --fork --logpath $LOG_PATH (ex. /var/log/mongodb.log)
```

- stop mongod process
 	- clean shutdown 이 수행 되면 mongod 는 모든 지연된 작업을 완료 시키고,  데이터를 디스크에 flush 한후 모든 파일을 닫는다.   

	```
# in mongo shell 
use admin
db.shutdownServer()
# in linux shell
mongod --shutdown
```
> 어쩔 수 없이 kill process 할 경우 
> kill <mongod proceess id>
> kill -2 <mongod proceess id>
> kill -9 (SIGKILL) 하지 말것 이라고 공식적으로 경고하고 있다.

- Run-time Database Configuration 
	- https://docs.mongodb.com/v3.2/administration/configuration/

## MongoDB - mongostat / mongotop



## PyMongo
- python driver
- import pymongo

### connection
파이몽고의 mongoClient 를 사용
```
from pymongo import MongoClient
c = MongoClient()
or 
c = MongoClient('localhost', 27017)
or 
c = MongoClient('mongodb://localhost:27017/')
```








