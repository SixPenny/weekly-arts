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

ʱ�临�Ӷ� O(N), �ռ临�Ӷ� O(N), ʹ�ý�������ϴ�ơ�

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

����һ������Ȥ�Ĳ�Ʒ�����Խ�pdf ����ʱ��ʹ��sql ����ѯ��Rockset �ﻹ֧�� JSON ��Excel ����ʽ���ı���ʹ��sql ��ѯ���ǳ���Ȥ��idea��

## Tips

API �� Ӧ�ñ�̽ӿ�(Application Programing Interface) , ��Ϊ���÷��ṩ���������һ�ַ�����
SPI �� �����ṩ�ӿ�(Service Provider Interface)�� ��Ϊ��������չ���ܵ�һ�ַ�����

API ��ϵͳ���ⲿ�����Ĵ��ڡ�SPI ��ϵͳΪ���������ĺ��š�

�ܶ�ʱ��һ��Ӧ�ÿ��ܼ���client Ҳ�� provider����ôAPI ��SPI �ķֽ�ͺ�ģ����������Ҫ�� API �� SPI �Ĳ�ͬ�����������֡�

API ������
- �ṩ�����÷�ĳ������
- ���� API �Ե��÷�û��Ӱ��
- �޸Ļ�ɾ�� API Ӱ��ܴ�
- ��������

SPI ������
- �ṩ��������չϵͳ����������
- SPI ��ɾ���Ե��÷�û��Ӱ�죬ֻ��Ӱ�����
- SPI �����ӿ��ܻ�������⣨����һ�����أ�
- ����ʵ��


�� API �� SPI ����һ��ͨ������һ�������⡣����ʱ����������û����ô��������Ҫ���������ʱ��ϸ���ǡ����������һ����rocketmq �ķ�װ��������۵���������һ�����ϸ�Ĳ�Ʒ��

API �Ƚϳ�����SPI �ٶ�˵һ�㡣

��java �У� SPI ʹ�õķ�ʽ�У�
- jdbc
- jndi
- Java Cryptography Extension
- Java API for XML Processing


�⼸�ִ�Ҷ�������ƽ���Լ�Ҳ��д���ʽ�ܹ���SPI ����һ���Ʋ���ȥ�ĸ��Ҫʵ��һ�����ʽ�ܹ�����Ҫ�����棺
- registry
- interface
- interface ���õ�
- implementation

�����¼���Ҫ��һ�£�ֻ�������ʽ�ܹ��е�interface �ǻ�Թ��ܲ���Ӱ��ģ����¼��е�listener ֻ�Ǳ���������Ϣ�����ܶ������̲���Ӱ�졣

## Share


### ժҪ

Spring ʹ�õĶ�������ʽ���񣬲������ʼʱ Spring ֧�ֵ��Ǳ��ʽ���񡣱�ƪ������ Spring ����汾 interface21������ʹ����ָ��spring������汾���룩 ʵ�ֵ����񼴱��ʽ������Ϊ����ʽ����ֻ�������������ԣ����ߵ��ں���һ�µġ�


### ������˵��

jdk: 1.7  
github: https://github.com/SixPenny/spring-framework-0.9.git  
IDE: idea

�ڸ�Ŀ�µ� docs Ŀ¼����һ�� tutorial.pdf �ļ��������� Spring �����ʹ�ã����Ķ�����ĺð��֡�

### Դ�����


#### ������

interface21 ʹ�õ��Ǳ��ʽ����ģ�ͣ�ʹ���ϲ�������ʽ����ģ�ͷ��㣬����ȴ���������ǿ��� Spring �����ʵ������ġ�

Spring ����ĺ�������`PlatformTransactionManager`, ���涨����������ص��������� ��ȡ�������`getTransaction`���ύ���� `commit` �� �ع����� `rollback`
```java
TransactionStatus getTransaction(TransactionDefinition definition)
	    throws TransactionException;

void commit(TransactionStatus status) throws TransactionException;

void rollback(TransactionStatus status) throws TransactionException;
```
`TransactionStatus` ��������������ģ������ `commit` �� `rollback` ������������`TransactionDefinition` �������壬����������Ĵ�����ʽ�����뷽ʽ�ͳ�ʱ���ԡ�

##### `getTransaction` ����

�ӿڵķ������٣�����ʼ������ͬ���ȶ�����ôʵ�ֵ��أ� `getTransaction` ��������������������Щ��������`AbstractPlatformTransactionManager` ��`getTransaction`ģ�����ҵ���������̶��塣

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

