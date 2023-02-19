---
layout: project
title: Icreated Web Store API plugin for iDempiere
description: Icreated Web Store API is a RESTful API based on JAX-RS Jersey framework and integrated in Idempiere OSGI environment. It allows users to create and manage their business logic for Web Store.
project: webstore-api
order: 4
---

# TL;DR
This server side plugin is created to show how API REST can be used in OSGI Idempiere Environment.
It's based on Jersey API Framework, Java-JWT for security purposes & Swagger integration:
>**Attention** This plugin needs some more development that will be done in the future.
- OpenAPI first approach
- Generated models and services
- MapStruct integration
- QA review



## Features
![WebStore Schema](/assets/images/projects/webstore/screenshot_schema.png?raw=true "Webstore Schema")

This API provides following services:

![Swagger services](/assets/images/projects/webstore/screenshot_swagger.png?raw=true "Swagger services")

It's quite enough to build a real frontend commercial site with.
As an example you can check an [WebStore Angular project]({% link _projects/webstore.md %}) built on it: 


## Installing / Getting started

To build this plugin you need to get sources in your project directory:

```shell
git clone https://github.com/icreated/webstore-api.git
```

Build it with Maven:

```shell
mvn verify
```


### Deploying / Publishing / Testing

Check if plugin is working by getting OpenAPI description file:

[http://localhost:8080/services/api-docs/openapi.xml](http://localhost:8080/services/api-docs/openapi.xml)

To check and test REST services install Swagger plugin from here:

[https://github.com/icreated/swagger](https://github.com/icreated/swagger)

> Make sure that Swagger plugin points to http://localhost:8080/services/api-docs/openapi.xml 


