## Caused by: org.yaml.snakeyaml.scanner.ScannerException:  while scanning for the next token found character '@' that cannot start any token. (Do not use @ for indentation)

* ```bash
  尝试 maven reimport ，问题解决
  ```


## Idea运行报错Error running ‘Application‘: Command line is too long的解决方法

* ```xml
  找到项目里面的.idea/workspace.xml文件
  找到<component name="PropertiesComponent"></component >
  在component 标签里加一行 <property name="dynamic.classpath" value="true" />
  ```

## Caused by: java.lang.IllegalStateException: Method has too many Body parameters

* ```tex
  在方法的参数上加上@RequestParam注解（同时可以用多个）即可
  这里有两个参数，加上两个@RequestParam注解。如果有一个对象参数是要用@RequestBody注解的（这个注解同时只能用一个）
  ```

## 支付宝调用接口保存：cert check fail: ALIPAY_CERT_SN is Empty!

* ```bash
  调用方法设置错误，一般是由于普通公钥签名，但response使用了公钥证书的方法
  公钥和公钥证书签名服务端response的方法不同，详见RSA2和公钥证书签名验签的区别
  
  java语言需将公钥证书的certificateExecute（）修改为RSA2密钥的execute（）
  java服务端请求，如当面付//RSA2密钥请求response = alipayClient.execute(request)
  公钥证书请求请求response = alipayClient.certificateExecute(request)
  php服务端请求，如当面付//RSA2普通公钥和公钥证书请求方法一致。$response = $aop->execute($request)
  .net服务端请求，如当面付//RSA2密钥请求response = client.Execute(request)
  公钥证书请求请求response = client.CertificateExecute(request)
  ```

## Error:(1, 1) java: 非法字符: ‘\ufeff’  Error:(1, 10) java: 需要class, interface或enum

* ```tex
  Eclipse可以智能的把UTF-8+BOM文件转为普通的UTF-8文件，IDEA没有这个智能转换
  用IDEA转换，先转换为GBK，再转回UTF-8
  ```
