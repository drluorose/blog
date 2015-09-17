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

```
start transaction
rollback
commit
```

####Spring事物的传播属性
首先应该可以确认一点，在Spring中可以保证在一个线程中数据库连接是线程独享的。在这点基础之上再来理解Spring的事物的传播属性。Spring的事物的传播性也是在一个线程中的方法调用的问题。

* spring事物传播属性
	1. PROPAGATION_REQUIRED(需要一个事物): 如果存在一个事务，则支持当前事务。如果没有事务则开启
	2. PROPAGATION_SUPPORTS(支持事物): 如果存在一个事务，支持当前事务。如果没有事务，则非事务的执行
	3. PROPAGATION_MANDATORY(强制事物): 如果已经存在一个事务，支持当前事务。如果没有一个活动的事务，则抛出异常。
	4. PROPAGATION_REQUIRES_NEW(需要一个新事物): 总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起。
	5. PROPAGATION_NOT_SUPPORTED(不支持事物): 总是非事务地执行，并挂起任何存在的事务。
	6. PROPAGATION_NEVER(不能有事物): 总是非事务地执行，如果存在一个活动事务，则抛出异常
	7. PROPAGATION_NESTED(嵌套事物):如果一个活动的事务存在，则运行在一个嵌套的事务中. 如果没有活动事务, 则按TransactionDefinition.PROPAGATION_REQUIRED 属性执行.

####Spring事物的传播特性举例说明
#####1.PROPAGATION_REQUIRED如果存在一个事务，则支持当前实务，如果没有事务则开启一个事物

~~~

//事务属性 PROPAGATION_REQUIRED
methodA{
……
methodB();
……
}

//事务属性 PROPAGATION_REQUIRED
methodB{
   ……
}

~~~
当在一个线程中单独调用methodB()时，这个时候会开始一个Transaction。但是methodA调用methodB时，因为methodA已经开启了一个事务，这个时候methodB会加入到methodA的事务中。

相当于

```
Transaction.begin()
try{
	methodB();
	commit();
}catch(Exception e){
	rollback();
}finnal{
	release();
}

Transaction.begin()
try{
	methodA();
	commit();
}catch(Exception e){
	rollback();
}finnal{
	release();
}

```

#####2.PROPAGATION_SUPPORTS支持当前事务，如果没有没事那就非事务执行
```
//事务属性 PROPAGATION_REQUIRED 
methodA(){
  methodB();
}

//事务属性 PROPAGATION_SUPPORTS 
methodB(){
  ……
}
```
单纯的调用methodB时，methodB方法是非事务的执行的。 
当调用methdA时,methodB则加入了methodA的事务中,事务地执行。 
#####3.PROPAGATION_MANDATORY如果已经存在一个事务，支持当前事务。如果没有一个活动的事务，则抛出异常。

```

//事务属性 PROPAGATION_REQUIRED 
methodA(){
  methodB();
}

//事务属性 PROPAGATION_MANDATORY 
methodB(){
  ……
}

```

当单独调用methodB时，因为当前没有一个活动的事务，则会抛出异常 
throw new IllegalTransactionStateException("Transaction propagation 'mandatory' but no existing transaction found"); 

当调用methodA时，methodB则加入到methodA的事务中，事务地执行。 
####4.PROPAGATION_REQUIRES_NEW总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起。

```

//事务属性 PROPAGATION_REQUIRED 
methodA(){
  doSomeThingA();
methodB();
doSomeThingB();
}

//事务属性 PROPAGATION_REQUIRES_NEW 
methodB(){
  ……
}

```

当单独调用methodB时，相当于把methodb声明为REQUIRED。开启一个新的事务，事务地执行。

```

main(){
 TransactionManager tm = null;
try{
  //获得一个JTA事务管理器
   tm = getTransactionManager();
   tm.begin();//开启一个新的事务
   Transaction ts1 = tm.getTransaction();
   doSomeThing();
   tm.suspend();//挂起当前事务
   try{
     tm.begin();//重新开启第二个事务
     Transaction ts2 = tm.getTransaction();
     methodB();
     ts2.commit();//提交第二个事务
     
   }
  Catch(RunTimeException ex){
     ts2.rollback();//回滚第二个事务
  }
  finally{
    //释放资源
  }
   //methodB执行完后，复恢第一个事务
   tm.resume(ts1);
doSomeThingB();
   ts1.commit();//提交第一个事务
}
catch(RunTimeException ex){
  ts1.rollback();//回滚第一个事务
}
finally{
  //释放资源
}
}

```

