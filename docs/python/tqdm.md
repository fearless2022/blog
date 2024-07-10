## 安装

* ```python
  pip install tqdm
  ```

## 使用

* ```python
  from tqdm import tqdm, trange
  import time
  import pandas as pd
  
  
  def loading_1():
      for _ in tqdm(range(100), desc="loading"):
          time.sleep(0.05)
  
  
  def loading_2():
      df = pd.DataFrame({"a": range(100)})
      for _ in tqdm(df.itertuples()):
          time.sleep(0.05)
  
  
  if __name__ == '__main__':
      loading_1()
      loading_2()
  ```