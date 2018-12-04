# MybatisPlus

### * 安装
   1. 引入包
   2. @MapperScan扫描
   3. extends BaseMapper<对象>  
   4. 根据规则编写方法

### * 注解
   1. @TableName("表名") //对应表明
   2. @TableId(type = IdType.规则)//选择主键自增规则 
   @TableField(value = "数据库对应名字",exist=(false表示该字段数据库不存在))

### * 使用
   1. 大部分使用,请使用数据库的字段如XXMap...,条件构造器
   2. 条件构造器`xxMapper.selectList( new QueryWrapper<employee>().between("属性",值1,值2).eq("属性",值))`

### * 分页
   1. 根据官网注入
   2 代码:返回page对象
        ``` 
            Page<employee> page = new Page<>(3,2);
            employeeMapper.selectPage(page, wrapper条件);
        ```