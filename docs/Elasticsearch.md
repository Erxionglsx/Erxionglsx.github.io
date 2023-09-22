# Elasticsearch

> <https://blog.csdn.net/Y_hanxiong/article/details/127261296>

[TOC]

## Elasticsearch概述

Elasticsearch是一种开源的分布式搜索和分析引擎，用于处理大量数据并提供快速、准确的搜索结果。它可以用于构建各种应用程序，包括企业搜索、日志分析、安全分析、商业智能等。Elasticsearch使用Lucene搜索引擎作为其核心，提供了强大的全文搜索和分析功能，同时还支持实时数据分析和可视化。

## Elasticsearch安装

下载地址：<https://www.elastic.co/cn/downloads/past-releases#elasticsearch>

解压后，进入bin 文件目录，点击 elasticsearch.bat 文件启动 ES 服务

打开浏览器（推荐使用谷歌浏览器），输入地址：<http://localhost:9200> ，测试结果

### 数据格式

Elasticsearch是面向文档型数据库，一条数据在这里就是一个文档。 为了方便大家理解，我们将 Elastic s earch 里 存储 文档 数据和关系型数据库 MySQL 存储数据的概念进行一个类比

![](https://note.youdao.com/yws/api/personal/file/WEB736665f19aaa614867ef97859b0d31ad?method=download\&shareKey=966e5e3348f86dcdb5f119666cbf23f0)

## 索引操作

### 1.创建索引

> 对比关系型数据库，创建索引就等同于创建数据库

在Postman 中，向 ES 服务器发 PUT 请求 <http://127.0.0.1:9200/user>

```java
//代码创建索引
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));
        //创建索引
        CreateIndexRequest request = new CreateIndexRequest("user");
        CreateIndexResponse response = client.indices().create(request, RequestOptions.DEFAULT);
        //创建索引的响应状态
        boolean acknowledged = response.isAcknowledged();
        System.out.println("响应状态为：" + acknowledged);
        client.close();
    }
```

### 2.查看索引

在Postman 中，向 ES 服务器发 GET 请求 <http://127.0.0.1:9200/_cat/indices?v>

![](https://note.youdao.com/yws/api/personal/file/WEBbea4d0a949b2457328e2bb0a4d343500?method=download\&shareKey=b9900ae49901cfbdc1f7a769fc7af1d5)

![](https://note.youdao.com/yws/api/personal/file/WEB851c20c6d3e14d3f2ca5f5095591070f?method=download\&shareKey=7daf16b53e3c80c03ff154738ad7b2a5)

### 3.查看单个索引

在Postman 中，向 ES 服务器发 GET 请求 <http://127.0.0.1:9200/user>

### 4.删除索引

在Postman 中，向 ES 服务器发 GET 请求 <http://127.0.0.1:9200/start>

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));
        //删除索引
        DeleteIndexRequest request = new DeleteIndexRequest("user");
        AcknowledgedResponse response = client.indices().delete(request, RequestOptions.DEFAULT);
        //删除索引的响应状态
        System.out.println("删除状态为：" + response.isAcknowledged());
        client.close();
}
```

## 文档操作

### 1.创建文档

这里的文档可以类比为关系型数据库中的表数据，添加的数据格式为 JSON 格式

在Postman 中，向 ES 服务器发 POST 请求 <http://127.0.0.1:9200/user/doc>

```json
{
    "name":"xiaobear",
    "age":18,
    "sex":"boy"
}
```

```java
//代码创建文档
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));

        IndexRequest indexRequest = new IndexRequest();
        indexRequest.index("user").id("1001");
        //创建数据对象
        User user = new User("xiaobear",18,"boy");
        //数据对象转为JSON
        ObjectMapper mapper = new ObjectMapper();
        String userJson = mapper.writeValueAsString(user);
        indexRequest.source(userJson, XContentType.JSON);
        //获取响应对象
        IndexResponse response = client.index(indexRequest, RequestOptions.DEFAULT);
        System.out.println("_index：" + response.getIndex());
        System.out.println("_id：" + response.getId());
        System.out.println("_result：" + response.getResult());
        client.close();
    }
```

### 2.查看文档

在Postman 中，向 ES 服务器发 GET 请求 <http://127.0.0.1:9200/user/_doc/1001>

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));
        //创建请求对象
        GetRequest request = new GetRequest().index("user").id("1001");
        //创建响应对象
        GetResponse response = client.get(request, RequestOptions.DEFAULT);
        // 打印结果信息
        System.out.println("_index:" + response.getIndex());
        System.out.println("_type:" + response.getType());
        System.out.println("_id:" + response.getId());
        System.out.println("source:" + response.getSourceAsString());
        client.close();
    }
```

