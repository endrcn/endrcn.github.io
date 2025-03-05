---
layout: post
title: "MongoDB Performans Artırma Yöntemleri"
author: Ender CAN
date: "2022-05-07"
categories: 
  - [ MongoDB ]
tags: 
  - "createindex"
  - "currentop"
  - "explain"
  - "getindexes"
  - "hint"
  - "mongodb"
  - "performance"
  - "performans"
---

MongoDB, genel olarak oldukça hızlı sorgulama yapmamıza imkan tanıyan bir veritabanıdır. Ancak sakladığı veriler büyüdükçe eğer doğru şekilde indexleme yapılmadıysa performansta ciddi olarak düşüş yaşanır. Bu makaledeki amacımız; mongodb performans sorunlarıyla karşılaştığımızda sorgu analizini nasıl yapabileceğimizi ve bu analiz sonucunda gerekli indexleri oluşturarak performansı nasıl artırabileceğimizi göstermektir.

## İşlemleri Görüntüleme - currentOp()

currentOp metodu, bize o anda gerçekleştirilen sorguların dökümünü verir. Bu döküm sayesinde uzun süren işlemleri rahatlıkla görebiliriz. MongoDB'de işlem yapacağımız veritabanına bağlandıktan sonra komutu aşağıdaki şekilde çalıştırabiliriz.

```js
db.currentOp();
```

Bu komut, veritabanını kullanan tüm kullanıcıların sorgularını gösterdiğinden, sınırlı yetkisi olan bir kullanıcı ile bu komutu çalıştırırsak aşağıdaki şekilde bir hata alırız:

```js
{
	"ok" : 0,
	"errmsg" : "not authorized on admin to execute command { currentOp: 1.0 }",
	"code" : 13,
	"codeName" : "Unauthorized"
}
```

Bu hatanın önüne geçmek ve sadece komutu çalıştırdığımız kullanıcının işlemlerini görmek için $ownOps parametresi ile sorgu atabiliriz.

```js
db.currentOp({$ownOps: true})
```

Yukarıdaki sorgunun çıktısı aşağıdaki gibi bir yapıda olacaktır:

```js
{
	"inprog" : [
		{
			"desc" : "conn60702",
			"threadId" : "140631540410112",
			"connectionId" : 60702,
			"client" : "xxx.xx.xx.xxx:123",
			"active" : true,
			"opid" : 159418722,
			"secs_running" : 5,
			"microsecs_running" : NumberLong(5139651),
			"op" : "query",
			"ns" : "example_db.comments",
			"query" : {
				"find" : "comments",
				"filter" : {
					"created_time" : {
						"$gte" : 1642777131
					},
					"feed_id" : "165826141939_10159869641436940"
				},
				"sort" : {
					"created_time" : -1
				},
				"projection" : {

				}
			},
			"numYields" : 31,
			"locks" : {
				"Global" : "r",
				"Database" : "r",
				"Collection" : "r"
			},
			"waitingForLock" : false,
			"lockStats" : {
				"Global" : {
					"acquireCount" : {
						"r" : NumberLong(64)
					}
				},
				"Database" : {
					"acquireCount" : {
						"r" : NumberLong(32)
					}
				},
				"Collection" : {
					"acquireCount" : {
						"r" : NumberLong(32)
					}
				}
			}
		},
		...
	],
	"ok" : 1
}
```

Bu çıktıda odaklanacağımız alanlar; _op_, _secs_running_, _query_, _opid_ ve _ns_ alanlarıdır. Bu alanların verdiği bilgiler:

**op**: İşlem tipini gösterir. _query_, _update_, _insert_ gibi.  
**secs_running**: İşlemin ne kadar süredir devam ettiğini saniye cinsinden gösterir.  
**query**: Yapılan sorgu ile ilgili bilgi verir.  
**opid**: İşlemin Id değeridir.  
**ns**: Hangi collection'da işlem yapıldığı bilgisini verir.

Bu alanlardan yola çıkarak uzun süren işlemlerle ilgili bilgi sahibi olarak gerekli müdahaleleri yapabiliriz. Ayrıca çıktı alanları ile ilgili filtreler de yaparak görünecek çıktıyı sınırlayabiliriz. Örneğin benim en sık kullandığım sorgu; 5 saniyeden uzun süren işlemleri gösteren sorgudur. Bu sayede index ihtiyacı olan işlemleri görmek kolaylaşır.

