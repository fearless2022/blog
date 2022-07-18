## 查询网关下设备的实时数据

* send

* ```json
  topic:
  to/epa/GW00000134
  command:
  {"bid":207,"mid":"E1C52FDD-DC6B-41B3-86E3-1E77975F0DF0"}
  ```

* receive

* ```json
  topic:
  from/epa/GW00000134
  response:
  {
  	"SysTime":	"2021-01-01 08:36:52",
  	"bid":	207,
  	"mid":	"E1C52FDD-DC6B-41B3-86E3-1E77975F0DF0",
  	"Children":	[{
  			"ClientID":	"D260000148",
  			"Voltage":	[232473, 0, 0, 0],
  			"Current":	[0, 0, 0, 0],
  			"Power":	[0, 0, 0, 0],
  			"Energy":	[0, 0, 0, 0],
  			"Temperature":	[3255, 2828, 0, 0],
  			"ElectricStatus":	0,
  			"SwitchStatus":	0,
  			"UsartComm":	0
  		}]
  }
  ```

## 网关解绑设备

* send

* ```json
  {"bid":206,"mid":"FBB09AB9-A6B4-4333-B7D8-CB24BD809672","Children":[{"ClientID":"D260000148"}]}
  ```

* receive

* ```json
  {
  	"bid":	206,
  	"mid":	"FBB09AB9-A6B4-4333-B7D8-CB24BD809672",
  	"result":	true,
  	"message":	0
  }
  ```
