# Elasticsearch 搜索引擎

### 1.核心功能

**全文搜索**：Elasticsearch 提供强大的全文搜索功能，可以处理复杂的查询，包括模糊查询、正则表达式和通配符查询等。

**实时搜索**：数据在索引后几乎立即可用进行搜索，提供快速的响应时间。

**RESTful API**：通过 JSON 进行交互，支持简单的 CRUD 操作、搜索、聚合和管理操作。





### 2.组成

**文档**：数据以 JSON 格式的文档存储。每个文档都是一个 JSON 对象，包含多个字段。

**索引**：一个索引是文档的集合，类似于数据库中的表。

**类型**（在 7.x 及之后版本中已弃用）：在早期版本中，一个索引可以包含多个类型，每个类型下可以有多个文档。现在的版本推荐将所有文档存储在一个单一的类型 `"_doc"` 下。

**字段**：每个文档都有多个字段，每个字段可以具有不同的数据类型，如字符串、数字、日期等













### 3.功能特点

**分片和备份**

Elasticsearch 将索引分为多个分片，每个分片都有一个或多个备份。这种设计确保了数据的高可用性和负载均衡。



**聚合功能**

通过聚合对数据进行分组和统计。



**高效查询**

支持多种查询方式，如布尔查询、范围查询、排序和过滤等。



**自动扩展**

集群可以根据需求自动扩展，添加更多节点以处理更多数据和请求。







### 4.入门【增删改查】

#### 创建索引

```java
@Repository
public class CourseRepository {
     
    @Autowired
    private RestHighLevelClient client;

    // 这里是创建索引
    public void createIndex() throws Exception {
         												//这里是索引的名称
        CreateIndexRequest createIndexRequest = new CreateIndexRequest("subject");
         											//这里是分片数量				//这里是备份数量
        createIndexRequest.settings(Settings.builder().put("number_of_shards", "1").put("number_of_replicas", "0").build());
       
        //创建映射
        createIndexRequest.mapping("doc", "{\n" +
                "\t\"properties\": {\n" +
                "\t\t\"description\": {\n" +
                "\t\t\t\"type\": \"text\",\n" +
                "\t\t\t\"analyzer\": \"ik_max_word\",\n" +
                "\t\t\t\"search_analyzer\": \"ik_smart\"\n" +
                "\t\t},\n" +
                "\t\t\"name\": {\n" +
                "\t\t\t\"type\": \"text\",\n" +
                "\t\t\t\"analyzer\": \"ik_max_word\",\n" +
                "\t\t\t\"search_analyzer\": \"ik_smart\"\n" +
                "\t\t},\n" +
                "\t\t\"pic\": {\n" +
                "\t\t\t\"type\": \"text\",\n" +
                "\t\t\t\"index\": false\n" +
                "\t\t},\n" +
                "\t\t\"price\": {\n" +
                "\t\t\t\"type\": \"scaled_float\",\n" +
                "            \"scaling_factor\": 100 \n" +
                "\t\t},\n" +
                "\t\t\"studymodel\": {\n" +
                "\t\t\t\"type\": \"keyword\"\n" +
                "\t\t},\n" +
                "\t\t\"timestamp\": {\n" +
                "\t\t\t\"type\": \"date\",\n" +
                "\t\t\t\"format\": \"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd\"\n" +
                "\t\t}\n" +
                "\t}\n" +
                "}", XContentType.JSON); //类型为json
        
        //执行创建操作
        CreateIndexResponse indexResponse = client.indices().create(createIndexRequest, RequestOptions.DEFAULT);
        
        //输出结果
        System.out.println(indexResponse.isAcknowledged());
    }

}
```

查询索引库下所有的数据： 【http://localhost:9200/wf_course/_search】 







#### 新增文档

