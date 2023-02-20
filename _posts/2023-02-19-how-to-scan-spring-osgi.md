---
author: "Sergey Polyarus"
layout: post
title:  "How to scan Spring components in OSGi"
date: 2023-02-19
comments: true
description: Workaround for scanning Spring components in OSGi environment
categories: spring osgi
tags: 
- webportal-api
---

## TL;DR
Spring cannot scan components in OSGi environment. This problem come from the fact that Spring uses classpath scanning to find components. In OSGi environment, classes are loaded from bundles. So, Spring cannot find them.

## Workaround
To solve this problem we cannot use `@ComponentScan` annotation. Instead, we need to use `@Import` annotation to import all the components we need.
    
    {% highlight java %}
    @Configuration
    @Import({
        AwesomeService1.class,
        AwesomeService2.class,
        AwesomeService3.class
        ...
    })
    {% endhighlight %}

It's not a good solution, but it works. However, it is not a good practice to import all the components manually. So, we need to find a better solution.
>We need to:
- find a way to scan components in OSGi environment
- add found components to the Spring context

## Solution

- adding classgraph library to the project which is able to scan classes in OSGi environment. It can be found here: [https://github.com/classgraph/classgraph](https://github.com/classgraph/classgraph).
-  we will not ovveride `doScan` method in `ClassPathBeanDefinitionScanner` class from Spring Framework. Instead, a more simpler solution is inject classes annotated with `@Component` annotation to `@Import` annotation:
{% highlight java %}
    Import importAnnotation = PortalConfig.class.getAnnotation(Import.class);
    PortalUtils.changeAnnotationValue(importAnnotation, "value", getSpringComponents());

      public static Class<?>[] getSpringComponents() {
	  
	  List<Class<?>> list = null;
	  try (ScanResult scanResult = new ClassGraph() //
			  .acceptPackages("co.icreated.portal") //
			  .enableAnnotationInfo()
	          .enableClassInfo().scan()) {
		  ClassInfoList beans = scanResult.getClassesWithAnnotation(Component.class);
		  list = beans
		  .filter(classInfo -> classInfo.getName().startsWith("co.icreated") && !classInfo.getName().endsWith("PortalConfig"))
		  .loadClasses();
	  }
	  return list.toArray(new Class<?>[list.size()]);
  } 
{% endhighlight %}

Value of `@Import` annotation can be injected with helper method `changeAnnotationValue`:
{% highlight java %}
    public static void changeAnnotationValue(Annotation annotation, String attributeName, Object newValue) {
      try {
          Field f = annotation.getClass().getDeclaredField("memberValues");
          f.setAccessible(true);
          Map<String, Object> memberValues = (Map<String, Object>) f.get(annotation);
          memberValues.put(attributeName, newValue);
      } catch (Exception e) {
          throw new RuntimeException(e);
      }
  }
{% endhighlight %}

Hope that this solution will help you to solve the same problem when integrating Spring Framework with OSGi environment!