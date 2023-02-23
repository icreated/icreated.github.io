---
author: "Sergey Polyarus"
layout: post
title:  "How to use Default Error Response with OpenAPI"
date: 2023-02-23
comments: true
description: OpenApi default error response wrapper for all exceptions from Spring
categories: spring openapi
tags: 
- webportal-api
- openapi
- spring
---

## TL;DR
OpenApi asks to describe different Http Status responses for each endpoint. Sometimes there are too many to add to each response.
For exemple:
```yaml
responses:
  '200':
    description: OK
  '400':
    description: Bad Request
  '401':
    description: Unauthorized
  '403':
    description: Forbidden
  '404':
    description: Not Found
  '405':
    description: Method Not Allowed
  '500':
    description: Internal Server Error
```
To put all these status each time broke the readability of the OpenApi file. 
>IMHO in this case `Don't repeat yourself DRY` principle is not respected.

## Solution
To solve this issue, we can use the `default` response. It can be added to each endpoint. So it will be used when no other response is found.
Each endpoint response can be simplified to:
```yaml
      responses:
        '200':
          description: OK
        default:
          description: Unexpected error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PortalError'

---
components:
  schemas:
    PortalError:
      type: object
      required:
        - code
      properties:
        code:
          type: string
        message:
          type: string
        details:
          type: array
          items:
            type: string
```

## How to handle it in Spring

Let's create `@ControllerAdvice` to handle all exceptions and return a `PortalErrorDto` object already created with Maven plugin.

```java
@ControllerAdvice
public class PortalExceptionHandler extends ResponseEntityExceptionHandler {

  @ExceptionHandler(value = {PortalInvalidInputException.class})
  public ResponseEntity<Object> handleInvalidInputException(PortalInvalidInputException exception) {

    log.warn(exception.getMessage());
    return getCommonResponseEntity(exception, HttpStatus.BAD_REQUEST);
  }
// Other exceptions follow the same pattern
...

  private ResponseEntity<Object> getCommonResponseEntity(Exception exception,
      HttpStatus httpStatus) {

    PortalErrorDto errorDetails = new PortalErrorDto() //
        .code(httpStatus.name()) //
        .message(exception.getLocalizedMessage());
    return new ResponseEntity<Object>(errorDetails, httpStatus);
  }

}
```

## Conclusion
This solution simplify a lot the work on OpenApi specification. It is also a good practice to handle all exceptions in one place.