### 3.修改文档

在Postman 中，向 ES 服 务器发 POST 请求 <http://127.0.0.1:9200/user/_doc/1001>

```json
{
    "name":"xiaobear",
    "age":18,
    "sex":"girl"
}
```

和新增文档一样，输入相同的URL 地址请求，如果请求体变化，会将原有的数据内容覆盖 在Postman 中

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));
        //修改文档
        UpdateRequest request = new UpdateRequest();
        request.index("user").id("1001");
        // 设置请求体，对数据进行修改
        request.doc(XContentType.JSON,"sex","girl");
        UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
        System.out.println("_index：" + response.getIndex());
        System.out.println("_id：" + response.getId());
        System.out.println("_result：" + response.getResult());
        client.close();
    }
```

### 4.修改字段

修改数据时，也可以只修改某一给条数据的局部信息

在Postman 中，向 ES 服务器发 POST 请求 <http://127.0.0.1:9200/user/_update/1001>

```json
 {
 "doc": {
     "age":20
     }
 }
```

### 5.删除文档

删除一个文档不会立即从磁盘上移除，它只是被标记成已删除（逻辑删除）。

在Postman 中，向 ES 服务器发 DELETE 请求 <http://127.0.0.1:9200/user/_doc/1001>

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200)));
        //创建请求对象
        DeleteRequest request = new DeleteRequest().index("user").id("1");
        //创建响应对象
        DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
        //打印信息
        System.out.println(response.toString());
        client.close();
    }
```

### 6.条件删除文档

一般删除数据都是根据文档的唯一性标识进行删除，实际操作时，也可以根据条件对多条数据进行删除

向ES 服务器发 POST 请求 <http://127.0.0.1:9200/user/_delete_by_query>

```json
{
    "query":{
        "match":{
            "age": 20
        }
    }
}
```

## 映射操作

映射类似于数据库 (database)中的表结构 (table)。创建数据库表需要设置字段名称，类型，长度，约束等；索引库也一样，需要知道这个类型下有哪些字段，每个字段有哪些约束信息，这就叫做映射 (mapping)。

### 1.创建映射

在Postman 中，向 ES 服务器发 PUT 请求<http://127.0.0.1:9200/user/_mapping>

```json
{
    "properties":{
        "name":{
            "type": "text",
            "index": true
        },
        "sex":{
            "type": "keyword",
            "index": true
        },
        "phone":{
            "type": "keyword",
            "index": false
        }
    }
}
```

```java
//映射数据说明
字段名：任意填写，下面指定许多属性，例如： title 、 subtitle 、 images 、 price
type ：类型 Elasticsearch 中支持的数据类型非常丰富，说几个关键的：
  * 基本数据类型：long 、 integer 、 short 、 byte 、 double 、 float 、 half_float
  * 浮点数的高精度类型：scaled_float
  * text：可分词
  * keyword：不可分词，数据会作为完整字段进行匹配
  * String 类型，又分两种：
  * Numerical ：数值类型，分两类
  * Date ：日期类型
  * Array ：数组类型
  * Object ：对象
index ：是否索引，默认为 true ，也就是说你不进行任何配置，所有字段都会被索引。
  * true：字段会被索引，则可以用来进行搜索
  * false：字段不会被索引，不能用来搜索
store ：是否将数据进行独立存储，默认为 false
analyzer ：分词器，这里的 ik_max_word 即使用 ik 分词器
```

### 2.查看映射

在Postman 中，向 ES 服务器发 GET 请求<http://127.0.0.1:9200/user/_mapping>

## 批量操作

### 1.批量新增文档

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建批量新增请求对象
        BulkRequest request = new BulkRequest();
        request.add(new IndexRequest().index("user").id("1004").source(XContentType.JSON,"name","xiaohuahua"));
        request.add(new IndexRequest().index("user").id("1005").source(XContentType.JSON,"name","zhangsan"));
        request.add(new IndexRequest().index("user").id("1006").source(XContentType.JSON,"name","lisi"));
        //创建响应对象
        BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println("took：" + response.getTook());
        System.out.println("items：" + response.getItems());
    }
```

### 2.批量删除文档

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建批量新增请求对象
        BulkRequest request = new BulkRequest();
        request.add(new DeleteRequest().index("user").id("1001"));
        request.add(new DeleteRequest().index("user").id("1002"));
        request.add(new DeleteRequest().index("user").id("1003"));
        //创建响应对象
        BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);
        System.out.println("took：" + response.getTook());
        Arrays.stream(response.getItems()).forEach(System.out::println);
        System.out.println("items：" + Arrays.toString(response.getItems()));
        System.out.println("status：" + response.status());
        System.out.println("失败消息：" + response.buildFailureMessage());
    }
```

