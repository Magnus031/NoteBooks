# <center>注解</center>

> 下面介绍一下 Mybatis-Plus 中的注解使用

## `@TableField`

[链接地址](https://baomidou.com/reference/annotation/#:~:text=%40-,TableField,-%E8%AF%A5%E6%B3%A8%E8%A7%A3%E7%94%A8%E4%BA%8E)

该注解的作用是用于标记实体类中的非主键字段，告诉`Mybatis-Plus` 如何映射实体类字段到数据库表字段。


### 属性

- `value` : 数据库字段名，如果与实体类字段名一致，可以不用填写,或者如果是驼峰命名的话，也可以不用填写
