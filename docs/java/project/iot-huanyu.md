## 设备信息获取，解析，告警判断，websocket告警信息推送

* ```mermaid
  graph LR
  iot-transport((iot-transport))-->MqttMessageHandler(handleMessage:解析deviceId,protocolId)-->sendToProtocol{topic:raw.device.data}-->iot-protocol((iot-protocol))-->protocol.KafkaMessageHandler(handleRawDeviceDataMessage:加载js脚本,解析payload)-->sendToData{topic:device.data}-->iot-data((iot-data))-->data.KafkaMessageHandler(写入ES)
  sendToData-->iot-alert((iot-alert))-->alert.KafkaMessageHandler(handleMessage:规则引擎校验)-->alert.KafkaMessageHandler.getDefaultRulesEngine(getDefaultRulesEngine:校验成功,发送消息)-->alert.sendToData{topic:device.alert}-->iot-data
  sendToData-->iot-push((iot-push))-->websocket(websocket)
  alert.sendToData-->iot-push((iot-push))
  ```

## 命令下发，响应流程

### cmd

* ```json
  # 收到下发的命令 
  {"cmdName":"turn_switch","deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","paramsJson":"{\"operation\":0}","uuid":"weqw1212314124"}
  ```

### 解析脚本

* ```javascript
  function turn_switch_SPRODX2021010816562711U642RMLWX1(cmdInfo){params=JSON.parse(cmdInfo['paramsJson']);cmd={'bid':501,'mid':cmdInfo['uuid'],'Children':[{'ID':parseInt(cmdInfo['customDeviceId']),'MO':params['operation']==0?2:params['operation']}]};return JSON.stringify(cmd)}function turn_switch_SPRODX20210108165710VHFGQQ5YIM64(cmdInfo){params=JSON.parse(cmdInfo['paramsJson']);cmd={'bid':501,'mid':cmdInfo['uuid'],'Children':[{'ID':parseInt(cmdInfo['customDeviceId']),'MO':params['operation']==0?2:params['operation']}]};return JSON.stringify(cmd)}function turn_switch_SPRODX202101081707116DFRYDYCNXQ7(cmdInfo){params=JSON.parse(cmdInfo['paramsJson']);cmd={'bid':201,'mid':cmdInfo['uuid'],'Children':[{'ClientID':cmdInfo['customDeviceId'],'MoterOperation':params['operation']==0?2:params['operation']}]};return JSON.stringify(cmd)}function turn_switch_SPRODX20210108170559TLJJQ49SOYJR(cmdInfo){params=JSON.parse(cmdInfo['paramsJson']);cmd={'bid':201,'mid':cmdInfo['uuid'],'Children':[{'ClientID':cmdInfo['customDeviceId'],'MoterOperation':params['operation']==0?2:params['operation']}]};return JSON.stringify(cmd)}

### cmd

* ```json
  # 向网关发送命令 
  {"bid":201,"mid":"96064d7cd25b4b8eb91d1bd679837f93","Children":[{"ClientID":"D360000101","MoterOperation":2}]}
  ```

### cmd-->mqtt

* ```json
  topic：raw.device.cmd
  向kafka发送数据 {"customDeviceId":"D360000101","customGatewayId":"GW00000130","deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","gatewayId":"GDEVXX20210110104810KSEJKZBYUNI2","payLoad":"eyJiaWQiOjIwMSwibWlkIjoiOTYwNjRkN2NkMjViNGI4ZWI5MWQxYmQ2Nzk4MzdmOTMiLCJDaGlsZHJlbiI6W3siQ2xpZW50SUQiOiJEMzYwMDAwMTAxIiwiTW90ZXJPcGVyYXRpb24iOjJ9XX0=","timestamp":1610594283167,"uuid":"96064d7cd25b4b8eb91d1bd679837f93"}
  ```

### mqtt

* ```json
  收到下发的命令消息 {"customDeviceId":"D360000101","customGatewayId":"GW00000130","deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","gatewayId":"GDEVXX20210110104810KSEJKZBYUNI2","payLoad":"eyJiaWQiOjIwMSwibWlkIjoiOTYwNjRkN2NkMjViNGI4ZWI5MWQxYmQ2Nzk4MzdmOTMiLCJDaGlsZHJlbiI6W3siQ2xpZW50SUQiOiJEMzYwMDAwMTAxIiwiTW90ZXJPcGVyYXRpb24iOjJ9XX0=","timestamp":1610594283167,"uuid":"96064d7cd25b4b8eb91d1bd679837f93"}
  ```

### mqtt-->网关-->设备

* ```json
  topic：to/epa/GW00000130
  向自定义topic to/epa/GW00000130发送数据 {"customDeviceId":"D360000101","customGatewayId":"GW00000130","deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","gatewayId":"GDEVXX20210110104810KSEJKZBYUNI2","payLoad":"eyJiaWQiOjIwMSwibWlkIjoiOTYwNjRkN2NkMjViNGI4ZWI5MWQxYmQ2Nzk4MzdmOTMiLCJDaGlsZHJlbiI6W3siQ2xpZW50SUQiOiJEMzYwMDAwMTAxIiwiTW90ZXJPcGVyYXRpb24iOjJ9XX0=","timestamp":1610594283167,"uuid":"96064d7cd25b4b8eb91d1bd679837f93"}
  ```

### 设备-->网关-->mqtt-->protocal

* ```json
  topic：raw.device.data
  protocal：
  收到数据deviceData {"customGatewayId":"GW00000130","payLoad":"ewoJIlN5c1RpbWUiOgkiMjAyMS0wMS0wNSAwOTozODo0MCIsCgkiYmlkIjoJMTAxLAoJIm1pZCI6CSI2REE3QTI4RC01OTk1LUYyMEEtNzRFNi0xM0ZGQzE4Rjc1QUMiLAoJIkNoaWxkcmVuIjoJW3sKCQkJIkNsaWVudElEIjoJIkQzNjAwMDAxMDEiLAoJCQkiVm9sdGFnZSI6CVsyMjE0NzgsIDIyMTQ3OCwgMjIxNDc4LCAwXSwKCQkJIkN1cnJlbnQiOglbMCwgMCwgMCwgMF0sCgkJCSJQb3dlciI6CVswLCAwLCAwLCAwXSwKCQkJIkVuZXJneSI6CVswLCAwLCAwLCAwXSwKCQkJIlRlbXBlcmF0dXJlIjoJWzM1NDgsIDI1MzAsIDIzNTcsIDBdLAoJCQkiRWxlY3RyaWNTdGF0dXMiOgkwLAoJCQkiU3dpdGNoU3RhdHVzIjoJMSwKCQkJIlVzYXJ0Q29tbSI6CTAKCQl9XQp9","protocolId":"PROTOX20201215134208ULXA2017SQDE","timestamp":1610594284112}
  ```

### protocal-->cmd

* ```json
  topic：raw.device.resp
  raw.device.resp:RawDeviceResp(uuid=96064d7cd25b4b8eb91d1bd679837f93, code=200, msg=null, data={"result":true,"mid":"96064d7cd25b4b8eb91d1bd679837f93","bid":201})

### cmd-->push

* ```json
  topic：device.resp
  向kafka发送数据 {"code":200,"data":{"result":true,"mid":"96064d7cd25b4b8eb91d1bd679837f93","bid":201},"deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","uuid":"96064d7cd25b4b8eb91d1bd679837f93"}
  ```

### push-->websocket-->客户端

* ```json
  iot-push receive data,DeviceResp={"code":200,"data":{"result":true,"mid":"96064d7cd25b4b8eb91d1bd679837f93","bid":201},"deviceId":"SDEVXX20210110105452HQNMD0VCXS3O","uuid":"96064d7cd25b4b8eb91d1bd679837f93"}
  ```