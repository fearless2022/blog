## jpa自定义sql联表查询

* ```java
  @Query(value = "select name,device_name from product p inner join gateway_device g on p.id = g.product_id",nativeQuery = true)
  List<Object[]> findListBySql();
  
  @Query(value = "from Product p inner join GatewayDevice g on p.id = g.product.id")
  List<Object[]> findListByJpql();
  
  @Test
  public void testSql(){
      List<Object[]> ls = gatewayDeviceDao.findListBySql();
      System.out.println(ls.toString());
  }
  
  @Test
  public void testJpql(){
      List<Object[]> ls = gatewayDeviceDao.findListByJpql();
      for (Object[] o: ls){
          String js = JSON.toJSONString(o[0]);
          Product product = JSON.parseObject(js, Product.class);
          String js1 = JSON.toJSONString(o[1]);
          GatewayDevice gd = JSON.parseObject(js1, GatewayDevice.class);
          System.out.println(product.toString());
          System.out.println(gd.toString());
      }
  }
  ```

## specification构造mysql函数条件

* ```java
  predicates.add(criteriaBuilder.equal(criteriaBuilder.function("year", String.class, root.get("alarmAt")), dto.getAlarmAt().getYear()));
  ```

## specification构造in条件

* ```java
  // 查询项目id
  List<Long> projectIds = getProjectIdsByNameLike(dto.getProjectName());
  CriteriaBuilder.In<Long> in = criteriaBuilder.in(deviceJoin.get("project"));
  for (Long id: projectIds){
      in.value(id);
  }
  predicates.add(in);
  ```