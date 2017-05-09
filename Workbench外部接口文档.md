# Workbench 对外接口文档

home_url: guandata.com

## 1. 登录

**url**: $home_url/public-api/sign-in

**method**: POST

**parameters**: {"domain":"demo","email":"123@456.com","password":"my_password"}

**response**:

```json
{
  "result":"ok",
  "response":
  {
    "token":"eyJ0eXAiOiJ...", /* 该token在后面调用其他接口时使用 */
    "expireAt":"2017-01-01 00:11:122.33"
  }
}
```



## 2. 上传数据

**url**: $home_url/public-api/upload-dataset

**method**: POST

**parameters**:

header:

* Guandata-Content-Encoding: deflate            // 可选，用于压缩
* X-Auth-Token:  $token (通过登录接口获取)    // 必选

body参数二选一

* body参数类型1:    上传时指定参数dsId，表示更新指定的观数的数据集。

```json
{
  "dsId": "121212121",             /* 必选，data_source_Id */
  "overwriteExistingData": false,  /* 可选，设置为true时，先删除原来的数据，再上传。仅在设置dsId时有效 */
  "data": [                        /* 必选，传要上传的具体数据，格式为 JsonObjectArray */
    {
      "column1": "data1_1",
      "column2": "data1_2"
    },
    {
      "column1": "data2_1",
      "column2": "data2_2"
    }
  ],
  "batchFinish": false             /* 可选，默认为false，分批上传时，标示是否上传完毕 */
}
```

* body参数类型2: 传tableName

```json
{
  "tableName": "my_table_name",        /* 必选 */
  "overwriteExistingData": false,      /* 可选，默认为false，设置为true时，先删除原来的数据，再上传。仅在设置dsId时有效 */
  "columns": [                         /* 可选，设置各字段 */
    {
      "name": "column1",
      "isPrimaryKey": true,            /* 可选，默认为false，设置主键，设置了primaryKey后，多次上传时会更新原有数据，否则，会追加到原有数据后 */
      "isPartitionKey": false,         /* 可选，默认为false，设置分区键 */
      "isEncrypt": false               /* 可选，默认为false，设置是否加密 */
    }
  ],
  "allColumnsSpecified": false,        /* 可选，默认false。为false时，会自动检查data中的字段并推测类型上传到观数，为true时，表示columns中指定了所有字段，那么，data中的多余字段会过滤掉。 */
  "data": [
    {
      "column1": "data1_1",
      "column2": "data1_2"
    },
    {
      "column1": "data2_1",
      "column2": "data2_2"
    }
  ],
  "displayType": "CSV",               /* 可选，标示在观数的显示格式，包括 CSV, EXCEL, DATAFUSION, DATAFLOW, MYSQL, KR3000, PUBLIC, WEIXIN, POSTGRESQL, GREENPLUM, CARD
  "batchFinish": false               /* 可选，默认为false，分批上传时，表示是否是最后一批。 */
}
```