����������һ���������
1. `Object transaction = doGetTransaction();` ������ʵ�ֵģ�����һ�����������Ҫ��֤ͬһ�����񷵻ص����������ͬһ������������з�װ�����ӡ��Ự����Ϣ���Ӿ���ʵ�ֶ�����`DataSourceTransactionManager` ������һ����װ��`Connection`�Ķ���`HibernateTransactionManager` ������һ����װ��`Session` �Ķ���
2. 
```
if (isExistingTransaction(transaction)) {
	logger.debug("Participating in existing transaction");
	return new TransactionStatus(transaction, false);
}
``` 
����Ѿ�����һ��������ֱ�ӷ���(��ʱ��δ����`PROPAGATION_REQUIRES_NEW` �����ԣ�����֮��������ر����`handleExistingTransaction`����)
3. 
```
if (definition == null) {
	// use defaults
	definition = new DefaultTransactionDefinition();
}
```
���û�������壬��ʹ��Ĭ�ϵ������� PROPAGATION_REQUIRED �� ISOLATION_DEFAULT
4. 
```
if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_MANDATORY) {
	throw new NoTransactionException("Transaction propagation mandatory but no existing transaction context");
}
```
���������ָ��ʹ�� `PROPAGATION_MANDATORY` ��ʽ�����׳��쳣��������û�������·����ǰ���������ֱ�ӷ����ˣ�
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
���������Ϊ`PROPAGATION_REQUIRED`, ����һ�������� ģ�巽��`doBegin`�����������ο���һ��������  
`DataSourceTransactionManager` �е���������������
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
���Կ�����������Ŀ������Ǳ��浱ǰjdbc connection ��`autoCommit`�ֳ�����`commit` �� `rollback`�лָ��������� `autoCommit` ��Ϊ `false`��

�����5����������Ŀ�ʼ���̣�������ڹ��ܲ�ȫ���������̸���������������ӷ��㡣

##### `commit` �� `rollback` ����
`commit` ������`rollback` �����ṩ�˱��ʽ�ع��Ĺ��ܣ���Ƕ�������ṩ֧�֣����ṩ�˻ص����ܡ�

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

ǰ��˵�Ļָ� `autoCommit` ��������������ʵ�ֵģ��ֳ�����Ҳ���������У�����ҿ���ȥ��`DataSourceTransactionManager` ���`closeConnection`������

##### ʹ�÷�ʽ

ʹ�÷�ʽ�ǳ��򵥣�������һ���ο�����1 �е����ӣ��ܼ򵥣��Ͳ���˵�ˡ�

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
Ҳ����ʹ���ܱ����е�`TransactionTemplate`,���涨���������̣�����ֻ��Ҫ�����Լ���ִ�з����Ϳ����ˡ�

#### �ܱ���

##### `TransactionTemplate`
Spring ��װ��һ�����๩����ʹ�� `TransactionTemplate`, ���ĺ��ķ����� `execute`����Ȼ�� Template, ʵ������һ���ص�ģʽ��Ӧ�á�

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
`TransactionCallback` �����Ƕ������Ҫִ�еķ����ص���`TransactionTemplate` �����Ǵ������ύ��ع�������(`TransactionCallback` ��interface21 ��ֻ��һ��Ĭ��ʵ�֣���û�з��ؽ������������ע�ص�������ṹ)

##### `TransactionSynchronization`
`TransactionSynchronization` �ṩ������ͬ����صķ�����interface21 ��ֻ������ `void afterCompletion(int status);` ���������ڵ�Spring �����˸���ķ������û�����ʹ�����ע�������ύ�����֮��Ļص�����

##### `TransactionInterceptor`

����һ��cglib ����ʵ�ֵ�������������������ʽ����ĳ��Σ�����Ȥ�Ŀ��Կ�һ��

### �ο���ַ

1. [Spring - Programmatic Transaction](https://www.logicbig.com/tutorials/spring-framework/spring-data-access-with-jdbc/programmatic-transaction.html)
2. [Spring MVC type conversion : PropertyEditor or Converter?](https://stackoverflow.com/questions/12544479/spring-mvc-type-conversion-propertyeditor-or-converter)
3. [Spring Property Editor �C CustomEditorConfigurer Example](https://howtodoinjava.com/spring-core/registering-built-in-property-editors-in-spring-4-customeditorconfigurer-example/)
4. [Spring Framework��s WebDataBinder](https://www.intertech.com/blog/spring-frameworks-webdatabinder/)
5. [<Spring>���Ա༭��, PropertyEditor, JavaBean-](http://www.cnblogs.com/Tony-Mu/articles/2565527.html)