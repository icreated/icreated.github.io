---
author: "Sergey Polyarus"
layout: post
title:  "How to use OpenAPI first approach"
date:   2023-02-13 20:17:52
comments: true
description: Thinking in OpenAPI first approach when developing a new API
categories: webportal openapi
tags: 
- webportal-api
- openapi
---

## TL;DR
**OpenAPI first approach** consists of the following steps:
* Create OpenAPI specification
* Generate server stubs
* Implement business logic

## Create OpenAPI specification
OpenAPI specification is a file that describes your API. It is a contract between your API and your clients. It is a good practice to create OpenAPI specification before you start implementing your API. It will help you to understand what your API should do and how it should look like. It will also help you to understand what your clients expect from your API. You can use [Swagger Editor](https://editor.swagger.io/) to create OpenAPI specification. 


For example, let's create an OpenAPI specification for a simple API that returns credit card types.

{% highlight yaml %}
  /common/reference/creditcardtypes:
    get:
      tags:
        - Common
      summary: Get credit card types
      description: Get credit card types
      operationId: getCreditCardTypes
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/ValueLabel'
{% endhighlight yaml %}

This specification describes an endpoint that returns credit card types. It returns an array of `ValueLabel` objects. `ValueLabel` object has two properties: `value` and `label`.



## Generate server stubs
Server stubs are generated thanks to `pom.xml` file. You can find `pom.xml` file in the root of the project. It contains the following plugin:

{% highlight xml %}
  <plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <version>6.2.1</version>
    <executions>
		  <execution> 
        <goals>
            <goal>generate</goal>
        </goals>
        <configuration>
            <inputSpec>${portal-frontend.path}/openapi.yaml</inputSpec>
            <output>${project.basedir}</output>
            <generatorName>spring</generatorName>
            <apiPackage>co.icreated.portal.api.service</apiPackage>
            <modelPackage>co.icreated.portal.api.model</modelPackage>
            <modelNameSuffix>Dto</modelNameSuffix>
            <generateApiTests>false</generateApiTests>
            <generateModelTests>true</generateModelTests>
            <generateModelDocumentation>false</generateModelDocumentation>
            <generateSupportingFiles>false</generateSupportingFiles>
            <globalProperties>
              <apiTests>false</apiTests>
            </globalProperties>
            <typeMappings>
              <typeMapping>Double=java.math.BigDecimal</typeMapping>
          </typeMappings>
            <configOptions>
                <sourceFolder>/src</sourceFolder>
                <interfaceOnly>true</interfaceOnly>
                <openApiNullable>false</openApiNullable>
                <hideGenerationTimestamp>true</hideGenerationTimestamp>
                <skipDefaultInterface>true</skipDefaultInterface>
                <useTags>true</useTags>
                <useBeanValidation>true</useBeanValidation>
                <performBeanValidation>false</performBeanValidation>
            </configOptions>
        </configuration>
		    </execution>
		  </executions>
  </plugin>
{% endhighlight xml %}

This plugin generates server stubs. It generates interfaces for controllers and models DTO (Data Transfer Objects). You can find generated files in the `src/main/java/co/icreated/portal/api` folder.

That's it. You have created OpenAPI specification and generated server stubs. Now you can implement business logic by implementing generated interfaces.


## Implement business logic
You can implement business logic by implementing generated interfaces. For example, let's implement `getCreditCardTypes` method.

{% highlight java %}
  @Override
  public ResponseEntity<List<ValueLabelDto>> getCreditCardTypes() {
    List<ValueLabelDto> creditCardTypes = new ArrayList<>();
    creditCardTypes.add(new ValueLabelDto("VISA", "VISA"));
    creditCardTypes.add(new ValueLabelDto("MASTERCARD", "MASTERCARD"));
    creditCardTypes.add(new ValueLabelDto("AMEX", "AMEX"));
    return ResponseEntity.ok(creditCardTypes);
  }
{% endhighlight java %}

