---
title: "Node.js - Operatörler (Operators)"
date: "2021-12-18"
layout: post
author: endrcn
categories: [NodeJS]
tags: [nodejs,node.js,operatorler,operators]
---

Daha önceki yazılarımızda Node.js'te [değişken tanımlama](https://endrcn.dev/nodejs-variables/) ve [veri tiplerinden](https://endrcn.dev/nodejs-data-types/) bahsetmiştik. Bu yazımızda ise operatörlere değineceğiz. Tüm programlama dillerinin olmazsa olmazlarından biri olan operatörler; aritmetik operatörler, atama operatörleri, karşılaştırma operatörleri, mantıksal operatörler olmak üzere beş bölüme ayrılıyor. Tüm bölümleri ayrı başlıklar altında inceleyelim.

## Aritmetik Operatörler

Matematiksel işlemleri yapmak için kullanılır. Number tipindeki değişkenler üzerinde toplama(+), çıkarma(-), çarpma(*), bölme(/) ve mod alma(%) olmak üzere beş tanedir. **Matematiksel operatörlerin** işlem önceliği matematikteki kurallar ile aynıdır.

```javascript
var total = 5.12+5; // 10.12
var division = 10/5; // 2
var multiplication = 5*6; // 30
var subtraction = 6-5; // 1
var mod = 7%5; // 2
```

Yukarıdaki kod parçasında iki sayıyı topladığımız gibi, number tipinde iki -ya da daha fazla- değişkeni de matematiksel operatörlerle kullanabiliriz.

```javascript
var num1 = 10.12;
var num2 = 2;
var total = num1 + num2; // 12.12
var division = num1 / num2; // 5.06
var multiplication = num1 * num2; // 20.24
var subtraction = num1 - num2; // 8.12
var mod = num1 % num2; // 0.12
```

Matematiksel operatörler zaten herkesin bildiği yapılar oldukları için bu operatörleri tek tek açıklamaya gerek duymuyorum. Sadece **mod(%)** operatörünü açıklayacağım. Mod operatörü; operatörün solundaki sayının, sağındaki sayıya bölümünden kalanı verir. Yukarıdaki **7%5** örneği üzerinden açıklarsak, 7'nin 5'e bölümünden kalan 2'dir.

Ek olarak, aritmetik operatörlerde bir arttırma(++) ve bir eksiltme(--) operatörleri mevcuttur. Bu operatörler, kullanıldığı değişkenlerin değerini bir artırır ya da bir eksiltir. Kullanımı aşağıdaki gibidir.

```javascript
var i = 0;
i++ // output: 1
<meta charset="utf-8">var i = 0;
++i // output: 1
<meta charset="utf-8">var i = 0;
i-- // output: -1
--i // output: -1
```

++ ve -- operatörlerinin değişkenin başında veya sonunda kullanılması arasında ince bir fark vardır. Operatör eğer değişkenin önünde kullanılırsa, önce arttırma/eksiltme işlemi yapılır ardından asıl işlem(örn: loglama) yapılır. Eğer operatör değişkenin sonunda kullanılırsa, önce asıl işlem yapılır, sonra arttırma/eksiltme işlemi yapılır. Bunu en kolay değişken değerlerini loglayarak görebiliriz.

```javascript
var i = 0;
console.log(i++); // output: 0
i = 0;
console.log(++i); // output: 1
i = 0;
console.log(i--); // output: 0
i = 0;
console.log(--i); // output: -1
```

### Aritmetik Operatörlerde İşlem Önceliği

1. Çarpma(*) / Bölme(/)
2. Mod(%)
3. Toplama(+) / Çıkarma(-)

Aynı seviyedeki operatörlerde işlem sırası dikkate alınır. Soldan sağa doğru bakılan sıralamada önce hangi işlem yer alıyorsa o yapılır. İşlem önceliğini değiştirmek için aynı matematikte olduğu gibi parantezlerden yararlanabiliriz. Aşağıdaki örnekten işlem önceliklerini daha iyi anlayabiliriz.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2021-12-19-at-00.50.27.png)

Matematiksel Operatörlerde İşlem Önceliği

Aynı işlemi bir de parantezlerle yaparak toplama ve mod alma işleminin çarpma ve bölmeden önce yapılmasını sağlayalım.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2021-12-19-at-00.46.48.png)

Matematiksel Operatörlerde İşlem Önceliği - 2

