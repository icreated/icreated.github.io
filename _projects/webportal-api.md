---
layout: project
title: Icreated Web Portal API plugin for iDempiere
description: Icreated Web Portal API is a RESTful API based on Spring framework and integrated in Idempiere OSGI environment. It allows users to create and manage their business logic for Web Portal. 
project: portal-api
order: 2
---


# TL;DR

This REST API OSGI plugin for ERP Idempiere initially has been created to communicate with Icreated Web Portal 
[https://github.com/icreated/portal-frontend](https://github.com/icreated/portal-frontend) 
It can be used with other type of integration, for example as an alternative to Idempiere Web Services

Web Portal is fully based on Spring Framework and integrates its features:

*   Spring configuration with **@Configuration** annotation
*	Idempiere context injected by **application.properties** file
*	Simple architecture based on Dependency Injection
*	Spring Security integration
 

Web Portal provides following features:

*	[API-first product-centric approach](https://swagger.io/blog/api-design/design-first-or-code-first-api-development/)
*	[Automatic generated DTO models with openapi-generator maven plugin]({% post_url 2023-02-14-how-to-use-openapi%})
*	[MapStruct converting between DTO and Idempiere Model]({% post_url 2023-02-15-how-to-map-with-mapstruct %})
*	Database requests with PQuery (Idempiere Query wrapper) 
*	JWT Spring Security authentication
*   [Hibernate Validator for declarative OpenApi DTO validation]({% post_url 2023-02-15-how-to-validate-with-hibernate-validator %})
*	Swagger Integration with [https://github.com/icreated/swagger](https://github.com/icreated/swagger) 
*	Invoices and Payments lists
*	OpenItems, i.e. payment due under contract
*	Payment Imitation by Credit Card



## Installing / Getting started

To build this plugin you need to get sources in your project directory with same parent as Idempiere source folder

```shell
git clone https://github.com/icreated/portal-api.git
```
Import this project to Eclipse.
Be sure to satisfy all required dependencies. All needed jars are added directly to lib folder with **mvn verify** command.
Eclipse needs a mapstruct plugin to compile converters. It can be found here [https://mapstruct.org/documentation/ide-support/](https://mapstruct.org/documentation/ide-support/) 

Follow instructions for installation in blog post: [How to install MapStruct for use with eclipse]({% post_url 2023-02-14-how-to-install-mapstruct %})



**openapi.yaml** is configured in [Icreated Web Portal)]({% link _projects/webportal.md %}). It's easier to work with one file and share it between backend & frontend. 

The copy of **openapi.yaml** is automatically copied with a maven plugin to project because of use of swagger plugin when Idempiere sever is started.



### Deploying / Publishing / Testing
Check if it works by accessing to Swagger Home Page:
[http://localhost:8080/portal/api](http://localhost:8080/portal/api) 
To connect to API provide JWT Token you can get like this:

```shell
curl --location --request POST 'http://localhost:8080/portal/api/login' \
--header 'Content-Type: application/json' \
--data-raw '{
   "username":"gardenusr",
   "password":"GardenUser"
}'
```


If everything is ok you open the following page:

!["Swagger UI"](/assets/images/projects/webportal-api/Swagger_UI.jpeg "Swagger UI") 