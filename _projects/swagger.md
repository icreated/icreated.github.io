---
layout: project
title: Swagger plugin for iDempiere
description: Swagger is a OpenAPI tool for Idempiere. It visualizes the RESTful API and allows users to test the API.
project: swagger
order: 5
---

# TL;DR
This is simple OpenApi implementation for Idempiere OSGI platform.

This OpenApi application was created to work together with Idempiere WebStore / Portal API

[WebStore API]({% link _projects/webstore-api.md %})

[Web Portal API]({% link _projects/webportal-api.md %})

It should point to following **openapi.xml** for WebStore:

[http://localhost:8080/services/api-docs/openapi.xml](http://localhost:8080/services/api-docs/openapi.xml)

or 

[http://localhost:8080/portal/openapi.yaml](http://localhost:8080/portal/openapi.yaml)

for Portal API


## Installing / Getting started

To build this plugin you need to get sources in your project directory:

```shell
git clone https://github.com/icreated/webstore-api.git
```

If used with others API plugins, modify path in pom.xml:

```xml
<openapi-value>http://localhost:8080/services/api-docs/openapi.xml</openapi-value>
```

Build it with Maven:

```shell
mvn verify
```

Deploy plugin to Idempiere OSGI and navigate to OpenAPI URL:

[http://localhost:8080/openapi/app/](http://localhost:8080/openapi/app/)

