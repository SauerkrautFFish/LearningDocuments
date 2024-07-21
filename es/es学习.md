在es中，创建索引相当于创建数据库

```bash
创建index

put http://127.0.0.1:9200/shopping
resp:
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "shopping"
}

查看index
get http://127.0.0.1:9200/shopping
resp:
{
    "shopping": {
        "aliases": {},
        "mappings": {},
        "settings": {
            "index": {
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "number_of_shards": "1",
                "provided_name": "shopping",
                "creation_date": "1721490230113",
                "number_of_replicas": "1",
                "uuid": "L-0s76bRS_qPOqPOSSNIRQ",
                "version": {
                    "created": "8505000"
                }
            }
        }
    }
}

列举出所有index
http://127.0.0.1:9200/_cat/indices?v
resp:
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size dataset.size
yellow open   shopping L-0s76bRS_qPOqPOSSNIRQ   1   1          0            0       249b           249b         249b


删除索引
http://127.0.0.1:9200/shopping
resp:
{
    "acknowledged": true
}
```

在索引中添加文档（文档就类似于数据，以前es有类似表的概念，现在没有了）

```bash
post方法，不设置contenttype会创建失败
curl --location 'http://127.0.0.1:9200/shopping/_doc' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "title": "xiaomi",
    "category": "小米",
    "images": "wow",
    "price": 300.0
}'
resp:
{
    "_index": "shopping",
    "_id": "9abQ0pABqYE6WRlAIs54",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}

创建文档时，自定义id
curl --location 'http://127.0.0.1:9200/shopping/_doc/10011' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "title": "xiaomi",
    "category": "小米",
    "images": "wow",
    "price": 300.0
}'
resp:
{
    "_index": "shopping",
    "_id": "10011",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 3,
    "_primary_term": 1
}

用_create也行
curl --location --request PUT 'http://127.0.0.1:9200/shopping/_create/10012' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "title": "xiaomi",
    "category": "小米",
    "images": "wow",
    "price": 300.0
}'
resp:
{
    "_index": "shopping",
    "_id": "10012",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 4,
    "_primary_term": 1
}
```

获取文档

```bash
获取指定文档
curl --location 'http://127.0.0.1:9200/shopping/_doc/10012' \
--header 'Content-Type: application/json;charset=UTF-8'
resp:
{
    "_index": "shopping",
    "_id": "10012",
    "_version": 1,
    "_seq_no": 4,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "title": "xiaomi",
        "category": "小米",
        "images": "wow",
        "price": 300.0
    }
}

查找全部文档
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8'
resp:
{
    "took": 50,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}
```



覆盖文档数据

```bash
curl --location --request PUT 'http://127.0.0.1:9200/shopping/_doc/10011' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "title": "wow",
    "category": "updatedwow"
}'
resp:
{
    "_index": "shopping",
    "_id": "10011",
    "_version": 2,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 5,
    "_primary_term": 3
}

局部修改文档数据
curl --location 'http://127.0.0.1:9200/shopping/_update/10011' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "doc": {
        "title": "华为手机"
    }
}'
resp:
{
    "_index": "shopping",
    "_id": "10011",
    "_version": 3,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 6,
    "_primary_term": 3
}

curl --location 'http://127.0.0.1:9200/shopping/_doc/10011' \
--header 'Content-Type: application/json;charset=UTF-8'
resp:
{
    "_index": "shopping",
    "_id": "10011",
    "_version": 3,
    "_seq_no": 6,
    "_primary_term": 3,
    "found": true,
    "_source": {
        "title": "华为手机",
        "category": "updatedwow"
    }
}
```

条件查询

```bash
curl --location 'http://127.0.0.1:9200/shopping/_search?q=category%3Aupdatedwow' \
--header 'Content-Type: application/json;charset=UTF-8'
resp:
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 1,
            "relation": "eq"
        },
        "max_score": 1.6943598,
        "hits": [
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 1.6943598,
                "_source": {
                    "title": "华为手机",
                    "category": "updatedwow"
                }
            }
        ]
    }
}

或者通过请求体的方式也可以（更推荐）
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match": {
            "category": "updatedwow"
        }
    }
}'

查询所有数据
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match_all": {
            
        }
    }
}'
resp:
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 1.0,
                "_source": {
                    "title": "华为手机",
                    "category": "updatedwow"
                }
            }
        ]
    }
}

也可以进行分页查询
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match_all": {
            
        }
    },
    "from": 0,
    "size": 1
}'
resp:
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}

控制查询出来的字段 & 控制排序
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match_all": {
            
        }
    },
    "from": 0,
    "size": 1,
    "_source": ["title"],
    "sort": {
        "price": {
            "order": "desc"
        }
    }
}'
resp:
{
    "took": 19,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": null,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": null,
                "_source": {
                    "title": "xiaomi"
                },
                "sort": [
                    300.0
                ]
            }
        ]
    }
}
```

多条件查询

