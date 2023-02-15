---
author: "Sergey Polyarus"
layout: post
title:  "How to install MapStruct for use with eclipse"
date: 2023-02-13 19:17:52
comments: true
description: Eclipse plugin for mapstruct
categories: mapstruct eclipse
tags: 
- webportal-api
- mapstruct
---


## TL;DR
**MapStruct** is a code generator that greatly simplifies the implementation of mappings between Java bean types based on a convention over configuration approach. You can use it to generate the boilerplate code that is required to convert between different types of objects.

## Install MapStruct
WebPortal API uses MapStruct to convert between different types of objects. For example, it is used to convert between `MProduct` and `ProductDTO` objects.

The eclipse plugin for MapStruct is not installed by default. You need to install it manually.
Visit [MapStruct Eclipse Plugin](https://mapstruct.org/documentation/ide-support/) page and download the latest version of the plugin.
Here is parameters that you need to use with Eclipse:

![alt text](/assets/images/mapstruct/eclipse-mapstruct.png "Eclipse MapStruct parameters")

![alt text](/assets/images/mapstruct/eclipse-mapstruct-dependencies.png "Eclipse MapStruct dependencies")

With this configuration your mappings will be generated automatically when you build your project in the following folder: `target/generated-sources/annotations`

