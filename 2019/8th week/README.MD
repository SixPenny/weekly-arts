## Algorithm

### Description

Shuffle a set of numbers without duplicates.

Example:
```
// Init an array with set 1, 2, and 3.
int[] nums = {1,2,3};
Solution solution = new Solution(nums);

// Shuffle the array [1,2,3] and return its result. Any permutation of [1,2,3] must equally likely to be returned.
solution.shuffle();

// Resets the array back to its original configuration [1,2,3].
solution.reset();

// Returns the random shuffling of array [1,2,3].
solution.shuffle();

```


### Solution

时间复杂度 O(N), 空间复杂度 O(N), 使用交换法来洗牌。

```java
public class ShuffleAnArray {
    private int[] nums;
    private Random random = new Random();
    public ShuffleAnArray(int[] nums) {
        this.nums = nums;
    }

    /** Resets the array to its original configuration and return it. */
    public int[] reset() {
        return nums;
    }

    /** Returns a random shuffling of the array. */
    public int[] shuffle() {
        int[] shuffled = Arrays.copyOf(nums, nums.length);
        for (int i = 0; i < shuffled.length; i++) {
            int exchange = random.nextInt(shuffled.length);
            int temp = shuffled[i];
            shuffled[i] = shuffled[exchange];
            shuffled[exchange] = temp;
        }
        return shuffled;
    }
}

```

## Review 