```bash
must里必须同时满足, 即代表and
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "category": "updatedwow"
                    }
                },
                {
                    "match": {
                        "title": "华为手机"
                    }
                }
            ]
        }
    }
    
}'
resp:
{
    "took": 17,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 1,
            "relation": "eq"
        },
        "max_score": 5.130808,
        "hits": [
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 5.130808,
                "_source": {
                    "title": "华为手机",
                    "category": "updatedwow"
                }
            }
        ]
    }
}

should里必须or, 满足其中一个即可
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "bool": {
            "should": [
                {
                    "match": {
                        "category": "updatedwow"
                    }
                },
                {
                    "match": {
                        "title": "xiaomi"
                    }
                }
            ]
        }
    }
}'
resp:
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.6943598,
        "hits": [
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 1.6943598,
                "_source": {
                    "title": "华为手机",
                    "category": "updatedwow"
                }
            },
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 0.3398124,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 0.3398124,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}

同时还可以进行过滤
curl --location --request GET 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "bool": {
            "should": [
                {
                    "match": {
                        "category": "updatedwow"
                    }
                },
                {
                    "match": {
                        "title": "xiaomi"
                    }
                }
            ],
            "filter": {
                "range": {
                    "price": {
                        "gt": 200
                    }
                }
            }
        }
    }
    
}'
resp:
{
    "took": 10,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 3,
            "relation": "eq"
        },
        "max_score": 0.3398124,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 0.3398124,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 0.3398124,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}
```

精准匹配

```bash
像query->match其实是会把要match的字符串分割，匹配到其中之一即可，比如说我查找“小米”，那么“小de米”也会被搜到
如果要完全匹配的话需要使用match_phrase

使用match
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match": {
            "category": "小米"
        }
    }
}'
resp:
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 0.48232412,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 0.48232412,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 0.48232412,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "p9Yo1ZABfmPyVwnBaFMk",
                "_score": 0.40042,
                "_source": {
                    "title": "xiaomi",
                    "category": "小de米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}

使用match_phrase
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match_phrase": {
            "category": "小米"
        }
    }
}'
resp:
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 3,
            "relation": "eq"
        },
        "max_score": 0.48232412,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 0.48232412,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 0.48232412,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    }
}
```

把符合条件的进行高亮显示

```bash
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match_phrase": {
            "category": "小"
        }
    },
    "highlight": {
        "fields": {
            "category": {}
        }
    }
}'

{
    "took": 5,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 0.24116206,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 0.24116206,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                },
                "highlight": {
                    "category": [
                        "<em>小</em>米"
                    ]
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 0.24116206,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                },
                "highlight": {
                    "category": [
                        "<em>小</em>米"
                    ]
                }
            }
        ]
    }
}
```

聚合操作

```bash
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "aggs": { 
        "price_group": { 
            "terms": { 
                "field": "price" 
            }
        }
    }
}'
resp:
{
    "took": 61,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 5,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "9abQ0pABqYE6WRlAIs54",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "1001",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10012",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小米",
                    "images": "wow",
                    "price": 300.0
                }
            },
            {
                "_index": "shopping",
                "_id": "10011",
                "_score": 1.0,
                "_source": {
                    "title": "华为手机",
                    "category": "updatedwow"
                }
            },
            {
                "_index": "shopping",
                "_id": "p9Yo1ZABfmPyVwnBaFMk",
                "_score": 1.0,
                "_source": {
                    "title": "xiaomi",
                    "category": "小de米",
                    "images": "wow",
                    "price": 300.0
                }
            }
        ]
    },
    "aggregations": {
        "price_group": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": 300.0,
                    "doc_count": 4
                }
            ]
        }
    }
}

但这样除了聚合数据，原始数据也会被获取，可以通过size字段不获取原始数据
curl --location 'http://127.0.0.1:9200/shopping/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "aggs": { 
        "price_group": { 
            "terms": {  // terms改成avg就是求平均值
                "field": "price" 
            }
        }
    },
    "size": 0
}'
resp:
{
    "took": 10,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 5,
            "relation": "eq"
        },
        "max_score": null,
        "hits": []
    },
    "aggregations": {
        "price_group": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": 300.0,
                    "doc_count": 4
                }
            ]
        }
    }
}
```

映射关系

```bash
创建一个user的index
curl --location 'http://127.0.0.1:9200/user' \
--header 'Content-Type: application/json;charset=UTF-8'

然后进行映射
curl --location --request PUT 'http://127.0.0.1:9200/user/_mapping' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "properties": {
        "name": {
            "type": "text", // 可以分词
            "index": true
        },
        "sex": {
            "type": "keyword", // 只可以完整匹配
            "index": true
        },
        "tel": {
            "type": "keyword", // 只可以完整匹配
            "index": false // 不能被索引
        }
    }
}'
resp:
{
    "acknowledged": true
}

然后插入文档
curl --location --request PUT 'http://127.0.0.1:9200/user/_create/10011' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "name": "小米",
    "sex": "男的",
    "tel": "1111"
}'

接下来我们查询一下
curl --location --request GET 'http://127.0.0.1:9200/user/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match": {
            "name": "小"
        }
    }
}'
resp:
{
    "took": 20,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 1,
            "relation": "eq"
        },
        "max_score": 0.60996956,
        "hits": [
            {
                "_index": "user",
                "_id": "10011",
                "_score": 0.60996956,
                "_source": {
                    "name": "小米",
                    "sex": "男的",
                    "tel": "1111"
                }
            }
        ]
    }
}

curl --location --request GET 'http://127.0.0.1:9200/user/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match": {
            "sex": "男" // 需要"男的"才能查找到
        }
    }
}'

curl --location --request GET 'http://127.0.0.1:9200/user/_search' \
--header 'Content-Type: application/json;charset=UTF-8' \
--data '{
    "query": {
        "match": {
            "tel": "1111" // error，没有被索引
        }
    }
}'
```

