---
title: "Node.js - Döngüler (Loops)"
date: "2021-12-20"
layout: post
author: endrcn
categories: [NodeJS]
tags: [donguler,javascript,nodejs,node.js,js loops, javascript loops]
---

Son makalemizde uygulama içinde [koşullar](https://endrcn.dev/nodejs/conditions/) tanımlayarak program akışını nasıl yönlendirebileceğinizi anlatmıştım. Döngüler konusu; [değişkenleri](https://endrcn.dev/nodejs/variables/), [koşulları](https://endrcn.dev/nodejs/conditions/), [operatörleri](https://endrcn.dev/nodejs/operators/) sıklıkla kullandığımız bir konudur. Bu nedenle buralarda eksiğiniz olduğunu düşünüyorsanız öncelikle onu tamamlamanızı öneririm.

**Döngü**, [array](https://endrcn.dev/nodejs/data-types/#Arrays) ya da [object](https://endrcn.dev/nodejs/data-types/#Objects) tipindeki verilerin içindeki değerleri tek tek gezip işlem yapmamızı sağlayan yapılardır. Döngüler, tüm programlama dillerinde yaklaşık olarak aynı şekilde tanımlanıp kullanılan yapılardır. Node.js'te kullanılan birden fazla döngü yapısı vardır. Bunlar; **while**, **do-while**, **for**, **for-in** ve **for-of** döngüleridir.

## While Döngüsü

**While döngüsü**, bir koşul ifadesi alan ve bu koşul ifadesi true olana kadar dönmeyi sürdüren bir döngü yapısıdır. If koşulu ile çok benzeyen bir yazım şekline sahipdir.

```javascript
// Syntax
while (condition) {
   // loop area
}
```

While döngüsünün kullanımına örnek olması açısından küçük bir kod geliştirelim. Öncelikle problemimizi belirleyelim: İki tane number tipinde değişkenimiz olsun. Bu değişkenlerden küçük olanın değeri her dönüşte bir artırılsın. Bu iki değişkenin değerleri eşitlendiğinde while döngüsü sonlansın.

```javascript
// While Loop Example
var num1 = 5;
var num2 = 10;
while(num1 != num2) { // Değerler eşit olmadığı sürece dön.
    if (num1 < num2) { // num1, num2 den küçükse bu blok çalışacak
        num1++;
    } else { // num1, num2 den büyükse bu blok çalışacak.
        num1--;
    }
    console.log("num1=",num1);
    console.log("num2=",num2);
    console.log("-----------");
}
console.log("Result:", num1, num2);
/*
Output:
num1= 6
num2= 10
-----------
num1= 7
num2= 10
-----------
num1= 8
num2= 10
-----------
num1= 9
num2= 10
-----------
num1= 10
num2= 10
-----------
Result: 10 10
*/
```

## Do-While Döngüsü

Bazı durumlarda koşuldan önce döngüde çalışan kodun bir kez çalışması gerekebilir. Do-While döngüsü, bu tarz durumlarda kullanılan bir döngüdür. While döngüsüne çok benzer yapıda çalışan yapının tek farkı işlemi önce yapması, kontrolü sonraya bırakmasıdır.

```javascript
// Syntax
do {
    // Loop area
} while (condition);
```

While için yaptığımız örneği bir de Do-While döngüsü ile yapalım:

```javascript
// Do-While Loop
var num1 = 5;
var num2 = 10;
do {
    if (num1 < num2) { // num1, num2 den küçükse bu blok çalışacak
        num1++;
    } else { // num1, num2 den büyükse bu blok çalışacak.
        num1--;
    }
    console.log("num1=", num1);
    console.log("num2=", num2);
    console.log("-----------");
} while (num1 != num2); // Değerler eşit olmadığı sürece dön.
console.log("Result:", num1, num2);
/*
Output:
num1= 6
num2= 10
-----------
num1= 7
num2= 10
-----------
num1= 8
num2= 10
-----------
num1= 9
num2= 10
-----------
num1= 10
num2= 10
-----------
Result: 10 10
*/
```

## For Döngüsü

For döngüsü, programlama dillerinde sıklıkla kullanılan, genelde belirli bir sayıda dönülmesi istendiğinde kullanılan döngü yapısıdır. While ve Do-While döngülerinden farklı olarak sadece koşul ifadesi almaz.

```javascript
// Syntax
for(startValue ; condition ; increment/decrement) {
    // Loop area
}
```

For döngüsüne örnek olarak 1'den 10'a kadar sayıları bir diziye ekleyen bir kod parçası yazalım ve diziyi ekrana bastıralım.

```javascript
let arr = [];
for (let i=1; i<=10;i++) {
    arr.push(i);
}
console.log(arr); // Output: [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
```

Şimdi bu dizideki elemanları tek tek silen bir kod parçası yazalım.

```javascript
for(let i = arr.length - 1; i>=0; i--) {
    arr.splice(i,1);
}
console.log(arr); // Output: []
```

Yukarıdaki kod parçasında dizinin elemanlarını tersten döndüğümüzü farkedeceksiniz. Bunun sebebi, [Veri Tipleri - Arrays](https://endrcn.dev/nodejs/data-types/#Arrays) konusunda not olarak paylaştığımız durumdur. _Bir diziden bir ara eleman silerseniz, diğer elemanlar sola doğru kayar ve index'ler değişir._ Bu kodu aşağıdaki şekilde yazsaydık, dizinin tüm elemanlarının silinemediğini görürdük.

```javascript
for(let i = 0; i < arr.length; i++) {
    arr.splice(i,1);
}
console.log(arr); // Output: [ 2, 4, 6, 8, 10 ]
```

## For...in Döngüsü

For...in döngüsü, genelde object tipindeki bir değişkenin elemanlarını dönmek için kullanılır. For döngüsü gibi **_for_** anahtar kelimesiyle başlamasına rağmen, parantez içi tamamen farklıdır.

```javascript
// Syntax
for (let key in objectValue) {
    // Loop area
}
```

Parantez içindeki ifadelerde anahtar kelimemiz **in** sözcüğüdür. Bu sözcüğün sağındaki **objectValue**, içinde dönülmesini istediğimiz object tipindeki değişkeni ifade eder. In anahtar kelimesinin solundaki yeni tanımlanmış **key** değişkeni de objenin sıradaki elemanının key değerini temsil eder. **_let_** anahtar kelimesine, [değişkenler](https://endrcn.dev/nodejs/variables/) yazımızda değinmiştik.

For...in döngüsüne örnek olarak, her bir müşterimizin bilgilerini barındıran [object](https://endrcn.dev/nodejs/data-types/#Objects) tipindeki değişkenimizin değerleri arasında dönüp, key-value çiftlerini yazdıran bir kod parçası geliştirelim.

```javascript
// For...in Loop
const customer = {
    first_name: "Ender",
    last_name: "CAN",
    age: 30,
    gender: "Male"
}

for (let key in customer) {
    console.log(key, "-", customer[key]);
}
/*
Output:
first_name - Ender
last_name - CAN
age - 30
gender - Male
*/
```

For...in döngüsü genelde object tipindeki değişkenlerle kullanılsa da [diziler](https://endrcn.dev/nodejs/data-types/#Arrays) içinde dönmek için de kullanılabilir. key alanı, dizilerle kullanıldığında index değerini alır.

```javascript
let arr = [1,2,3,4,5,6,7,8,9,0];
for (let index in arr) {
    console.log(index,"-",arr[index]);
}
/*
Output:
0 - 1
1 - 2
2 - 3
3 - 4
4 - 5
5 - 6
6 - 7
7 - 8
8 - 9
9 - 0
*/
```

TEBRİKLER! Döngüler konusunu tamamladınız. Kendinize bir kahve ısmarlayabilirsiniz :)
