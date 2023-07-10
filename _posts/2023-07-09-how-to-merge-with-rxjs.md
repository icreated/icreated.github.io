---
author: "Sergey Polyarus"
layout: post
title:  "How to merge REST API responses with RxJS"
date: 2023-07-09
comments: true
description: Merging 2 REST API responses with RxJS switchmap
categories: angular rxjs switchmap
tags: 
- webstore
- webportal
- angular
---

## TL;DR
We are working on an angular address component which fetches the list of countries and the user address. 
We have 2 requirements:
1. To get the list of countries with the selected country when the user address is selected.
2. To select the default given country when the address is empty and the component is used to create a new address.

## The problem
2 REST API provide the list of countries and the user address. The user address contains the country id. The country id is used to get the country name from the list of countries.
Theses 2 requests are asynchronous. We can't know which one will be executed first. So, we need to merge these 2 requests to get the list of countries with the selected country.

## The solution

The first ungly solution is to use nested subscribe. It works but it is not very elegant. Don't do it!

```typescript

this.countryService.getCountries().subscribe(countries => {
    this.countries = countries;
    this.addressService.getAddress().subscribe(address => {
        this.address = address;
        this.address.country = this.countries.find(country => country.id === this.address.country.id);
    });
});

```

The second solution is to use RxJS switchmap. It is more elegant and more efficient. 


```typescript

this.countryService.getCountries().pipe(
    switchMap(countries => {
        this.countries = countries;
        return this.addressService.getAddress();
    })
).subscribe(address => {
    this.address = address;
    this.address.country = this.countries.find(country => country.id === this.address.country.id);
});

```

But how to implement the second requirement? We need to merge the 2 requests only when the address is defined. We can do it with the following code:

```typescript

    this.countryService.getCountries()
        .pipe(
            switchMap(countries => {
                this.countries = countries;
                if (id > 0) {
                    return this.addressService.getAddress(id);
                } else {
                    this.address.country = this.countries.find( country =>
                        country.id === defaultCountryId);
                    return EMPTY;
                }
            }
            )).subscribe(address => {
            this.address = address;
            this.address.country = this.countries.find( country =>
                country.id === this.address.country.id);
        });

```



Here the subscription is executed only when the address `id > 0` . Otherwise, the subscription is not executed and the default country is selected.

By the way, how about html template? We can use the following Bootstrap code:

```html
    <select [(ngModel)]="address.country"  name="selectedCountry" class="form-control">
        <option [ngValue]="country" *ngFor="let country of countries">{{country.name}}</option>
    </select>
```



## Conclusion
The RxJS switchmap operator is very useful to merge 2 REST API responses with different scenarios in it.