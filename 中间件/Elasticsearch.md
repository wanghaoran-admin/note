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

#### 配置类

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



