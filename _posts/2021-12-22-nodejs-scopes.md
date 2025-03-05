---
title: "Node.js - Değişken Kapsamları (Variable Scope)"
date: "2021-12-22"
layout: post
author: endrcn
categories: [NodeJS]
tags: [node.js,nodejs,javascript,scope]
---

Değişken kapsamları, bir [değişkenin](https://endrcn.dev/nodejs/variables/) erişim alanını ifade eder. Literatürde "değişken kapsamları" ifadesi kullanılmayıp "**scope**" ya da "**variable scope**" ifadesi kullanılır. EcmaScript 6'dan önce Javascript'te sadece **Global Scope** ve **Function Scope** terimleri vardı. Ancak, ES6 ile birlikte değişken tanımlamak için eklenen let ve const keyword'leri ile birlikte **Block Scope** kavramı ortaya çıkmıştır. Tüm **scope** yapılarını alt başlıklar halinde inceleyelim.

![_config.yml]({{ site.baseurl }}/assets/images/js-variable-scope.webp)

## Global Scope

Eğer bir değişken, bir fonksiyon içinde tanımlanmamışsa, bu değişken global scope'a sahip demektir. Yani kodun herhangi bir yerinde kullanılabilir. Eğer bir blok içinde tanımlanmıyorsa **var**, **let** ya da **const** ile tanımlanan değişkenlerin erişim açısından aralarında bir fark yoktur. Hepsi global scope'a sahip olurlar.

```javascript
var num = 4;

function myFunction() {
    console.log(num); // output: 4
}

let num = 4;

function myFunction() {
    console.log(num); // output: 4
}

const num = 4;

function myFunction() {
    console.log(num); // output: 4
}
```

## Function Scope

Function Scope, her [fonksiyonun](https://endrcn.dev/nodejs/functions/) kendine ait alanına verilen isimdir. Eğer bir değişken bir fonksiyonun içinde tanımlandıysa, bu değişken fonksiyon dışında kullanılamaz.

```javascript
function myFunction () {
   var person = "CAN";
   console.log(person); // output: CAN
}
myFunction();
console.log(person); // Output: ReferenceError: person is not defined
```

Yukarıdaki durum **let** ve **const** ile tanımlanmış değişkenler için de geçerlidir.

Eğer hiç tanımlanmamış bir değişkene **var**, **let** ya da **const** anahtar kelimeleri kullanılmadan direkt bir atama yapılırsa, bu değişken direkt olarak **Global Scope**'a tanımlanır.

```javascript
function myFunction(){
   person = "Ender"
}
console.log(person); // Output: Ender
```

## Local Scope (Block Scope)

**Block Scope**, süslü parantezlerle arasında belirlenmiş bloklar içinde **let** veya **const** ile tanımlanan değişkenlere, tanımlandıkları blok dışından erişilemeyeceği anlamı taşır. **Function Scope**, aynı zamanda bir **Local Scope**' dir.

```javascript
function myFunction(){
    var person = "Ender";
}
console.log(person); // Output: ReferenceError: person is not defined
```

Ancak diğer bloklarda durum farklılaşıyor. Eğer, bir [if bloğunda](https://endrcn.dev/nodejs/conditions/) ya da [döngüde](https://endrcn.dev/nodejs/loops/) **var** ile bir değişken tanımlanırsa, bu değişkene blok dışından erişilebilir ancak **let** ya da **const** ile tanımlanmış bir değişkene blok dışından erişilemez.

```javascript
if (true) {
    var person = "Ender";
}
console.log(person); // Output: Ender

if (true) {
    let carName = "Audi";
    console.log(carName); // Output: Audi
}
console.log(carName); // Output: ReferenceError: carName is not defined

if (true) {
    const brand = "Apple";
    console.log(brand); // Output: Apple
}
console.log(brand); // Output: ReferenceError: brand is not defined
```

Bir değişkene nerelerden ne şekilde erişilebileceğini bilmek, yazdığınız koda çok daha hakim olmanızı sağlar. Node.js öğrenme yolculuğunuzda bir adım daha ilerlemeyi başardınız. Motivasyonunuzu kaybetmeden öğrenmeye devam.