```java
/**
 * 进行存储
 * @param subjects 存储对象
 * @throws Exception
 */
@Autowired
private RestHighLevelClient client; //客户端

@PostMapping("add")
public void add(Subjects subjects) throws Exception {
    Map map = new HashMap();
    map.put("title",subjects.getTitle());
    map.put("totalVotes",subjects.getTotalVotes());
    map.put("viewTimes",subjects.getViewTimes());
    map.put("createDate",subjects.getCreateDate());

    IndexRequest indexRequest = new IndexRequest("subject","doc",""+subjects.getId());
    indexRequest.source(map);
    IndexResponse indexResponse = client.index(indexRequest, RequestOptions.DEFAULT);
    System.out.println(indexResponse.status());
}
```

新增完后能够使用这个进行查看：【http://localhost:9200/wf_course/_search】 







#### 查询全部

```java
public void searchAll() throws Exception {
    //创建索引请求
    SearchRequest searchRequest = new SearchRequest("wf_course");
    //设置类型
    searchRequest.types("doc");
    //进行索引源的构建
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    //构建搜索请求的类。它允许你设置查询、分页、排序等。
    searchSourceBuilder.query(QueryBuilders.matchAllQuery());
    searchRequest.source(searchSourceBuilder);

    //执行
    SearchResponse search = client.search(searchRequest, RequestOptions.DEFAULT);
    //返回的数据
    System.out.println(search);
}
```

【这里是使用java的代码进行查询索引库中的数据】





#### 根据id查

```java
/**
 * 根据id查
 * @param id id
 * @throws Exception
 */
@Autowired
private RestHighLevelClient client; //客户端

@PostMapping("getById")
public void getById(Integer id) throws Exception {
     							 // 索引名称    类型      id
    GetRequest getRequest = new GetRequest("subject", "doc", ""+id);
     					
    GetResponse getResponse = client.get(getRequest, RequestOptions.DEFAULT);
    //获取数据转换为map
    Map<String, Object> sourceAsMap = getResponse.getSourceAsMap();
    System.out.println(sourceAsMap);
}
```

【这里是使用java的代码进行查询索引库中的数据】









#### 删除文档

```java
//删除文档
@PostMapping("deleteDoc")
public void deleteDoc(int id) throws Exception {
    DeleteRequest deleteRequest = new DeleteRequest("subject","doc",""+id);
    DeleteResponse delete = client.delete(deleteRequest, RequestOptions.DEFAULT);
    System.out.println(delete.status());
}
```







#### 删除索引

```java
@PostMapping("deleteIndex")
public void deleteIndex(String name) throws Exception {
													//这里是索引名称
    DeleteIndexRequest deleteIndexRequest = new DeleteIndexRequest(name);
    AcknowledgedResponse delete = client.indices().delete(deleteIndexRequest, RequestOptions.DEFAULT);
    System.out.println(delete.isAcknowledged());
}
```





#### 修改文档

```java
//修改文档
@PostMapping("updateDoc")
public void updateDoc(Subjects subjects) throws Exception {
    UpdateRequest updateRequest = new UpdateRequest("subject","doc",""+subjects.getId());
   	
    //map中存储的是要修改的字段以及修改的新值 
    Map map = new HashMap();
    map.put("title",subjects.getTitle());
    map.put("totalVotes",subjects.getTotalVotes());
    map.put("viewTimes",subjects.getViewTimes());
    updateRequest.doc(map);
    UpdateResponse updateResponse = client.update(updateRequest, RequestOptions.DEFAULT);
    System.out.println(updateResponse.status());
}
```





### 5.多种查询

#### -----------修改此代码-----------

```java
public void searchAll() throws Exception {
    //创建索引请求
    SearchRequest searchRequest = new SearchRequest("wf_course");
    //设置类型
    searchRequest.types("doc");
    //进行索引源的构建
    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    //构建搜索请求的类。它允许你设置查询、分页、排序等。
    searchSourceBuilder.query(QueryBuilders.matchAllQuery());
    //-----------------------------------------------------------------
     
     
    //-----------------------------------------------------------------
    
    //searchSourceBuilder.query(boolQueryBuilder);
    searchRequest.source(searchSourceBuilder);
     
    //执行
    SearchResponse search = client.search(searchRequest, RequestOptions.DEFAULT);
    //返回的数据
    System.out.println(search);
}
```

