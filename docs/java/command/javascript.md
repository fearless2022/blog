## protocol脚本调试

* ```javascript
  <!DOCTYPE html>
  <html>
  <body>
  
  <h2>JavaScript 函数</h2>
  
  <p>本例调用了一个执行计算的函数，然后返回结果：</p>
  
  <p id="demo"></p>
  
  <script>
  function getDataType(e) {
      obj = JSON.parse(e);
      type = "data";
      if ("bid" in obj) {
          if (obj["bid"] == 201 || obj["bid"] == 501) {
              type = "push"
          } else if (obj["bid"] == 207 || obj["bid"] == 507) {
              type = "data"
          } else if (obj["bid"] == 209 || obj["bid"] == 509) {
              type = "setting"
          } else {
              type = "cmd"
          }
      }
      return type
  }
  var a = "{\n" +"\t\"bid\":\t209,\n" +"\t\"mid\":\t\"0CB6205D-9200-4E02-ACF9-12C12688A894\",\n" +"\t\"result\":\ttrue,\n" +"\t\"message\":\t0\n" +"}";
  document.getElementById("demo").innerHTML = getDataType(a);
  </script>
  
  </body>
  </html>
  ```

## cmd脚本调试
* ```javascript
  <!DOCTYPE html>
  <html>
  <body>
  
  <h2>JavaScript 函数</h2>
  
  <p>本例调用了一个执行计算的函数，然后返回结果：</p>
  
  <p id="demo"></p>
  
  <script>
  function send_parameter_to_gateway(i) {
      cmd = {
          bid: 504,
          mid: i["uuid"],
          Threshhold: i["threshold"],
          Data: i["data"]
      };
      return JSON.stringify(cmd)
  }
  var a ={"data":100.0,"parameter":0,"cmdName":"send_parameter_to_gateway","threshold":10,"customGatewayId":"GW00000186","uuid":"E32BA74F-9519-429C-AE2F-DAA6C9FD3124","gatewayId":"GDEVXX20210406141614QF4IEPI401CJ"};
  document.getElementById("demo").innerHTML = send_parameter_to_gateway(a);
  </script>
  
  </body>
  </html>
  ```