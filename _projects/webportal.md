---
layout: project
title: Icreated Web Portal
description: Icreated Web Portal is a web application based on Angular and PrimeNG frameworks that allows users to create and manage their own web front-end business logic.
project: portal-frontend
order: 1
---

# TL;DR

Icreated Web Portal is a frontend application created as responsive **Angular / PrimeNG** seed for your projects.
This application works in pair with the server side plugin for Idempiere [Web Portal API]({% link _projects/webportal-api.md %})

## Features
- OpenApi first approach
- Generated models and services
- Responsive design
- Login / Logout
- Customizable theme
- Customizable fonts (Open Dyslexic included)
- i18n support

## Some snapshots:

### Home page

![Portal Home](/assets/images/projects/webportal/screenshot_home.png?raw=true "Portal Home")

### Invoices page

![Portal Invoices](/assets/images/projects/webportal/screenshot_invoices.png?raw=true "Portal Invoices")

### Invoice Detail page

![Portal Invoice Detail](/assets/images/projects/webportal/screenshot_invoice_detail.png?raw=true "Portal Invoice Detail")


### Settings

![Settings](/assets/images/projects/webportal/screenshot_settings.png?raw=true "Settings")


## Installing / Getting started

Run `npm install`.

This will create the node_modules directory in your current directory (if one doesn’t exist yet) and will download packages to that directory.


By default, Web Portal API REST endpoints are installed here:

[http://localhost:8080/portal/api/](http://localhost:8080/portal/api/)

Change constant `ApiUrl` in `environments/environments.ts` if different



### Deploying / Publishing / Testing

Run `ng ng-openapi-gen` to generate models and services from OpenApi spec.

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.
You are redirected to login page. To get started fill in the form with: gardenusr/GardenUser

Run `ng test` for testing. Services, components, pipes have corresponding specs with Jasmine & Karma.

Run `ng lint` to check if the code is clean.


