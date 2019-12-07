#加密算法之Itsdangerous

* 安装

  ```python
  pip install itsdangerous
  ```

* 代码块

  ```python
  from itsdangerous import TimedJSONWebSignatureSerializer as serializer 
  
  # tem = serializer(secret_key='123456789',expires_in=30)
  # t = tem.dumps({'uid':1}).decode()
  # print(t)
  # print(tem.loads(t))
  
  # 优化代码后的写法
  class Itsdangerouser:
      # 初始化函数
      def __init__(self, secret_key='123456789', expires_in=3700):
          self.tep = serializer(secret_key=secret_key, expires_in=expires_in)
  
      # 加密 ra是对象
      def decry(self, ra):
          return self.tep.dumps(ra).decode()
  
      # 解密  raw是字符串
      def encry(self, raw):
          if not raw:
              return {}
          return self.tep.loads(raw)
  its = Itsdangerouser(secret_key='123456789')
  t = its.decry({'uid': 1})
  print(t)
  t1 = its.encry(t)
  print(t1)
  ```

  # 实验楼表结构

  * 课程

| -字段名     | -类型            | 解释                      |
| ----------- | ---------------- | ------------------------- |
| id          |                  |                           |
| name        | Charfile(32)     | 课程名称                  |
| desc        | Charfile(255)    | 课程描述                  |
| preview     | int（）外建      | 角标                      |
| img         | Charfile(255)    | 图片   20191029148542.jpg |
| addtime     | datatime         | 添加时间                  |
| status      | tinyint          | 课程状态 0下架 1上架      |
| category_id | foreignkey   int | 方向id                    |
| tag_id      | int              | 标签id                    |



* 方向表

| -字段名 | -类型      | -解释              |
| ------- | ---------- | ------------------ |
| id      | int        | 主键自增           |
| name    | Charfile32 | 方向名称           |
| status  | tinyint    | 方向状态0下架1上架 |
| addtime | datatime   | 添加时间           |



* 标签表

| -字段名 | -类型      | -解释              |
| ------- | ---------- | ------------------ |
| id      | int        | 主键自增           |
| name    | Charfile32 | 标签名称           |
| status  | tinyint    | 标签状态0下架1上架 |
| addtime | datatime   | 添加时间           |
| cate_id | int        | 方向id             |
|         |            |                    |

* 角标

| -字段   | -类型        | -解释              |
| ------- | ------------ | ------------------ |
| id      | int          | 主键自增           |
| name    | Charfile(32) | 角标名称           |
| status  | tinyint      | 角标状态0下架1上架 |
| img     | Cahrfile     | 角标图片           |
| addtime | datatime     | 添加时间           |
|         |              |                    |

















