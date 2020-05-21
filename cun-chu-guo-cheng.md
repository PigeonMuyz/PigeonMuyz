# 存储过程

### 存储过程的概念

存储过程相当于高级程序语言中的 \[方法\] ，可以将多条指令组合起来调用 特点：

1. 执行速度更快
2. 允许模块化程序设计
3. 提高网络安全性
4. 减少网络流通量

### 存储过程的分类

1. 系统存储过程：名称以 `sp_` 开头,是系统定义的存储过程，存放在每个数据库中的 `可编程性` =&gt; `存储过程` =&gt; `系统存储过程`
2. 拓展存储过程：名称以 `xp_` 开头,是由其他编程语言封装成DLL文件的存储过程
3. 自定义存储过程：一般以`proc_`开头，开发者定义的存储过程

### 调用存储过程的方式

```text
execute 存储过程名称 [参数值列表]

exec 存储过程名称 [参数值列表]
```

**Tips**：

1. 当存储过程的调用和存储过程本身没有用 `GO` 关键字分隔，则可能出现“自己调用自己方法”的递归现象
   * SQL Server递归的最大次数位32次，如果超过，则系统也会报错
2. 参数列表给了默认值时，调用的这个默认参数时，可以用default或者不写来代替

### 在DBHelper中写法

```text
///<summary>
/// 调用存储过程
///</summary>
public static void getProcedure(string procName, SqlParameter[] parameters)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
         SqlCommand cmd = new SqlCommand(procName, conn);
        /* 第一种
          cmd.Connection = conn;
          cmd.CommandText = procName;
        */
        cmd.CommandType = CommandType.StoredProcedure;
        if(parameters != null)
        {
            cmd.Parameters.AddRange(parameters);
        }
    }
}
```

### 变量的声明

1. 系统变量：系统自带的变量（全局变量），变量名必须以  `@@`  开头
2. 自定义变量：程序员定义的变量，这个变量仅在执行时有效，变量名必须以  `@`  开头

```text
--自定义变量声明方式：
declare 变量名 数据类型
--使用变量的方式：
set 变量名 = 值
--查看变量内容
select 变量名
print 变量名
```

| **系统存储过程** | **说明** |
| :--- | :--- |
| **sp\_databases** | 列出服务器上的所有数据库 |
| **sp\_helpdb** | 报告有关指定数据库或所有数据库的信息 |
| **sp\_renamedb** | 更改数据库的名称 |
| **sp\_tables** | 返回当前环境下可查询的对象的列表 |
| **sp\_columns** | 返回某个表列的信息 |
| **sp\_help** | 查看某个表的所有信息 |
| **sp\_helpconstraint** | 查看某个表的约束 |
| **sp\_helpindex** | 查看某个表的索引 |
| sp\_stored\_procedures | 列出当前环境中的所有存储过程 |
| **sp\_password** | 添加或修改登录账户的密码 |
| **sp\_helptext** | 显示默认值、未加密的存储过程、用户定义的存储过程、触发器或视图的实际文本 |

