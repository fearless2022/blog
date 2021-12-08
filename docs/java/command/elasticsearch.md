## 获取所有的index列表

* ```json
  GET /_cat/indices
  ```

##  获取索引为001的index的mapping

* ```json
  GET /001/_mapping
  ```

## 获取某个设备过去12个小时，电流和电压的平均值

* ```json
  GET /001/_search
  {
    "size": 0,
    "query": {
      "bool": {
        "must": [
          {
            "match": {
              "deviceId": "DEVXXX20201216171317CG86RGE4TWGV"
            }
          }
        ],
        "filter": [
          {
            "range": {
              "timestamp": {
                "gte": "now-24h/h",
                "lte": "now/h"
              }
            }
          }
        ]
      }
    }, 
    "aggs": {
      "tsagg": {
        "date_histogram": {
          "field": "timestamp",
          "min_doc_count": 0, 
          "interval": "hour",
          "time_zone": "Asia/Shanghai", 
          "format": "yyyy-MM-dd HH:mm:ss"
        },
        "aggs": {
          "CurrentA": {
            "avg": {
              "field": "data.CurrentA"
            }
          },
          "VoltageA": {
            "avg": {
              "field": "data.VoltageA"
            }
          }
        }
      }
    }
  }
  ```

## 查询设备最近的消息

* ```json
  GET /001/_search
  {
    "size": 0, 
    "query": {
      "bool": {
        "must": [
          {
            "terms": {
              "deviceId": [
                "DEVXXX20201216171317CG86RGE4TWGV",
                "DEVXXX20180101132204UKHYHCGRLYLI"
              ]
            }
          }
        ],
        "filter": [
          {
            "range": {
              "timestamp": {
                "gte": "now-2h/h",
                "lte": "now/h"
              }
            }
          }
        ]
      }
    },
    "aggs": {
      "devices": {
        "terms": {
          "field": "deviceId"
        },
        "aggs": {
          "lastdata": {
            "top_hits": {
              "size": 1,
              "sort": [{
                "timestamp": {
                  "order": "desc"
                }
              }]
            }
          }
        }
      }
    }
  }
  ```

## 统计一个组织下，各个项目的设备数量（注意索引设置了通配符00*）

* ```json
  GET /00*/_search
  {
    "size": 0, 
    "query": {
      "term": {
        "organId": {
          "value": "001"
        }
      }
    },
    "aggs": {
      "projects": {
        "terms": {
          "field": "projectId"
        },
        "aggs": {
          "devices": {
            "cardinality": {
              "field": "deviceId"
            }
          }
        }
      }
    }
  }
  ```

## 查询一个组织下，各个产品包含的设备数量（注意索引设置了通配符00*）

* ```json
  GET /00*/_search
  {
    "size": 0, 
    "query": {
      "term": {
        "organId": {
          "value": "001"
        }
      }
    },
    "aggs": {
      "products": {
        "terms": {
          "field": "productId"
        },
        "aggs": {
          "devices": {
            "cardinality": {
              "field": "deviceId"
            }
          }
        }
      }
    }
  }
  ```

## 获取某个组织下每个项目的总设备数量和在线设备数量

* ```json
  GET /00*/_search
  {
    "size": 0, 
    "query": {
      "term": {
        "organId": {
          "value": "001"
        }
      }
    },
    "aggs": {
      "projects": {
        "terms": {
          "field": "projectId"
        },
        "aggs": {
          "total_devices": {
            "cardinality": {
              "field": "deviceId"
            }
          },
          "times": {
            "date_range": {
              "field": "timestamp",
              "ranges": [
                {
                  "from": "now-2m/m",
                  "to": "now/m"
                }
              ]
            },
            "aggs": {
              "online_devices": {
                "cardinality": {
                  "field": "deviceId"
                }
              }
            }
          }
        }
      }
    }
  }
  ```