```js
db.currentOp({$ownOps: true, secs_running:{$gt:5}});
```

Şimdi bu sorguyla gerçek hayatta karşılaştığımız bir çıktı örneği üzerinden hangi işlemlerle sorunu çözeceğimize bakalım. Aşağıdaki işlem çıktısı, yaklaşık 20 milyon veri bulunan bir collection'a atılan sorgunun **721 saniyedir** devam ettiğini gösteriyor.

```js
{
    "desc": "conn58802",
    "threadId": "140631535146752",
    "connectionId": 58802,
    "client": "xxx.xx.xx.xxx:123",
    "active": true,
    "opid": 136801811,
    "secs_running": 721,
    "microsecs_running": NumberLong(721652790),
    "op": "query",
    "ns": "example_db.comments",
    "query": {
        "find": "comments",
        "filter": {
            "page_id": "165826141939",
            "from.id": "7271129512928941"
        },
        "sort": {},
        "projection": {}
    },
    "numYields": 32,
    "locks": {
        "Global": "r",
        "Database": "r",
        "Collection": "r"
    },
    "waitingForLock": false,
    "lockStats": {
        "Global": {
            "acquireCount": {
                "r": NumberLong(66)
            }
        },
        "Database": {
            "acquireCount": {
                "r": NumberLong(33)
            }
        },
        "Collection": {
            "acquireCount": {
                "r": NumberLong(33)
            }
        }
    }
}
```

Bu çıktıya bakarak, bir index oluşturmamız gerektiğini anlıyoruz. Index'i oluştururken kullanacağımız alanları görebilmek için, çıktının _ns_ ve _query_ alanlarına bakmamız gerekir. _ns_ alanı bize hangi collection'da bir index oluşturmamız gerektiğini, _query_ alanı da index'in kapsayacağı alanları görmemize yardımcı olacaktır. Yukarıdaki çıktıya göre oluşturmamız gereken index _comments_ tablosunda olmalı ve _page_id_ ile _from.id_ alanlarını kapsamalıdır. Index'i nasıl oluşturacağımız görelim.

## Index oluşturma - createIndex()

MongoDB'de index oluşturmak için kullanılan metod, createIndex metodudur. Eğer MongoDB'nin 3.0 versiyonundan eski bir versiyon kullanıyorsanız ensureIndex metodunu kullanmalısınız. Bu iki metod da aynı işlemi yapar.

Yukarıdaki örneğimizden yola çıkarak ihtiyacımız olan index'i nasıl oluşturacağımızı görelim.

```js
db.comments.createIndex({page_id:1, "from.id": 1});
```

Bu komutla basitçe bir index oluşturabiliriz. Eğer _page_id_ ya da _from.id_ alanları için tersten(Z-A, desc) bir sıralama yapılması gerekseydi bu durumda _1_ yerine _-1_ vermeliydik. Kısaca 1: Ascending, -1: Descending anlamına geliyor.

```js
db.comments.createIndex({page_id: -1, "from.id": 1});
```

Şimdi önemli bir konuya değinmemiz gerekiyor. **Index oluşturma sırasında veritabanımız kilitlenir.** Bu da eğer paralelde işlemlerimiz varsa can sıkıcı olacaktır. Bunun önüne geçmek için createIndex metoduna _background: true_ parametresini geçmemiz gerekir. Bu parametre, normal index oluşturmaya göre daha yavaş çalışır ancak herhangi bir kilitleme yaşatmaz.

```js
db.comments.createIndex({page_id: 1, "from.id": 1}, {background: true});
```

Son olarak, index oluşturma işlemlerinin currentOp() çıktısına bakalım.

