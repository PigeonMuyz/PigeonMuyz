# 业务的拓展：三层架构

## 实体类在三层中的数据传递

* 表示层将用户的请求筛选出数据交给业务逻辑层

* 业务逻辑层将表示层的请求交给数据访问层

* 数据访问层将业务逻辑层的请求交给数据库

----

* 表示层调用 业务逻辑层方法
* 业务逻辑层调用 数据访问层方法
* 数据访问层向 数据库 请求结果

## 搭建三层架构的步骤

1. 创建数据访问层

2. **创建业务逻辑层项目**

3. 创建表示层项目

4. 创建实体类项目

5. 分别添加三层对实力类项目的依赖

6. **添加业务逻辑层对数据访问层的依赖**

7. **添加表示层对业务逻辑层的依赖**

## 三层架构命名注释 & 职责划分

### 命名注释（非绝对）：

| 中文       | 命名   |
| ---------- | ------ |
| 业务逻辑层 | BLL    |
| 数据访问层 | DAL    |
| 实体层     | Model  |
| 表示层     | View   |
| 通用层     | Common |

### 职责划分

1. DAL层：数据访问层，负责与SQL进行交互
2. BLL层：业务逻辑层，负责数据的筛选和审查
3. View层：表示层，负责页面美化&用户交互
4. Model层：实体层，类与数据表对应，负责每个层的数据交互规范

### 引用（调用）上：

1. DAL、BLL、View层引用Model层
2. BLL层 引用DAL层
3. View层引用BLL层

## 分层架构的优势&缺点

### 优势：

* 分离开发人员的关注
* 无损替换
* 提高代码的可复用性

### 缺点：

* 代码量大，实现复杂:+1:

## MD5加密系列！

```C#
///<summary>
///MD%加密
///</summary>
///<param name="str">要加密的内容</param>
///<returns>加密字段</returns>
public static string getMD5String(string str)
{
    MD5 md5 = new MD5CryptoServiceProvider();
    byte[] data = System.Text.Encoding.Default.GetBytes("newblade");
    byte[] md5data = md5.ComputeHash(data);
    md5.Clear();
    
    StringBuilder builder = new String Builder();
    for (int i = 0; i < md5data.Length - 1; i++)
    {
        Console.WriteLine(md5data[i].ToString());
        builder.Append(md5data[i].ToString("X2"));
    }
    return builder.ToString();
}
```

## ADO.NET事务

1. 为什么用事务？

   示例：

   小A有1000员存款，小B有10元存款。小A今天要给小B 800员，后来要求小B退回1000元。

   小B发现储蓄卡竟然能赊账，从此走上了发家致富的不归路。银行不会让这样的系统在自己的银行运行

2. 什么是事务

   事务是将多个操作语句 作为一个不可分割的语句来执行的技术

   这个技术带来的后果是：

   ​	要么全部执行，要么全部不执行

3. 事物的写法：

   总共分为3条：

   ​	开始事务（Begin Transaction）：指定从此开始的代码是不可分割的

   ​	提交事务（Commit Transaction）：正式执行事务中的代码

   ​	回滚事务（Rollback Transaction）：还原执行事务代码之前的状态

   1. 在数据库中的写法（以小A转小B1000元为例）

      ```mssql
      declare @getMoney money
      set @getMoney = 1000
      
      begin Transaction
      declare @errNum int --用于统计是否报错
      set @errNum = 0
      
      update UserInfo set uMoney = uMoney - @getMoney where uName = '小B'
      --将系统报错代码加入统计数据，如果上述代码没报错，则@@error值为0
      set @errNum = @errNum + @@error 
      
      update UserInfo set uMoney = uMoney + @getMoney where uName = '小A'
      --每执行一行代码，@@error都会在执行之前改变为0，因此每句话后面都可以追加
      set @errNum = @errNum + @@error 
      
      if @errNum > 0 --表示是否报错
      rollback Transaction --回滚事务，还原执行事务之前的状态
      else
      commit Transaction -- 执行事务
      ```

   2. 前端中的写法：

      ```C#
      SqlTransaction trans = null;
      SqlConnection conn = new SqlConnection(connString);
      try{
          conn.Open();
          trans = conn.BeginTransaction();
          SqlCommand cmd = new SqlCommand();
          trans = cmd.Transaction;
          cmd.ExecuteNonQuery();
          trans.Commit();
      }
      catch{
          trans.Commit();
      }
      finally{
          conn.Close();
      }
      ```

      