[How to Run SQL on PDF Files](https://rockset.com/blog/how-to-run-sql-on-pdf-files/)

这是一个很有趣的产品，可以将pdf 解析时候使用sql 来查询。Rockset 里还支持 JSON 、Excel 等形式的文本，使用sql 查询，非常有趣的idea。

## Tips

API 是 应用编程接口(Application Programing Interface) , 是为调用方提供服务或函数的一种方法。
SPI 是 服务提供接口(Service Provider Interface)， 是为第三方扩展功能的一种方法。

API 是系统与外部交流的窗口。SPI 是系统为第三方留的后门。

很多时候一个应用可能既是client 也是 provider，那么API 与SPI 的分界就很模糊，我们需要从 API 与 SPI 的不同属性上来区分。

API 的属性
- 提供给调用方某种能力
- 添加 API 对调用方没有影响
- 修改或删除 API 影响很大
- 用来调用

SPI 的属性
- 提供给服务方扩展系统能力的能力
- SPI 的删除对调用方没有影响，只会影响服务方
- SPI 的添加可能会产生问题（多了一步拦截）
- 用来实现


将 API 与 SPI 混在一起通常不是一个好主意。但有时这两者区分没有那么清晰，需要我们在设计时仔细考虑。（最近做的一个对rocketmq 的封装按照这个观点来看就是一个不合格的产品）

API 比较常见，SPI 再多说一点。

在java 中， SPI 使用的方式有：
- jdbc
- jndi
- Java Cryptography Extension
- Java API for XML Processing


这几种大家都见过，平常自己也会写插件式架构，SPI 就是一个绕不过去的概念。要实现一个插件式架构，需要几方面：
- registry
- interface
- interface 调用点
- implementation

这与事件的要素一致，只不过插件式架构中的interface 是会对功能产生影响的，而事件中的listener 只是被动接受信息，不能对主流程产生影响。

## Share


### 摘要

Spring 使用的都是声明式事务，不过在最开始时 Spring 支持的是编程式事务。本篇讲的是 Spring 最初版本 interface21（以下使用它指代spring的最初版本代码） 实现的事务即编程式事务。因为声明式事务只是提升了易用性，两者的内核是一致的。


### 环境与说明

jdk: 1.7  
github: https://github.com/SixPenny/spring-framework-0.9.git  
IDE: idea

在根目下的 docs 目录中有一个 tutorial.pdf 文件，描述了 Spring 该如何使用，是阅读代码的好帮手。

### 源码解析


#### 核心类

interface21 使用的是编程式事务模型，使用上不如声明式事务模型方便，不过却有利于我们看清 Spring 是如何实现事务的。

Spring 事务的核心类是`PlatformTransactionManager`, 里面定义了事务相关的三个方法 获取事务对象`getTransaction`，提交事务 `commit` 和 回滚事务 `rollback`
```java
TransactionStatus getTransaction(TransactionDefinition definition)
	    throws TransactionException;

void commit(TransactionStatus status) throws TransactionException;

void rollback(TransactionStatus status) throws TransactionException;
```
`TransactionStatus` 类是事务的上下文，后面的 `commit` 与 `rollback` 都依赖于它，`TransactionDefinition` 是事务定义，包含了事务的传播方式、隔离方式和超时属性。

##### `getTransaction` 方法

接口的方法很少，事务开始、事务同步等都是怎么实现的呢？ `getTransaction` 这个方法的语义包含了这些，可以在`AbstractPlatformTransactionManager` 的`getTransaction`模板中找到具体的流程定义。

```java 
/**
 * This implementation of getTransaction handles propagation behavior and
 * checks non-transactional execution (on CannotCreateTransactionException).
 * Delegates to doGetTransaction, isExistingTransaction, doBegin.
 */
public final TransactionStatus getTransaction(TransactionDefinition definition)
    throws TransactionException {
	try {
		Object transaction = doGetTransaction();
		logger.debug("Using transaction object [" + transaction + "]");
		if (isExistingTransaction(transaction)) {
			logger.debug("Participating in existing transaction");
			return new TransactionStatus(transaction, false);
		}
		if (definition == null) {
			// use defaults
			definition = new DefaultTransactionDefinition();
		}
		if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_MANDATORY) {
			throw new NoTransactionException("Transaction propagation mandatory but no existing transaction context");
		}
		if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRED) {
			// create new transaction
			doBegin(transaction, definition.getIsolationLevel(), definition.getTimeout());
			if (this.transactionSynchronization) {
				TransactionSynchronizationManager.init();
			}
			return new TransactionStatus(transaction, true);
		}
	}
	catch (CannotCreateTransactionException ex) {
		// throw exception if transactional execution required
		if (!this.allowNonTransactionalExecution) {
			logger.error(ex.getMessage());
			throw ex;
		}
		// else non-transactional execution
		logger.warn("Transaction support is not available: falling back to non-transactional execution", ex);
	}
	catch (TransactionException ex) {
		logger.error(ex.getMessage());
		throw ex;
	}
	// empty (-> "no") transaction
	return new TransactionStatus(null, false);
}

```

我们来分析一下这个方法
1. `Object transaction = doGetTransaction();` 由子类实现的，返回一个事务对象，需要保证同一个事务返回的事务对象是同一个，事务对象中封装了连接、会话等信息，视具体实现而定。`DataSourceTransactionManager` 返回了一个封装了`Connection`的对象，`HibernateTransactionManager` 返回了一个封装了`Session` 的对象。
2. 
```
if (isExistingTransaction(transaction)) {
	logger.debug("Participating in existing transaction");
	return new TransactionStatus(transaction, false);
}
``` 
如果已经存在一个事务，则直接返回(此时还未加入`PROPAGATION_REQUIRES_NEW` 等属性，加入之后这个返回变成了`handleExistingTransaction`方法)
3. 
```
if (definition == null) {
	// use defaults
	definition = new DefaultTransactionDefinition();
}
```
如果没有事务定义，就使用默认的事务定义 PROPAGATION_REQUIRED 与 ISOLATION_DEFAULT
4. 
```
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_MANDATORY) {
	throw new NoTransactionException("Transaction propagation mandatory but no existing transaction context");
}
```
如何事务定义指定使用 `PROPAGATION_MANDATORY` 方式，则抛出异常（到这是没有事务的路径，前面存在事务直接返回了）
5. 
```
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRED) {
	// create new transaction
	doBegin(transaction, definition.getIsolationLevel(), definition.getTimeout());
	if (this.transactionSynchronization) {
		TransactionSynchronizationManager.init();
	}
	return new TransactionStatus(transaction, true);
}
```
如果事务定义为`PROPAGATION_REQUIRED`, 则开启一个新事务， 模板方法`doBegin`由子类决定如何开启一个新事务。  
`DataSourceTransactionManager` 中的事务开启定义如下
```
protected void doBegin(Object transaction, int isolationLevel, int timeout) {
	if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {
		throw new InvalidTimeoutException("DataSourceTransactionManager does not support timeouts");
	}
	DataSourceTransactionObject txObject = (DataSourceTransactionObject) transaction;
	Connection con = txObject.getConnectionHolder().getConnection();
	logger.debug("Switching JDBC connection [" + con + "] to manual commit");
	try {
		if (isolationLevel != TransactionDefinition.ISOLATION_DEFAULT) {
			logger.debug("Changing isolation level to " + isolationLevel);
			txObject.setPreviousIsolationLevel(new Integer(con.getTransactionIsolation()));
			con.setTransactionIsolation(isolationLevel);
		}
		con.setAutoCommit(false);
	}
	catch (SQLException ex) {
		throw new CannotCreateTransactionException("Cannot configure connection", ex);
	}
	DataSourceUtils.getThreadObjectManager().bindThreadObject(this.dataSource, txObject.getConnectionHolder());
}
```
可以看到这里事务的开启就是保存当前jdbc connection 的`autoCommit`现场（在`commit` 或 `rollback`中恢复），并将 `autoCommit` 设为 `false`。

上面的5步就是事务的开始过程，相比现在功能不全，但是流程更加清晰，理解更加方便。

##### `commit` 与 `rollback` 方法
`commit` 方法与`rollback` 方法提供了编程式回滚的功能，对嵌套事务提供支持，还提供了回调功能。

```java
public final void commit(TransactionStatus status) throws TransactionException {
	if (status.isRollbackOnly()) {
		logger.debug("Transactional code has requested rollback");
		rollback(status);
	}
	else if (status.isNewTransaction()) {
		try {
			doCommit(status);
			triggerAfterCompletion(TransactionSynchronization.STATUS_COMMITTED);
		}
		catch (UnexpectedRollbackException ex) {
			triggerAfterCompletion(TransactionSynchronization.STATUS_ROLLED_BACK);
			logger.error(ex.getMessage());
			throw ex;

		}
		catch (TransactionException ex) {
			triggerAfterCompletion(TransactionSynchronization.STATUS_UNKNOWN);
			logger.error(ex.getMessage());
			throw ex;
		}
		finally {
			TransactionSynchronizationManager.clear();
		}
	}
}
public final void rollback(TransactionStatus status) throws TransactionException {
	if (status.isNewTransaction()) {
		try {
			doRollback(status);
			triggerAfterCompletion(TransactionSynchronization.STATUS_ROLLED_BACK);
		}
		catch (TransactionException ex) {
			triggerAfterCompletion(TransactionSynchronization.STATUS_UNKNOWN);
			logger.error(ex.getMessage());
			throw ex;
		}
		finally {
			TransactionSynchronizationManager.clear();
		}
	}
	else if (status.getTransaction() != null) {
		try {
			doSetRollbackOnly(status);
		}
		catch (TransactionException ex) {
			logger.error(ex.getMessage());
			throw ex;
		}
	}
	else {
		// no transaction support available
		logger.info("Should roll back transaction but cannot - no transaction support available");
	}
}

```

前面说的恢复 `autoCommit` 属性是在子类中实现的（现场保存也是在子类中），大家可以去看`DataSourceTransactionManager` 类的`closeConnection`方法。

##### 使用方式

使用方式非常简单，下面是一个参考资料1 中的例子，很简单，就不多说了。

```
public void persistOrderItems() {
      TransactionStatus ts =
              transactionManager.getTransaction(new DefaultTransactionDefinition());
      try {
          long id = dao.save(new OrderItem("BWell Ethernet Cable", 5));
          id = dao.save(new OrderItem("EDrive SSD", 2000));
          transactionManager.commit(ts);
      } catch (Exception e) {
              transactionManager.rollback(ts);
      }
  }
```
也可以使用周边类中的`TransactionTemplate`,里面定义了主流程，我们只需要定义自己的执行方法就可以了。

#### 周边类

##### `TransactionTemplate`
Spring 封装了一个简单类供我们使用 `TransactionTemplate`, 它的核心方法是 `execute`，虽然叫 Template, 实际上是一个回调模式的应用。

```java
public Object execute(TransactionCallback action) throws TransactionException, RuntimeException {
	TransactionStatus status = this.transactionManager.getTransaction(this);
	try {
		Object result = action.doInTransaction(status);
		this.transactionManager.commit(status);
		return result;
	}
	catch (TransactionException tse) {
		throw tse;
	}
	catch (RuntimeException ex) {
		// transactional code threw exception
		this.transactionManager.rollback(status);
		throw ex;
	}
}
```
`TransactionCallback` 是我们定义的需要执行的方法回调，`TransactionTemplate` 帮我们处理了提交与回滚操作。(`TransactionCallback` 在interface21 中只有一个默认实现，还没有返回结果，不过我们注重的是这个结构)

##### `TransactionSynchronization`
`TransactionSynchronization` 提供了事务同步相关的方法，interface21 中只包含了 `void afterCompletion(int status);` 方法，现在的Spring 包含了更多的方法，用户可以使用这个注册事务提交，完成之后的回调任务。

##### `TransactionInterceptor`

这是一个cglib 代理实现的事务拦截器，是声明式事务的雏形，有兴趣的可以看一下

### 参考网址

1. [Spring - Programmatic Transaction](https://www.logicbig.com/tutorials/spring-framework/spring-data-access-with-jdbc/programmatic-transaction.html)
2. [Spring MVC type conversion : PropertyEditor or Converter?](https://stackoverflow.com/questions/12544479/spring-mvc-type-conversion-propertyeditor-or-converter)
3. [Spring Property Editor – CustomEditorConfigurer Example](https://howtodoinjava.com/spring-core/registering-built-in-property-editors-in-spring-4-customeditorconfigurer-example/)
4. [Spring Framework’s WebDataBinder](https://www.intertech.com/blog/spring-frameworks-webdatabinder/)
5. [<Spring>属性编辑器, PropertyEditor, JavaBean-](http://www.cnblogs.com/Tony-Mu/articles/2565527.html)