```js
{
    "desc": "conn58779",
    "threadId": "140631569884928",
    "connectionId": 58779,
    "client": "xxx.xx.xx.xxx:123",
    "appName": "MongoDB Shell",
    "clientMetadata": {
        "application": {
            "name": "MongoDB Shell"
        },
        "driver": {
            "name": "MongoDB Internal Client",
                "version": "3.4.23"
        },
        "os": {
            "type": "Linux",
            "name": "CentOS Linux release 7.7.1908 (Core)",
            "architecture": "x86_64",
            "version": "Kernel 3.10.0-1062.1.2.el7.x86_64"
        }
    },
    "active": true,
    "opid": 136591823,
    "secs_running": 4713,
    "microsecs_running": NumberLong("4713029035"),
    "op": "command",
    "ns": "example_db.$cmd",
    "query": {
        "createIndexes": "comments",
        "indexes": [
            {
                "key": {
                    "page_id": 1,
                    "from.id": 1
                },
                "name": "page_id_1_from.id_1",
                "background": true
            }
        ]
    },
    "msg": "Index Build (background) Index Build (background): 12033322/21246295 56%",
    "progress": {
        "done": 12033322,
        "total": 21246295
    },
    "numYields": 113127,
        "locks": {
        "Global": "w",
            "Database": "w",
            "Collection": "w"
    },
    "waitingForLock": false,
    "lockStats": {
        "Global": {
            "acquireCount": {
                "r": NumberLong(113128),
                "w": NumberLong(113128)
            }
        },
        "Database": {
            "acquireCount": {
                "w": NumberLong(113128),
                "W": NumberLong(1)
            },
            "acquireWaitCount": {
                "w": NumberLong(1),
                "W": NumberLong(1)
            },
            "timeAcquiringMicros": {
                "w": NumberLong(144944),
                "W": NumberLong(275529)
            }
        },
        "Collection": {
            "acquireCount": {
                "w": NumberLong(113128)
            }
        }
    }
}
```

Burada dikkat çekeceğim alanlar; _progress_ ve _msg_ alanlarıdır. Bu alanlara bakarak index oluşturma işleminin ne kadarının tamamlandığını görebiliriz.

## İşlem Sonlandırma - killOp()

currentOp() ile incelediğimiz ve devam etmesini istemediğimiz bir işlemi kapatmak istediğimizde killOp() metodunu kullanabiliriz. currentOp başlığı altında bahsettiğim _opid_ alanını kullanarak sonlandırma işlemini gerçekleştirebiliriz.

```js
db.killOp(opid);
```

## Sorgu Analizi - explain()

Index oluşturmamıza rağmen bazen sorgularımız yavaş çalışmaya devam edebiliyor. Bunun sebebi de mongodb'nin bazı durumlarda yanlış index'i seçmesinden kaynaklanıyor. Bu tarz durumları yakalamak ve müdahale edebilmek için gerekli bilgiyi veren metod _explain()_ metodudur. Analiz etmek istediğimiz sorgunun sonuna eklenerek çalışan explain() metodu, bize sorguyla ilgili hangi index'leri denediğini ve hangisini seçtiğini söyler. Kullanımı aşağıdaki gibidir:

```js
db.comments.find({"page_id" : "165826141939","from.id" : "7271129512928941"}).explain()
```

Bu sorgunun çıktısı:

```js
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "example_db.comments",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"from.id" : {
						"$eq" : "7271129512928941"
					}
				},
				{
					"page_id" : {
						"$eq" : "165826141939"
					}
				}
			]
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"page_id" : 1,
					"from.id" : 1
				},
				"indexName" : "page_id_1_from.id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"page_id" : [ ],
					"from.id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"page_id" : [
						"["165826141939", "165826141939"]"
					],
					"from.id" : [
						"["7271129512928941", "7271129512928941"]"
					]
				}
			}
		},
		"rejectedPlans" : [
			{
				"stage" : "FETCH",
				"filter" : {
					"from.id" : {
						"$eq" : "7271129512928941"
					}
				},
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"page_id" : 1,
						"created_time" : -1
					},
					"indexName" : "page_id_1_created_time_-1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"page_id" : [ ],
						"created_time" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"page_id" : [
							"["165826141939", "165826141939"]"
						],
						"created_time" : [
							"[MaxKey, MinKey]"
						]
					}
				}
			},
			{
				"stage" : "FETCH",
				"filter" : {
					"from.id" : {
						"$eq" : "7271129512928941"
					}
				},
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"page_id" : 1,
						"is_hidden" : 1,
						"sentiment" : 1,
						"created_time" : -1
					},
					"indexName" : "page_id_1_is_hidden_1_sentiment_1_created_time_-1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"page_id" : [ ],
						"is_hidden" : [ ],
						"sentiment" : [ ],
						"created_time" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"page_id" : [
							"["165826141939", "165826141939"]"
						],
						"is_hidden" : [
							"[MinKey, MaxKey]"
						],
						"sentiment" : [
							"[MinKey, MaxKey]"
						],
						"created_time" : [
							"[MaxKey, MinKey]"
						]
					}
				}
			},
			{
				"stage" : "FETCH",
				"filter" : {
					"from.id" : {
						"$eq" : "7271129512928941"
					}
				},
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"page_id" : 1
					},
					"indexName" : "page_id_1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"page_id" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"page_id" : [
							"["165826141939", "165826141939"]"
						]
					}
				}
			}
		]
	},
	"serverInfo" : {
		"host" : "ip-172-31-69-55",
		"port" : 27019,
		"version" : "3.4.23",
		"gitVersion" : "324017ede1dbb1c9554dd2dceb15f8da3c59d0e8"
	},
	"ok" : 1
}
```

