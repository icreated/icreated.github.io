---
author: "Sergey Polyarus"
layout: post
title:  "How to hide jdbc implementation"
date: 2023-06-24
comments: true
description: How to hide jdbc implementation with java lambda
categories: java lambda jdbc idempiere
tags: 
- webstore-api
- webportal-api
- idempiere
- java
---


## TL;DR
Standard JDBC implementation looks like this:
```java
    Connection conn = null;
    PreparedStatement pstmt = null;
    ResultSet rs = null;
    try {
      conn = DB.getConnectionRO();
      pstmt = conn.prepareStatement(sql);
      // Set parameters
      rs = pstmt.executeQuery();
      if (rs.next()) {
        // Find something amazing in the result set
      }
    } catch (Exception e) {
      throw new AdempiereException(e);
    } finally {
      DB.close(rs, pstmt, conn);
    }
```
 
It is a good practice to hide JDBC implementation from the client code. It will help us to avoid code duplication, escape from `try/catch/finally` blocks and make our code more readable.


## Desired implementation
Here is how we would like to see our code:
```java
    String sql = "SELECT * FROM C_Order WHERE C_Order_ID = ?";
    MOrder order = QueryTool.nativeFirst(sql, Map.of(1, W_Store_ID), rs -> {
      return new MOrder(ctx, rs, trxName);
    });
```

## Implementation
Here is the implementation of `QueryTool.nativeFirst` method:
```java
	  public static <T> T nativeFirst(String sql, Map<Integer, Object> params,
	      ThrowingFunction<ResultSet, T, Exception> function) {

	    PreparedStatement pstmt = null;
	    ResultSet rs = null;
	    T retValue = null;
	    try {
	      pstmt = DB.prepareStatement(sql, null);
	      applyParams(pstmt, params);
	      rs = pstmt.executeQuery();
	      if (rs.next()) {
	        retValue = function.apply(rs);
	      } else
	        log.fine("No record");
	    } catch (Exception e) {
	      log.log(Level.SEVERE, sql, e);
	    } finally {
	      DB.close(rs, pstmt);
	      rs = null;
	      pstmt = null;
	    }
	    return retValue;
	  }
```
We need to guess parameter types and apply them to the prepared statement:
```java
	  private static void applyParams(PreparedStatement pstmt, Map<Integer, Object> params) {
	    params.forEach((index, param) -> {
	      try {
	        if (param instanceof String) {
	          pstmt.setString(index, (String) param);
	        } else if (param instanceof Integer) {
	          pstmt.setInt(index, (Integer) param);
	        } else if (param instanceof Timestamp) {
	          pstmt.setTimestamp(index, (Timestamp) param);
	        } else if (param instanceof BigDecimal) {
	          pstmt.setBigDecimal(index, (BigDecimal) param);
	        } else {
	          log.log(Level.SEVERE, "Instance param not implemented");
	        }
	      } catch (SQLException e) {
	        log.log(Level.SEVERE, "Error applying param: ", e);
	      }
	    });
	  }
```
We also need to define `ThrowingFunction` interface that will help us to avoid `try/catch` blocks in our lambda:
```java
    @FunctionalInterface
    public interface ThrowingFunction<T, R, E extends Exception> {
      R apply(T t) throws E;
    }
```

## Conclusion
Java lambda helped us to hide JDBC implementation and make our code more readable. This lambda approach can be used for transactions too like here: [How to hide transaction implementation](/java/lambda/transaction/idempiere/2023/06/24/how-to-hide-transaction-impl.html).