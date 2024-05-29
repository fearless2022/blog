## Mock 静态方法

* ```java
  // 导包，注意如果导入junit.jupiter执行时会报错，需要将方法全部改为public，@BeforeEach改为@Before
  import org.junit.Before;
  import org.junit.Test;
  import org.junit.runner.RunWith;
  // 类注解
  @RunWith(PowerMockRunner.class)
  @PrepareForTest({SecurityUtils.class})
  @Transactional(transactionManager = "transactionManager")
  @SpringBootTest
  // mock
  PowerMockito.mockStatic(SecurityUtils.class);
  PowerMockito.when(SecurityUtils.getDeptId()).thenReturn("1");
  ```

## Mock BaseMapper

* ```java
  // mock mapper
  @Mock
  private ShopMallEntranceMapper shopMallEntranceMapper;
  // cock baseMapper
  ReflectionTestUtils.setField(shopMallEntranceService, "baseMapper", shopMallEntranceMapper);
  // 使用对应mapper代替baseMapper调用即可
  Mockito.when(shopMallEntranceMapper.insert(any())).thenReturn(1);
  ```

## Mock mybatis-plus service this.saveBatch方法

* ```java
  // Mockito.spy()可以在真实对象上创建间谍（类似于浅克隆），尝试在创建的间谍对象上打桩
  CouponAndPoolRelationshipV2ServiceImpl service = Mockito.spy(couponAndPoolRelationshipV2Service);
  // 用spy service对象调用saveBatch
  Mockito.when(service.saveBatch(Mockito.any())).thenReturn(Boolean.TRUE);
  ```

## Mock mybatis-plus service this.remove方法

* ```java
  // 不能用service或spy service直接mock，只能mock mapper
  Mockito.when(couponAndPoolRelationshipV2Mapper.delete(Mockito.any())).thenReturn(1);
  ```