Yukarıdaki çıktıda; winningPlan ve rejectedPlan alanları bize denenen ve kazanan index'lerle ilgili bilgi verecektir. Eğer winningPlan içerisindeki index, yanlış ise sorgumuz olması gerekenden daha yavaş çalışacaktır. MongoDB ekibi bu tarz durumlar olabileceğini düşünmüş olacak ki bize _hint()_ metodunu sunmuş.

## Index Belirtme - hint()

hint() metodu, index seçimini direkt bizim yapmamızı sağlar. Parametre olarak index adını alır. Kullanım şekli _explain()_ metodu ile aynıdır.

```js
db.comments.find({"page_id" : "165826141939","from.id" : "7271129512928941"}).hint("page_id_1_from.id_1")
```

Bu sorgunun ucuna explain() metodunu da eklersek farkı görebiliriz.

```js
db.comments.find({"page_id" : "165826141939","from.id" : "7271129512928941"}).hint("page_id_1_from.id_1").explain()
```

Çıktıyı tekrar incelediğimizde bu kez rejectedPlans alanının boş kaldığını görebiliriz. Bunun sebebi hint ile direkt olarak kullanılacak index'i belirtmiş olmamızdır.

```js
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "example_db.comments",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"from.id" : {
						"$eq" : "7271129512928942"
					}
				},
				{
					"page_id" : {
						"$eq" : "165826141939"
					}
				}
			]
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"page_id" : 1,
					"from.id" : 1
				},
				"indexName" : "page_id_1_from.id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"page_id" : [ ],
					"from.id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"page_id" : [
						"["165826141939", "165826141939"]"
					],
					"from.id" : [
						"["7271129512928942", "7271129512928942"]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"serverInfo" : {
		"host" : "ip-172-31-69-55",
		"port" : 27019,
		"version" : "3.4.23",
		"gitVersion" : "324017ede1dbb1c9554dd2dceb15f8da3c59d0e8"
	},
	"ok" : 1
}
```

Son olarak, _hint()_ metodunun parametre olarak index adını aldığını söylemiştik. Index adını görmek için getIndexes metodunu kullanabiliriz.

## Index Görüntüleme - getIndexes()

Bir collection'da tanımlı index'leri ve özelliklerini görebilmek için _getIndexes()_ metodunu kullanırız.. Kullanımı aşağıdaki gibidir:

```js
db.comments.getIndexes()
```

Bu komutun çıktısı, comments collection'unda tanımlı olan index'lerin listesi olacaktır.

```js
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "example_db.comments"
	},
	{
		"v" : 2,
		"unique" : true,
		"key" : {
			"id" : 1
		},
		"name" : "id_1",
		"ns" : "example_db.comments",
		"background" : true
	},
	{
		"v" : 2,
		"key" : {
			"page_id" : 1
		},
		"name" : "page_id_1",
		"ns" : "example_db.comments"
	},
	{
		"v" : 2,
		"key" : {
			"page_id" : 1,
			"from.id" : 1
		},
		"name" : "page_id_1_from.id_1",
		"ns" : "example_db.comments",
		"background" : true
	}
]
```

Makalemizin sonuna geldik. Bu makaleyi hazırlarken kullandığım kaynakları aşağıda paylaşıyorum:

- [https://www.mongodb.com/docs/manual/reference/method/db.currentOp/](https://www.mongodb.com/docs/manual/reference/method/db.currentOp/)
- [https://www.mongodb.com/docs/manual/reference/method/db.killOp/](https://www.mongodb.com/docs/manual/reference/method/db.killOp/)
- [https://www.mongodb.com/docs/manual/reference/method/cursor.explain/](https://www.mongodb.com/docs/manual/reference/method/cursor.explain/)
- [https://stackoverflow.com/questions/7730591/why-does-mongo-hint-make-a-query-run-up-to-10-times-faster](https://stackoverflow.com/questions/7730591/why-does-mongo-hint-make-a-query-run-up-to-10-times-faster)
