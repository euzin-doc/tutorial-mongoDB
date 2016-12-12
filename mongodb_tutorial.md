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
		| RDBMS	 | MongoDB           |
		|--------|-------------------|
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
collection 의 모든 데이터 
```
db.COLLECTION_NAME.find()
```


### 

## MongoDB - sorting / indexing / aggregation
## MongoDB - Replication / Sharding / backup
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








