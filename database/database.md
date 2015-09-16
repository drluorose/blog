#数据库知识
##数据库的事务
###数据库事务的特性
###数据库事务的隔离级别
####1.Serializable(串行化)
    一个事务在执行过程中完全看不到其他事务对数据库所做的更新（事务执行的时候不允许别的事务并发执行。事务串行化执行，事务只能一个接着一个地执行，而不能并发执行。）
####2.Repeatable Read（可重复读）
    一个事务在执行过程中可以看到其他事务已经提交的新插入的记录，但是不能看到其他其他事务对已有记录的更新。
####3.Read Commited（读已提交数据）
    一个事务在执行过程中可以看到其他事务已经提交的新插入的记录，而且能看到其他事务已经提交的对已有记录的更新。
####4.Read Uncommitted（读未提交数据）
    一个事务在执行过程中可以看到其他事务没有提交的新插入的记录，而且能看到其他事务没有提交的对已有记录的更新。
###如何设置一个数据库的隔离级别
####Mysql数据库如何设置隔离级别
    mysql数据的默认的数据库隔离级别是REPEATABLE-READ.
    mysql中可以通过下面的命令来查看一个隔离级别:
    select @@global.tx_isolation; 查看全局的一个事物的隔离级别.
    select @@session.tx_isolation;查看当前连接的事物的隔离级别.
    select @@tx_isolation;查看当前的隔离级别.
    通过命令设置一个隔离级别:
    SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}

    关于mysql for update;
    在repeatable read 事物隔离级别下,当select * ... for update .
    在事物A中select for update. 事物B这是可以去select这条记录,但是执行update操作时会等待事物A Commit.事物B阻塞在这里.
    在并发的状态下,不能解决覆盖更新的问题.可以在数据库字段上加上一个version更新的时候比较version,跟新数据库.

    MySQL命令中开启一个事物
    start transaction
    rollback
    commit


####Spring事物的传播属性
事务的几种传播特性
1. PROPAGATION_REQUIRED: 如果存在一个事务，则支持当前事务。如果没有事务则开启
2. PROPAGATION_SUPPORTS: 如果存在一个事务，支持当前事务。如果没有事务，则非事务的执行
3. PROPAGATION_MANDATORY: 如果已经存在一个事务，支持当前事务。如果没有一个活动的事务，则抛出异常。
4. PROPAGATION_REQUIRES_NEW: 总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起。
5. PROPAGATION_NOT_SUPPORTED: 总是非事务地执行，并挂起任何存在的事务。
6. PROPAGATION_NEVER: 总是非事务地执行，如果存在一个活动事务，则抛出异常
7. PROPAGATION_NESTED：如果一个活动的事务存在，则运行在一个嵌套的事务中. 如果没有活动事务, 则按TransactionDefinition.PROPAGATION_REQUIRED 属性执行