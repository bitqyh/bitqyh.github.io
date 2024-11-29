# Mybatis/MybatisPlus

## 依赖注入

```xml
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>${mybatis-plus.version}</version>
            </dependency>
```

## XML映射文件

**resultmap**

例：

```xml
<resultMap id="SelectByIdPage" type="com.atguigu.lease.web.app.vo.history.HistoryItemVo" autoMapping="true">
    <id column="id" property="id"/>
    <result column="room_id" property="roomId"/>
    <collection property="roomGraphVoList" ofType="com.atguigu.lease.web.app.vo.graph.GraphVo"
                select="selectGraphVoByRoomId" column="room_id" autoMapping="true"/>
</resultMap>
```

`<collection>`标签对应返回对象为列表， 用`ofType` 字段表示路径

`<association>` 标签对应返回对象为实例对象， 用`javaType` 字段表示路径

- select代表内嵌查询，用于分页查询结果

- column字段代表传入字段名称

  **注：添加column之后对应字段无法自动注入**

`<id>` 标签标识主键， 其余字段用`<result>` 标签

- **column字段对应查询结果中字段名称**
- **property字段对应返回对象中字段名称**



## 通用Mapper

通过`BaseMapper` 接口提供通用的 CRUD 方法

```java
删除操作:
deleteById(Serializable id)
deleteBatchIds(Collection<? extends Serializable> idList)
deleteByMap(Map<String, Object> columnMap)
delete(T entity)
更新操作:
updateById(T entity)
updateBatch(List<T> entityList)
update(T entity, UpdateWrapper<T> updateWrapper)
查询操作:
selectById(Serializable id)
selectBatchIds(Collection<? extends Serializable> idList)
selectByMap(Map<String, Object> columnMap)
selectList(Wrapper<T> queryWrapper)
selectCount(Wrapper<T> queryWrapper)
selectOne(Wrapper<T> queryWrapper)
分页查询:
<T>IPage<T> selectPage(IPage<T> page, Wrapper<T> queryWrapper)
<T>IPage<T> selectMapsPage(IPage<T> page, Wrapper<T> queryWrapper)


```

## Service层自动装配

**通过`@MapperScan` 注解指定mapper包** 例：

```java
@Configuration
@MapperScan("com.atguigu.lease.web.*.mapper")
public class MybatisPlusConfiguration {

}
```

## **自动填充**

**自动填充创建时间和更新时间**

```java
@Component
public class MybatisMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.strictInsertFill(metaObject, "createTime", Date.class, new Date());
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.strictUpdateFill(metaObject, "updateTime", Date.class, new Date());
    }
}
```

## 逆向工程

**自动生成基本CRUD代码**

- 根据数据库表结构自动生成Mapper接口， XML配置文件和POJO类

- 基于数据库表结构生成对应的Entity类、Mapper接口、Mapper XML文件等。

- 自动生成基本的增删改查操作代码。

  

## 逻辑删除功能

