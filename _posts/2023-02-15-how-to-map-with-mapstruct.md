---
author: "Sergey Polyarus"
layout: post
title:  "How to map Idempiere models to DTO with MapStruct"
date: 2023-02-14 19:28:52
comments: true
description: MapStruct mapping between different types of objects. For example, it is used to convert between MProduct and ProductDTO objects.
categories: mapstruct webportal idempiere
tags: 
- webportal-api
- mapstruct
- idempiere
---

## TL;DR
**MapStruct** is a code generator simplifying mapping between Idempiere Objects and DTOs.\
 Before using MapStruct you need to install it manually. You can find instructions [here]({% post_url 2023-02-14-how-to-install-mapstruct %}).

## @Mapper annotation
`@Mapper` annotation is used to define a mapper interface. It is used to define mappings between different types of objects.\
`@Mapper` annotation has a parameter: `componentModel = "spring"`. It is used to add `@Component` annotation to generated mapper.\
`@Component` annotation is used to register the mapper interface as a Spring bean. \
`@Mapper` annotation has a parameter: `uses = {PaymentService.class}`. It is used to define a service we need to use in the mapper.\
`@Mapper` annotation has a parameter: `imports = {MBPartner.class}`. It is used to define a class that will be used in the expression.

{% highlight java %}
@Mapper(componentModel = "spring", uses = {PaymentService.class}, 
imports = {MBPartner.class})
public interface InvoiceMapper {
  ...
}
{% endhighlight %}


## Basic mapping
Let's take a look at an exemplary mapping between `MInvoice` and `InvoiceDTO` objects.\
MInvoice object is a model from Idempiere. It is a representation of `C_Invoice` table in the database.\
InvoiceDTO is a data transfer object created by OpenApi. It is used to transfer data between backend and frontend.
    
{% highlight java %}
@Schema(name = "Invoice", description = "Object Invoice")
@JsonTypeName("Invoice")
@Generated(value = "org.openapitools.codegen.languages.SpringCodegen")
public class InvoiceDto {

  @JsonProperty("id")
  private Integer id;

  @JsonProperty("documentNo")
  private String documentNo;

  @JsonProperty("poReference")
  private String poReference;

  @JsonProperty("bpartnerName")
  private String bpartnerName;

  ...
  }
{% endhighlight %}
    
Mappings between these objects are defined in `co.icreated.portal.mapper.InvoiceMapper` class.
        
{% highlight java nolinenumbers %}
    @Mapping(target = "id", source = "c_Invoice_ID")
    @Mapping(target = "bpartnerName",
        expression = "java(MBPartner.get(invoice.getCtx(), 
        invoice.getC_BPartner_ID()).getName())")
    @Mapping(target = "date", source = "dateInvoiced")
    @Mapping(target = "poReference", source = "POReference")
    @Mapping(target = "currency", source = "currencyISO")
    public abstract InvoiceDto toDto(MInvoice invoice);
{% endhighlight %}


MapStruct maps fields with the same name by default. So, you don't need to define mapping for fields with the same name. \
If you want to map fields with different names you need to use **@Mapping** annotation: \
**@Mapping** annotation is used to define mapping between two fields. \
**@Mapping** annotation has two parameters: `target` and `source`:
 - `target` is a field in the target object. In our case it is `InvoiceDTO`. 
 - `source` is a field in the source object. In our case it is `MInvoice`.

For example: `@Mapping(target = "id", source = "c_Invoice_ID")` means that `id` field in `InvoiceDTO` will be mapped from `c_Invoice_ID` field in `MInvoice`.

`expression` is used to define a custom mapping. In our case we use it to get `bpartnerName` from `C_BPartner_ID` field. \ 
Java code is used to define custom mapping. \
Don't forget to add `MBPartner` class to the list of imports in @Mapper annotation.


## Special cases
For exemple we need to do additional actions when mapping `MInvoice` to `InvoiceDTO`.
It can be done with **@AfterMapping** annotation. This annotation means that the method will be called after the mapping is done.
In the parameters of the method we can use the source and target objects. \
Here `InvoiceDto` annotated with `@MappingTarget` is already populated with data from `MInvoice`. We can use it to get additional data.
{% highlight java %}
    @AfterMapping
    protected void setBPartnerName(MInvoice invoice, @MappingTarget InvoiceDto invoiceDto) {
        invoiceDto.setBpartnerName(MBPartner.get(invoice.getCtx(), invoice.getC_BPartner_ID())
        .getName());
        invoiceDto.setPayments(paymentService.findInvoicePayments(invoice.getC_Invoice_ID()));
    }
{% endhighlight %}

Thanks to all these features we can easily map Idempiere models to DTOs. \

**Advice**: Escaping custom mapping is possible if you give the same name to the field in the source and target objects. Think about it when working with OpenApi definitions.
    
