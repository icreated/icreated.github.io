---
author: "Sergey Polyarus"
layout: post
title:  "How to hide transaction implementation"
date: 2023-06-24
comments: true
description: How to hide transaction implementation with java lambda
categories: java lambda transaction idempiere
tags: 
- webstore-api
- webportal-api
- idempiere
- java
---


## TL;DR
Every transaction in Idempiere is implemented with Transaction initialization and finalisation. 
Here is example of transaction implementation:

```java
		String trxName = null;
		Trx trx = null;
		try {
			trxName = Trx.createTrxName("AwesomeOrderTransaction");
			trx = Trx.get(trxName, true);

            MOrder order = new MOrder(Env.getCtx(), orderId, trxName);
            // Do something amazing with order
            order.save(trxName);

		} catch (Exception e) {
			if (trx != null) {
				trx.rollback();
				trx.close();
				trx = null;
			}
			throw new AdempiereException(e);
		} finally {
			if (trx != null) {
				trx.commit();
				trx.close();
			}
		}
```

## Desired implementation
It is a good practice to hide transaction implementation from the client code. It will help you to avoid code duplication and make your code more readable.
Here is how we would like to see our code:

```java
        MOrder createdOrder = Transaction.run((trxName) -> {
            MOrder order = new MOrder(ctx, orderId, trxName);
            // Do something amazing with order
            order.save(trxName);
            return order;
        });
```

## Implementation
Here is the implementation of the `Transaction` class:

```java
  public static <T> T run(Function<String, T> transaction) {

    Trx trx = null;
    try {
      String transactionName = Thread.currentThread().getStackTrace()[2].getMethodName();
      String trxName = Trx.createTrxName(transactionName);
      trx = Trx.get(trxName, true);
      T object = transaction.apply(trxName);
      if (trx.commit()) {
        return object;
      }

    } catch (Throwable e) {
      if (trx != null) {
        trx.rollback();
        trx.close();
        trx = null;
      }
    } finally {
      if (trx != null) {
        trx.close();
      }
    }
    return null;
  }
```

Note that we use `Thread.currentThread().getStackTrace()[2].getMethodName()` to get the name of the method that calls `Transaction.run()`. It is a good practice to use the name of the method as a transaction name. It will help you to find the transaction in the log file.

## Conclusion
This lambda approach can be used for JDBC implementation too like here: [How to hide JDBC implementation](java/lambda/jdbc/idempiere/2023/06/24/how-to-hide-jdbc-impl.html)