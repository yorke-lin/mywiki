# 安装配置
## 安装elasticsearch

# ik分词器
## 使用ik分词器
```js
// 分词器使用
GET _analyze
{
  "analyzer": "ik_max_word", // 使用分词器
  "text": "云南消费者"  // 搜索字符
}
```
`ik_smart`为最少切分，`ik_max_word`最细粒度切分

## 配置ik分词器

配置文件:分词器文件夹/config/IKAnalyzer.cfg.xml

# rest文档操作

| method | url地址                           | 描述                  |
| :----: | :-------------------------------: | :-------------------: |
| PUT    | /索引名/类型名/文档id         | 创建文档(指定文档id） |
| POST   | /索引名/类型名                | 创建文档(随机文档id） |
| POST   | /索引名/类型名/文档id/_update | 修改文档              |
| DELETE | /索引名/类型名/文档id         | 删除文档              |
| GET    | /索引名/类型名/文档id         | 通过id查询文档        |
| POST   | /索引名/类型名/_search        | 查询所有文档          |

**版本8类型不在使用改为`_doc`**

# Search APIs
- query: 在请求消息体中的`query`允许我们用`Query DSL`的方式查询
    - match: 将被查询值进行分词，然后用评分机制(TF/IDF)进行打分
    -
