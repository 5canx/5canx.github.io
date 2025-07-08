# Elasticsearch学习记录

<!--more-->
## Elasticsearch学习记录

### 安装elasticsearch和kibana

```docker
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.13.2
    platform: linux/amd64
    container_name: es
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false    #关闭密码认证（只在测试环境下运行）
    ports:
      - "9200:9200"
    networks:
      - es-net

  kibana:                              #可视化工具
    image: docker.elastic.co/kibana/kibana:8.13.2
    platform: linux/amd64
    container_name: kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
    networks:
      - es-net

networks:
  es-net:

```

启动:

```sh
docker compose up -d
```

![image-20250518123809347](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181238378.png)

访问ES：

```http
http://localhost:9200/
```

![image-20250518124044266](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181240293.png)

浏览器出现上图表示正常。

访问kibana：

```http
http://localhost:5601/
```

![image-20250518124143301](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181241325.png)

### Kibana运行查询等命令

![image-20250518124526969](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181245002.png)

点击Dev Tools：

![image-20250518124611199](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181246222.png)

查询全部数据：

```json
GET index/_search
{
  "query": {
    "match_all": {}
  }
}
```

查询全部索引：

```json
GET /_cat/indices?v
```

![May-18-2025 14-45-09](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202505181445444.gif)


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/elasticsearch%E5%AD%A6%E4%B9%A0%E8%AE%B0%E5%BD%95/  

