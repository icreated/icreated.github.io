---
author: "Sergey Polyarus"
layout: post
title:  "How to validate with Hibernate Validator"
date: 2023-02-14 19:28:52
comments: true
description: Hibernate Validator is used in WebPortal API to validate incoming requests from OpenApi specifications.
categories: mapstruct webportal idempiere
tags: 
- webportal-api
---

## TL;DR
Hibernate Validator is a validation framework that integrates with the Java Bean Validation API. It is used in WebPortal API to validate incoming requests from OpenApi specifications.

## How to use Hibernate Validator
In OpenApi specifications you can define validation rules for each field. \
For example, you can define that the field should be a number win min and max values or that it should be a string with a minimum length of 3 characters. \

{% highlight yaml %}
    Document:
      type: object
      description: Object Document
      required:
        - id
        - documentNo
      properties:
        id:
          type: integer
          format: int32
        documentNo: 
          type: string
          minLength: 3
          maxLength: 10
{% endhighlight %}

When you generate the code from OpenApi specifications, you will get a DTO class with validation annotations.
    
    {% highlight java %}
    @Schema(name = "Document", description = "Object Document")
    @JsonTypeName("Document")
    @Generated(value = "org.openapitools.codegen.languages.SpringCodegen")
    public class DocumentDto {

        @JsonProperty("id")
        private Integer id;

        @JsonProperty("documentNo")
        private String documentNo;

        @NotNull
        setId(Integer id) {
        this.id = id;
        }

        @NotNull
        @Size(min=3, max=10)
        setDocumentNo(String documentNo) {
            this.documentNo = documentNo;
        }
    
      ...
      }
    {% endhighlight %}

These annotations are validated by Hibernate Validator during the request processing.
