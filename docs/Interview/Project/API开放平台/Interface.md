# <center>接口文档</center>

## 数据库表单设计

### 接口信息表

- `id` 接口id bigint 
- `name` 接口名称 varchar(256) 
- `url` 接口地址  varchar(256)
- `method` 接口类型 varchar(256)
- `request_header` 请求头 text 
- `response_header` 响应头 text
- `description` 描述 varchar(256)
- `status` 接口状态 int 0-关闭 1-开启
- `is_delete` tinyint 0-未删除 1-已删除
- `create_time` dataTime
- `update_time` dataTime

