---
layout: project
title: Icreated Web Store for iDempiere
description: Icreated Web Store is an alternative for Idempiere Web Store. It is a web application based on Angular and Bootstrap frameworks. It allows users to create and manage icommerce business logic.
project: webstore
order: 3
---


# TL;DR

Icreated WebStore for Idempiere is a frontend application created as responsive **angular / bootstrap** seed for your projects.

This applications shows standard web sites features:
* product catalog
* product search
* user authentication
* basket management synchronized with server
* order checkout
* private customer area
* orders information
* addresses management
* password update
* account information update

## Demo
The demo is available here: [https://webstore.icreated.co](https://webstore.icreated.fr)
The backend of this demo is based on Json Server. It is a fake REST API. It allows to test the application without installing a backend server.
If you find some troubles (mmm, actually I cannot save new backend data), you can check the behavior here: [https://github.com/icreated/webstore-json-server/blob/main/server.js](https://github.com/icreated/webstore-json-server/blob/main/server.js)


## Features
![WebStore Architecture](/assets/images/projects/webstore/screenshot_architecture.png?raw=true "Webstore Architecture")

Server side Icreated WebStore API project for Idempiere can found [here]({% link _projects/webstore-api.md %})


## Some snapshots:

### Home page

![WebStore Home](/assets/images/projects/webstore/screenshot_home.png?raw=true "Webstore Home")

### Basket page

![WebStore Basket](/assets/images/projects/webstore/screenshot_basket.png?raw=true "Webstore Basket")

### Checkout page

![WebStore Checkout](/assets/images/projects/webstore/screenshot_checkout.png?raw=true "Webstore Checkout")

### Order page

![WebStore Order](/assets/images/projects/webstore/screenshot_order.png?raw=true "Webstore Order")



## Installing / Getting started

Run `npm install`.

This will create the node_modules directory in your current directory (if one doesn’t exist yet) and will download packages to that directory.


By default, Icreated Webstore API endpoints are installed here:

[http://localhost:8080/services/api/](http://localhost:8080/services/api/)

Change constant `API_ENDPOINT` in `app/core/library.ts` if different



### Deploying / Publishing / Testing

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