## 高级查询

### 1.请求体查询

查询所有索引数据

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询所有对象
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        request.source(sourceBuilder);

        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 2.term查询

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询所有对象
        sourceBuilder.query(QueryBuilders.termQuery("name","zhangsan"));
        request.source(sourceBuilder);

        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 3.分页查询

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询所有对象
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        //分页查询 当前页其实索引 第一条数据的顺序号 from
        sourceBuilder.from(0);
        //每页显示多少条
        sourceBuilder.size(2);
        request.source(sourceBuilder);
 
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 4.数据排序

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询所有对象
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        //数据排序
        sourceBuilder.sort("age", SortOrder.DESC);
 
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 5.过滤字段

```java
 public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //查询所有对象
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        //数据排序
        sourceBuilder.sort("age", SortOrder.DESC);
        //查询过滤字段
        String[] excludes = {};
        //过滤掉name属性
        String[] includes = {"age"};
        sourceBuilder.fetchSource(includes,excludes);
 
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 6.Bool查询

```java
 public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
        //必须包含
        boolQuery.must(QueryBuilders.matchQuery("age",18));
        //一定不包含
        boolQuery.mustNot(QueryBuilders.matchQuery("name","lisi"));
        //可能包含
        boolQuery.should(QueryBuilders.matchQuery("name","zhangsan"));
        //查询所有对象
        sourceBuilder.query(boolQuery);
        
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 7.范围查询

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //范围查询
        RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");
        //大于等于
        rangeQuery.gte("19");
        //小于等于
        rangeQuery.lte("40");
        //查询所有对象
        sourceBuilder.query(rangeQuery);
 
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

### 8.模糊查询

```java
 public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //创建搜索对象
        SearchRequest request = new SearchRequest();
        request.indices("user");
        //构建查询的请求体
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //模糊查询
        FuzzyQueryBuilder fuzzyQuery = QueryBuilders.fuzzyQuery("name", "zhangsan");
        fuzzyQuery.fuzziness(Fuzziness.ONE);
 
        sourceBuilder.query(fuzzyQuery);
 
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //查询匹配
        SearchHits hits = response.getHits();
        System.out.println("took：" + response.getTook());
        System.out.println("是否超时：" + response.isTimedOut());
        System.out.println("TotalHits：" + hits.getTotalHits());
        System.out.println("MaxScore：" + hits.getMaxScore());
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }
```

## 高亮查询

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        //高亮查询
        SearchRequest request = new SearchRequest("user");
        //创建查询请求体构建器
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //构建查询方式，高亮查询
        TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "zhangsan");
        //设置查询方式
        sourceBuilder.query(termQueryBuilder);
        //构建高亮字段
        HighlightBuilder highlightBuilder = new HighlightBuilder();
        //设置标签前缀
        highlightBuilder.preTags("<font color='red'");
        //设置标签后缀
        highlightBuilder.postTags("</font>");
        //设置高亮字段
        highlightBuilder.field("name");
        //设置高亮构建对象
        sourceBuilder.highlighter(highlightBuilder);
        //设置请求体
        request.source(sourceBuilder);
        //客户端发送请求，获取响应对象
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 打印响应结果
        SearchHits hits = response.getHits();
        System.out.println("took::"+response.getTook());
        System.out.println("time_out::"+response.isTimedOut());
        System.out.println("total::"+hits.getTotalHits());
        System.out.println("max_s core::"+hits.getMaxScore());
        System.out.println("hits::::>>");
        for (SearchHit hit : hits) {
            String sourceAsString = hit.getSourceAsString();
            System.out.println(sourceAsString);
            //打印高亮结果
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            System.out.println(highlightFields);
            System.out.println("<<::::");
        }
    }
```

## 聚合查询

### 1.最大年龄

```java
public static void main(String[] args) throws IOException {
        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
        SearchRequest request = new SearchRequest().indices("user");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.aggregation(AggregationBuilders.max("maxAge").field("age"));
        //设置请求体
        request.source(sourceBuilder);
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        //打印响应结果
        SearchHits hits = response.getHits();
        System.out.println("hits = " + hits);
        System.out.println(response);
    }
```

### 2.分组查询

```java
 public static void main(String[] args) throws IOException {
         RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200)));
         //创建搜索对象
         SearchRequest request = new SearchRequest().indices("user");
         SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
         searchSourceBuilder.aggregation(AggregationBuilders.terms("age_groupby").field("age"));
 
         //设置请求体
         request.source(searchSourceBuilder);
         SearchResponse response = client.search(request, RequestOptions.DEFAULT);
         System.out.println(response.getHits());
         System.out.println(response);
 
     }
```

