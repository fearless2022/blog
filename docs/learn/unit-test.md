## Mock 静态方法

* ```java
  // 导包，注意如果导入junit.jupiter执行时会报错，需要将类、方法全部改为public，@BeforeEach改为@Before
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

## Mock mybatisPlus baseMapper

* ```java
  // mock mapper
  @Mock
  private ShopMallEntranceMapper shopMallEntranceMapper;
  // mock baseMapper
  ReflectionTestUtils.setField(shopMallEntranceService, "baseMapper", shopMallEntranceMapper);
  // 使用对应mapper代替baseMapper调用即可
  Mockito.when(shopMallEntranceMapper.insert(Mockito.any())).thenReturn(1);
  ```

## Mock mybatisPlus service this.saveBatch方法

* ```java
  // Mockito.spy()可以在真实对象上创建间谍（类似于浅克隆），尝试在创建的间谍对象上打桩，用于mock本类this调用
  CouponAndPoolRelationshipV2ServiceImpl service = Mockito.spy(couponAndPoolRelationshipV2Service);
  // 用spy service对象调用saveBatch
  Mockito.when(service.saveBatch(Mockito.any())).thenReturn(Boolean.TRUE);
  ```

## Mock mybatisPlus service this.remove方法

* ```java
  // 不能用service或spy service直接mock，只能mock mapper
  Mockito.when(couponAndPoolRelationshipV2Mapper.delete(Mockito.any())).thenReturn(1);
  ```

## Mock 特殊类

* ```java
  // Mock文件类
  MockMultipartFile file = new MockMultipartFile("测试", "测试".getBytes());
  // Mock HttpServletRequest
  MockHttpServletRequest request = new MockHttpServletRequest();
  ```

## Mock MVC调用

* ```java
  // 注入WebApplicationContext
  @Autowired
  protected WebApplicationContext wac;
  // 初始化MockMvc对象
  MockMvc mockMvc = MockMvcBuilders.webAppContextSetup(wac).build();  
  // Mock MVC
  String reqVo = "XK5H";
  mockMvc.perform(MockMvcRequestBuilders.get("/area/listByParentAuth")
                  .contentType(MediaType.APPLICATION_JSON)
                  .param("parentAuth", reqVo))
          .andDo(MockMvcResultHandlers.print())
          .andExpect(MockMvcResultMatchers.status().isOk());
  ```

## Mock 错误记录

* ```java
  // 1.错误解决：Caused by: java.security.NoSuchAlgorithmException: class configured for SSLContext: sun.security.ssl.SSLContextImpl$TLSContext not a SSLContext
  // 加类注解
  @PowerMockIgnore("javax.net.ssl.*")
  // 2.错误解决：com.baomidou.mybatisplus.core.exceptions.MybatisPlusException: can not find lambda cache for this entity[com.domain.ThirdJinkeActivityMemberScore]
  // lambda cache 为null问题解决
  TableInfoHelper.initTableInfo(new MapperBuilderAssistant(new MybatisConfiguration(), ""), ThirdJinkeActivityMemberScore.class);
  ```