#####5.PROPAGATION_NOT_SUPPORTED总是非事务地执行，并挂起任何存在的事务。

```

//事务属性 PROPAGATION_REQUIRED 
methodA(){
  doSomeThingA();
methodB();
doSomeThingB();
}

//事务属性 PROPAGATION_NOT_SUPPORTED 
methodB(){
  ……
}

```
当单独调用methodB时，不启用任何事务机制，非事务地执行。 
当调用methodA时，相当于下面的效果 

```

main(){
 TransactionManager tm = null;
try{
  //获得一个JTA事务管理器
   tm = getTransactionManager();
   tm.begin();//开启一个新的事务
   Transaction ts1 = tm.getTransaction();
   doSomeThing();
   tm.suspend();//挂起当前事务
     methodB();
   //methodB执行完后，复恢第一个事务
   tm.resume(ts1);
doSomeThingB();
   ts1.commit();//提交第一个事务
}
catch(RunTimeException ex){
  ts1.rollback();//回滚第一个事务
}
finally{
  //释放资源
}
}

```

#####6. PROPAGATION_NEVER: 总是非事务地执行，如果存在一个活动事务，则抛出异常

```

//事务属性 PROPAGATION_REQUIRED 
methodA(){
  doSomeThingA();
methodB();
doSomeThingB();
}

//事务属性 PROPAGATION_NEVER 
methodB(){
  ……
}

```
单独调用methodB，则非事务的执行。 
调用methodA则会抛出异常 
throw new IllegalTransactionStateException( 
"Transaction propagation 'never' but existing transaction found"); 


#####6.PROPAGATION_NESTED如果一个活动的事务存在,则运行在一个嵌套的事务中. 如果没有活动事务, 则按TransactionDefinition.PROPAGATION_REQUIRED 属性执行 

这是一个嵌套事务,使用JDBC 3.0驱动时,仅仅支持DataSourceTransactionManager作为事务管理器。需要JDBC 驱动的java.sql.Savepoint类。有一些JTA的事务管理器实现可能也提供了同样的功能。 

使用PROPAGATION_NESTED，还需要把PlatformTransactionManager的nestedTransactionAllowed属性设为true; 
而nestedTransactionAllowed属性值默认为false; 

```
//事务属性 PROPAGATION_REQUIRED   
methodA(){  
  doSomeThingA();  
methodB();  
doSomeThingB();  
}  
  
//事务属性 PROPAGATION_NESTED  
methodB(){  
  ……  
}  
```

如果单独调用methodB方法，则按REQUIRED属性执行。 

如果调用methodA方法，相当于下面的效果 

```
main(){  
Connection con = null;  
Savepoint savepoint = null;  
try{  
  con = getConnection();  
  con.setAutoCommit(false);  
  doSomeThingA();  
  savepoint = con2.setSavepoint();  
  try  
      methodB();  
  }catch(RuntimeException ex){  
     con.rollback(savepoint);  
  }  
  finally{  
    //释放资源  
  }  
  
  doSomeThingB();  
  con.commit();  
}  
catch(RuntimeException ex){  
  con.rollback();  
}  
finally{  
  //释放资源  
}  
}  
```
当methodB方法调用之前，调用setSavepoint方法，保存当前的状态到savepoint。如果methodB方法调用失败，则恢复到之前保存的状态。但是需要注意的是，这时的事务并没有进行提交，如果后续的代码(doSomeThingB()方法)调用失败，则回滚包括methodB方法的所有操作。 

嵌套事务一个非常重要的概念就是内层事务依赖于外层事务。外层事务失败时，会回滚内层事务所做的动作。而内层事务操作失败并不会引起外层事务的回滚。 

