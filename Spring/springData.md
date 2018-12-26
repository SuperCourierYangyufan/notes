#jpa
1. @Entity                   //表示持久化类
2. @Table(name="JPA_PERSONS")  // 指定数据库名字，可以不写
3. get方法上
  ``` 
	  @GenericGenerator(name = "uuid", strategy = "uuid")//主键策略，不写里面的策略为自增   
      @Id    //主键
	  @GeneratedValue  //自动递增
      @Column(name = "password",length = 50,nullable = false) //数据库名字，长度，不能为空 可以不写
	  @Column(columnDefinition="timestamp default current_timestamp") //设置时间默认为当前时间
      @Temporal(TemporalType.DATE) //对于时间类型指定格式
	  @Transient 忽略该字段,不会生成字段
  ```
4. 新增 persist //添加数据不能设置ID
5. 删除 remove  //删除数据不能删除游离对象
6. 查询 find getReference //第二个为懒加载
7. 更新 merge //若是新对象或游离对象，会创建新对象，然后对新对象进行持久化操作，所以先前的对象没有id 


8. @Repository //dao层注解
9. @PersistenceContext  //使用该注解注入  
  `private EntityManager entityManager;`

10. @Transactional //service层必须使用该注解开启事务   org.springframework.transaction.annotation.Transactional;

11. @JoinColumn(name="外键名称") //设置外键名称 在进行双向一对多是 外键名称需要相同
12. 对象关联都需要joinColumn
13. @ManyToOne(fetch = FetchType.LAZY,cascade = {CascadeType.PERSIST,CascadeType.MERGE}) //多对一  开启懒加载，可以不写 开启级联更新操作(较为方便)
14. @OneToMany(mappedBy="对方表引用属性名")   开启这个放弃维护后.不能用JoinColumn //一对多 用set集合
15. @OneToOne //一对一   -》@JoinColumn(name="外键名称",unique = true) 必须开启唯一约束，一方用@JoinColumn维护就行
16. @ManyToMany  //多对多
  ```
  @JoinTable(name="关联表名字",joinColumns = {@JoinColumn(name = "本表数据库外键名",referencedColumnName = "主键数据库名")}    
                              ,inverseJoinColumns = {@JoinColumn(name="关联表数据库外键名",referencedColumnName = "关联主键数据库名")})
  ```
  * 另外一方在@ManyToMany(mappedBy="对方表中set的引用名",cascade = {CascadeType.PERSIST,CascadeType.MERGE})
  * 双发都必须get().add()操作保存
  * 可以不写referencedColumnName


17. JQPL
    * entityManager.createQuery(jpql);得到query查询 占位符为1开始
    * 可以 select new Item(i.item_id,i.item_name) from item i where i.item_id > ? 来包装获得对象
    * getResultList() 获得多个 getSingleResult() 获得一个
    * entityManager.createNativeQuery(sql) //原生sql
    * 在实体类上@NamedQuery(name="名字",query="查询语句")  =》entityManager.createNamedQuery("名字").setParameter()




18. springdata-jpa (属性不能带下划线)
  * 自定义接口extends Repository<Person,Integer> //第一个参数，处理的对象,第二个是主键的类型
  * 或者通过@RepositoryDefinition(domainClass = Person.class,idClass = Integer.class)
  * 查询时  =》 以find|read|get开头
        * 涉及查询条件，以关键字连接，首字母大写
        * 以And进行条件连接，并且属性名称的个数要与参数位置一一对应
        * 支持级联操作，不过当名字相同时，会优先查询类中名字
  * 可以创建方法,在上面使用@query("jpql语句"),自定义方法
        *占位符有两种  
			* ?1 , ?2  参数与？number  要一一对应  
            * 名字一，:名字二   在参数前@Param("名字对应")  
        * @Query(value = "sql语句",nativeQuery = true)  //原生sql查询
        * @Modifying 在@query上时，可以使用jpql的update 和 delete方法 ,但是不能用save  
		* 使用事务
  * 正常情况下的子接口，应该继承CrudRepository接口 有save等方法  save=update
  * 分页和排序的子接口 PagingAndSortingRepository
	*  findAll(page)  分页信息 PageRequest page = new PageRequest(当前页数(从0开始),每页多少数据,sort);
    *  findAll(sort)  sort分装排序信息   Sort sort = new Sort(可以是多个order)    new order(Direction.DESC,"排序属性")
  * JpaRepository PagingAndSortingRepository的扩展   saveAndFlush() = merge方法
  * JpaSpecification 里面可以进行带查询条件的分页

19. springdata-redis
 ```
      @Autowired
      private RedisTemplate redisTemplate;
	  
		  redisTemplate.opsForValue();//操作字符串
		  redisTemplate.opsForHash();//操作hash
		  redisTemplate.opsForList();//操作list
		  redisTemplate.opsForSet();//操作set
		  redisTemplate.opsForZSet();//操作有序set
		  redisTemplate.expire(user.getId(),randomKeyTime, TimeUnit.SECONDS);    第三个个long型 第四个设置为秒
		  ops.set(key,value,10L, TimeUnit.SECONDS); 第三个个long型 第四个设置为秒
```