【条件查询需要修改---------------中的代码】





#### 精确查

```java
//进行精确查询										  字段名	  字段数据
TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("studymodel","201002");
searchSourceBuilder.query(termQueryBuilder);
```





#### 分词查询

```java
//设置分词查询											字段名 		 字段数据
MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("description", "spring cloud");
//这里有and、or如果是and意思就是spring cloud 的分词必须全部都要满足
//例：spring cloud分词spring和cloud,要查找的数据必须同时有spring 和 cloud两个单词
matchQueryBuilder.operator(Operator.AND); 

//配置比例：如果spring 和 cloud两个单词的比例高达80%，就会筛选出来
matchQueryBuilder.minimumShouldMatch("80%"); 
searchSourceBuilder.query(matchQueryBuilder);
```





#### 组合查询

```java
 //组合查询
BoolQueryBuilder builder = QueryBuilders.boolQuery().mustNot(QueryBuilders.termQuery("name", "spring cloud实战"));
           .must(QueryBuilders.termQuery("name", "spring"))
           .must(QueryBuilders.termQuery("studymodel", "201001"))
           .should(QueryBuilders.termQuery("studymodel", "201001"));
```

**must**：

​		用于指定必须满足的条件 相当于逻辑与 (`AND`)。



**should**：

​		文档如果满足 `should` 子句中的一个或多个条件，则文档将被匹配

​		`should` 子句相当于逻辑或 (`OR`)

​		满足更多 `should` 子句的文档会有更高的相关性得分。



**must_not**：

​		`must_not` 子句用于指定必须不满足的条件。

​		相当于逻辑非 (`NOT`)。



#### 查询count





#### 一组值查询









### 6.配置使用

```java
@Configuration
public class ElasticsearchConfig {

    @Value("${myelasticsearch.host}")
    private String host;
    @Value("${myelasticsearch.port}")
    private Integer port;

    @Bean
    public RestHighLevelClient client() {
        HttpHost httpHost = new HttpHost(host, port, "http");
        return new RestHighLevelClient(RestClient.builder(httpHost));
    }
}
```



```yml
myelasticsearch:
  host: 127.0.0.1
  port: 9200
```



```java
@Autowired
private RestHighLevelClient client;
```

【结合入门案例进行编写】





### 7.地理位置查询

#### 地理位置类型

Elasticsearch 提供了两种表示地理位置的方式：用纬度－经度表示的坐标点使用 geo_point 字段类型

以 GeoJSON 格式定义的复杂地理形状，使用 geo_shape 字段类型。



#### geo_shape和geo_point 区别

Geo-points 允许你找到距离另一个坐标点一定范围内的坐标点、计算出两点之间的距离来排序或进行相关性打分、或者聚合到显示在地图上的一个网格。另一方面，Geo-shapes 纯粹是用来过滤的。它们可以用来判断两个地理形状是否有重合或者某个地理形状是否完全包含了其他地理形状。



#### 创建索引

```json
PUT /gps

{
  "mappings": {
    "restaurant": {
      "properties": {
        
        "location": {
          "type": "geo_point"
        }
      
      }
    }
  }
}
```





#### 经纬度坐标格式

如上例，location 被声明为 geo_point 后，我们就可以索引包含了经纬度信息的文档了。经纬度信息的形式可以是字符串，数组或者对象。

```json
PUT /attractions/restaurant/1
{
  "name":     "Chipotle Mexican Grill",
  "location": "40.715, -74.011" <1>
}

PUT /attractions/restaurant/2
{
  "name":     "Pala Pizza",
  "location": { <2>
    "lat":     40.722,
    "lon":    -73.989
  }
}

PUT /attractions/restaurant/3
{
  "name":     "Mini Munchies Pizza",
  "location": [ -73.983, 40.719 ] <3>
}

```

【lat是纬度 ，lon是经度】

> <1> 以半角逗号分割的字符串形式 “lat,lon”；

> <2> 明确以 lat 和 lon 作为属性的对象；

> <3> 数组形式表示 [lon,lat]。