- **逻辑删除功能**

  由于数据库中所有表均采用逻辑删除策略，所以查询数据时均需要增加过滤条件`is_deleted=0`。

  上述操作虽不难实现，但是每个查询接口都要考虑到，也显得有些繁琐。为简化上述操作，可以使用Mybatis-Plus提供的逻辑删除功能，它可以自动为查询操作增加`is_deleted=0`过滤条件，并将删除操作转为更新语句。具体配置如下，详细信息可参考[官方文档](https://baomidou.com/pages/6b03c5/#%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95)。

  - 步骤一：在`application.yml`中增加如下内容

    ```yml
    mybatis-plus:
      global-config:
        db-config:
          logic-delete-field: flag # 全局逻辑删除的实体字段名(配置后可以忽略不配置步骤二)
          logic-delete-value: 1 # 逻辑已删除值(默认为 1)
          logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
    ```

  - 步骤二：在实体类中的删除标识字段上增加`@TableLogic`注解

    ```java
    @Data
    public class BaseEntity {
    
        @Schema(description = "主键")
        @TableId(value = "id", type = IdType.AUTO)
        private Long id;
    
        @Schema(description = "创建时间")
        @JsonIgnore
        private Date createTime;
    
        @Schema(description = "更新时间")
        @JsonIgnore
        private Date updateTime;
    
        @Schema(description = "逻辑删除")
        @JsonIgnore
        @TableLogic
        @TableField("is_deleted")
        private Byte isDeleted;
    
    }
    ```

    **注意**：

    逻辑删除功能只对Mybatis-Plus自动注入的sql起效，也就是说，对于手动在`Mapper.xml`文件配置的sql不会生效，需要单独考虑。

- **忽略特定字段**

  通常情况下接口响应的Json对象中并不需要`create_time`、`update_time`、`is_deleted`等字段，这时只需在实体类中的相应字段添加`@JsonIgnore`注解，该字段就会在序列化时被忽略。

  具体配置如下，详细信息可参考Jackson[官方文档](https://github.com/FasterXML/jackson-annotations#annotations-for-ignoring-properties)。

  ```java
  @Data
  public class BaseEntity {
  
      @Schema(description = "主键")
      @TableId(value = "id", type = IdType.AUTO)
      private Long id;
  
      @Schema(description = "创建时间")
      @JsonIgnore
      @TableField(value = "create_time")
      private Date createTime;
  
      @Schema(description = "更新时间")
      @JsonIgnore
      @TableField(value = "update_time")
      private Date updateTime;
  
      @Schema(description = "逻辑删除")
      @JsonIgnore
      @TableField("is_deleted")
      private Byte isDeleted;
  
  }
  ```

**知识点**：

保存或更新数据时，前端通常不会传入`isDeleted`、`createTime`、`updateTime`这三个字段，因此我们需要手动赋值。但是数据库中几乎每张表都有上述字段，所以手动去赋值就显得有些繁琐。为简化上述操作，我们可采取以下措施。

- `is_deleted`字段：可将数据库中该字段的默认值设置为0。

- `create_time`和`update_time`：可使用mybatis-plus的自动填充功能，所谓自动填充，就是通过统一配置，在插入或更新数据时，自动为某些字段赋值，具体配置如下，详细信息可参考[官方文档](https://baomidou.com/pages/4c6bcf/)。

  - 为相关字段配置触发填充的时机，例如`create_time`需要在插入数据时填充，而`update_time`需要在更新数据时填充。具体配置如下，观察`@TableField`注解中的`fill`属性。

    ```java
    @Data
    public class BaseEntity {
    
        @Schema(description = "主键")
        @TableId(value = "id", type = IdType.AUTO)
        private Long id;
    
        @Schema(description = "创建时间")
        @JsonIgnore
        @TableField(value = "create_time", fill = FieldFill.INSERT)
        private Date createTime;
    
        @Schema(description = "更新时间")
        @JsonIgnore
        @TableField(value = "update_time", fill = FieldFill.UPDATE)
        private Date updateTime;
    
        @Schema(description = "逻辑删除")
        @JsonIgnore
        @TableLogic
        @TableField("is_deleted")
        private Byte isDeleted;
    
    }
    ```

  - 配置自动填充的内容，具体配置如下

    在**common模块**下创建`com.atguigu.lease.common.mybatisplus.MybatisMetaObjectHandler`类，内容如下：

    ```java
    @Component
    public class MybatisMetaObjectHandler implements MetaObjectHandler {
        @Override
        public void insertFill(MetaObject metaObject) {
            this.strictInsertFill(metaObject, "createTime", Date.class, new Date());
        }
    
        @Override
        public void updateFill(MetaObject metaObject) {
            this.strictUpdateFill(metaObject, "updateTime", Date.class, new Date());
        }
    }
    ```

  在做完上述配置后，当写入数据时，Mybatis-Plus会自动将实体对象的`create_time`字段填充为当前时间，当更新数据时，则会自动将实体对象的`update_time`字段填充为当前时间。