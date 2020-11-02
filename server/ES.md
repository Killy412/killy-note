```
# 传统型数据库和es对应关系
Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices   -> Types  -> Documents -> Fields
```

> 一个节点代表一个es实例,一个cluster代表一个集群,由多个节点node组成
>
> 9300端口: TCP协议端口,集群之间通过此端口通讯,java客户端也是以此端口通讯
>
> 9200端口: 面向外部通讯,外部可以使用restful接口进行访问

集群健康状态可以用`get /_cluster/health`检测,status为状态值

|   颜色   |                    意义                    |
| :------: | :----------------------------------------: |
| `green`  |        所有主要分片和复制分片都可用        |
| `yellow` | 所有主要分片可用，但不是所有复制分片都可用 |
|  `red`   |          不是所有的主要分片都可用          |

```
must/must_not/should 后面是中括号 []
```

##### 关键词

1. match_all

2. match: 支持全文检索和精确查询

   - `operator` 操作
   - `minimum_should_match` 最小匹配数量

   ```json
   {
     "query": {
       "match": {
         "ad": {
           "query": "a red",
           "operator": "and",      # 默认操作符是or
           "minimum_should_match": "2"   # 最小匹配数量  如果是1 ,匹配上a或者red就会返回
         }
       }
     }
   }
   ```

   

3. `multi_match`查询 :多字段查询

   ```json
   {
     "query": {
       "multi_match": {
         "query": "red",
         "fields": ["color","ad"]
       }
     }
   }
   ```

4. `range`查询  范围查询  gte 大于等于  gt大于  lt 小于  lte 小于等于

5. `term` 查询 精确值查询 

6. `terms` 可以匹配多个值

7. `exists`和`missing`查询

8. `match_phrase` 查询 精确匹配,不会进行分词

9. `fuzzy` 模糊查询,会计算与关键词的相似程度.

   参数:

   - `fuzziness`：最大编辑距离，默认为`AUTO`
   - `prefix_length`：不会“模糊化”的初始字符数。这有助于减少必须检查的术语数量，默认为`0`
   -  `max_expansions`：`fuzzy`查询将扩展到 的最大术语数。默认为`50`，设置小，有助于优化查询
   -  `transpositions`：是否支持模糊转置（`ab`→ `ba`），默认是`false`
   
10. `wildcard`查询 支持通配符的模糊查询 ？匹配单个字符，*匹配任何字符

##### 返回数据详解

```json
{
  "took": 1,    # 请求花费毫秒数
  "timed_out": false,
  "_shards": {
    "total": 15, # 参与查询的分片数
    "successful": 15,
    "failed": 0
  },
  "hits": {
    "total": 13,  
    "max_score": 1.0,
    "hits": [
      {
        "_index": "gb",
        "_type": "tweet",
        "_id": "5",
        "_score": 1.0,
        "_source": {
          "date": "2014-09-15",
          "name": "Mary Jones",
          "tweet": "However did I manage before Elasticsearch?",
          "user_id": 2
        }
      },
      //....
      }
    ]
  }
}
```