Aritmetik operatörler number tipindeki değişkenler ve değerler ile kullanılabilir. Ancak + operatörü için bir istisna var. + operatörü, string tipindeki değişkenler ile kullanılabilir. [String](https://endrcn.dev/nodejs-data-types/#Strings) tipindeki bir değişkenle number ya da string tipindeki başka bir değişken + operatörü ile birleştirilebilir. Bunun sonucunda yeni değer string tipinde olur.

```javascript
var str = "5";
var number = 5;
var result = str + number;
console.log(typeof result, result); // output: string 55
```

## Atama Operatörleri

Atama operatörleri, adından da anlaşılacağı gibi bir değişkene veri atamak için kullanılır. En temel atama operatörü olan **= operatörünü** [değişkenler](https://endrcn.dev/nodejs-variables/) ve [veri tipleri](https://endrcn.dev/nodejs-data-types/) yazılarımızda kullanmıştık. Atama operatörleri; sağındaki değeri, solundaki değişkene atama görevini üstlenirler. = operatörü bir değişkene değer atarken, bu operatör aritmetik operatörlerle birleştirilerek toplayarak atama, çıkararak atama, çarparak atama, bölerek atama ve mod alarak atama operatörlerine dönüşür.

```javascript
var num = 0; // temel atama işlemi
var num2 = 1; // <meta charset="utf-8">temel atama işlemi
num += num2; // toplayarak atama, num = num + num2
num -= 6 // çıkararak atama, num = num - 6
num /= 2 // bölerek atama, num = num / 2
num *= num2 // çarparak atama, num = num / num2
num %= num2 // mod alarak atama, num = num % num2
```

_**Not:** Aritmetik operatörlerle birleştirilen atama operatörleri sadece number tipindeki değişkenlerle kullanılabilir. + operatörü için istisnayı yukarıda açıklamıştık._

## Karşılaştırma Operatörleri

Karşılaştırma operatörleri, iki değişkeni ya da değeri karşılaştırmak için kullanılır. Bu operatörler genelde [koşullarda](https://endrcn.dev/nodejs-conditions) kullanırız. Bu operatörler kullanıldığında sonuç [boolean](https://endrcn.dev/nodejs-data-types/) veri tipinde oluşur.

<table><tbody><tr><td>Operatör</td><td>Açıklama</td></tr><tr><td>a == b</td><td>a ile b nin değerleri eşit mi kontrolü, değerler eşitse, <strong>true</strong></td></tr><tr><td>a === b</td><td>a ile b nin değerleri ve tipleri eşit mi kontrolü, hem değerler hem de tipleri eşitse, <strong>true</strong></td></tr><tr><td>a != b</td><td>a ile b nin değerlerinin eşit değil mi kontrolü, değerleri eşit değilse, <strong>true</strong></td></tr><tr><td>a !== b</td><td><meta charset="utf-8">a ile b nin değerleri ve tipleri eşit değil mi kontrolü, hem değerleri hem de tipleri eşit değilse, <strong>true</strong></td></tr><tr><td>a &gt; b</td><td>a, b'den büyük mü kontrolü. a, b'den büyükse, <strong>true</strong></td></tr><tr><td>a &lt; b</td><td><meta charset="utf-8">a, b'den küçük mü kontrolü. a, b'den <meta charset="utf-8">küçükse, <strong>true</strong></td></tr><tr><td>a &gt;= b</td><td>a, b'den büyük veya eşit mi kontrolü. a, b'den büyük veya b'ye eşitse, <meta charset="utf-8"><strong>true</strong></td></tr><tr><td>a &lt;= b</td><td><meta charset="utf-8">a, b'den küçük veya eşit mi kontrolü. a, b'den küçük veya b'ye eşitse, <meta charset="utf-8"><strong>true</strong></td></tr></tbody></table>

Tablo 1 - Karşılaştırma Operatörleri

Bir de kod parçasında sonuçları görelim.

```javascript
var str = "5";
var num = 5;
var result;
result = str == num // result = true
result = str === num // result = false
result = str != num // result = false
result = str !== num // result = true
result = str > num // result = false
result = str >= num // result = true
result = str < num // result = false
result = str <= num // result = true
```

## Mantıksal Operatörler

Mantıksal operatörler, **ve**(&&), **veya**(||) ve **değil**(!) olmak üzere üç tanedir. Bu operatörler, verilerin true olması ile ilgilenirler ve buna göre sonuç dönerler.

### AND(&&) Operatörü

AND operatörü, **karşılaştırılan tüm değerlerin** true olup olmadığını kontrol eder. Eğer tüm değerler true ise sonuç **true**, herhangi biri false ise sonuç **false** olacaktır.

<table><tbody><tr><td><strong>Karşılaştırma</strong></td><td><strong>Sonuç</strong></td></tr><tr><td>true &amp;&amp; true</td><td><strong>true</strong></td></tr><tr><td>false &amp;&amp; true</td><td><strong>false</strong></td></tr><tr><td>true &amp;&amp; false</td><td><strong>false</strong></td></tr><tr><td>false &amp;&amp; false</td><td><strong>false</strong></td></tr></tbody></table>

Tablo 2 - AND Operatörü

### OR(||) Operatörü

OR operatörü, **karşılaştırılan değerlerden herhangi birinin** true olup olmadığını kontrol eder. Eğer değerlerden en az biri true ise sonuç **true**, tüm değerler false ise sonuç **false** olacaktır.

<table><tbody><tr><td><meta charset="utf-8"><strong>Karşılaştırma</strong></td><td><strong>Sonuç</strong></td></tr><tr><td>true || true</td><td><strong>true</strong></td></tr><tr><td>false || true</td><td><strong>true</strong></td></tr><tr><td>true || false</td><td><strong>true</strong></td></tr><tr><td>false || false</td><td><strong>false</strong></td></tr></tbody></table>

Tablo 3 - OR Operatörü

### NOT(!) Operatörü

NOT operatörü, verilen değerin tersini alır. Eğer verilen değer true ise sonuç **false**, false ise sonuç **true** olacaktır.

<table><tbody><tr><td><strong>Karşılaştırma</strong></td><td><strong>Sonuç</strong></td></tr><tr><td>!true</td><td><strong>false</strong></td></tr><tr><td>!false</td><td><strong>true</strong></td></tr></tbody></table>

Tablo 4 - NOT Operatörü

Operatör konusunun da sonuna geldik. Bir sonraki makalede [koşullardan](https://endrcn.dev/nodejs-conditions/) bahsedeceğiz. Koşullarla birlikte yazdığımız uygulamayı nasıl yönlendirebileceğimizi göreceğiz. Hız kesmeden devam edelim!
