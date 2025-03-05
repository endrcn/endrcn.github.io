---
title: "Node.js - Koşullar (Conditions)"
date: "2021-12-19"
layout: post
author: endrcn
categories: [NodeJS]
tags: [conditions,kosullar,node.js,nodejs]
---

Bir önceki yazımızda [operatörlerden](https://endrcn.dev/nodejs/operators/) bahsetmiştik. Bu yazımızdaysa özellikle karşılaştırma operatörlerinin en sık kullanıldığı yer olan koşullar konusuna değineceğiz.

Node.js ile yazılan temel bir uygulamanın kodları çoğu programlama dilinde olduğu gibi yukarıdan aşağıya doğru çalıştırılır. **Koşullar**, bu akışta bazı düzenlemeler yapmamızı ve akışı yönlendirebilmemizi sağlar.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2021-12-19-at-12.12.18.png)

Koşulsuz Program Akışı

Yukarıdan aşağıya olan bu akışı, bazı durumlarda değiştirme ihtiyacı duyarız. Tam bu noktada koşul tanımlama ve kullanmayı bilmemiz gerekir. Bir koşul tanımlamak için ihtiyaç duyduğumuz anahtar kelimeler: _**if**, **else if**_ ve **_else_** dir. Bir if koşulunun temel syntax'i aşağıdaki gibidir.

```javascript
if (condition1) {
    // condition1 koşulu doğruysa çalışacak
} else if (condition2) {
    // condition2 koşulu doğruysa çalışacak
} else {
    // iki koşul da yanlışsa çalışacak
}
```

Yukarıdaki kod bloğunu tek tek açıklayacak olursak:

**if**: Eğer, condition1 koşulu doğru ise, devamındaki süslü parantezler "{}" arasındaki kodları çalıştırır.

**else if**: Eğer, condition1 koşulu yanlış ise bu alana geçer ve condition2 koşulunu kontrol eder. Eğer, condition2 koşulu doğru ise, devamındaki süslü parantezler "{}" arasındaki kodları çalıştırır.

**else**: Eğer, tüm koşullar yanlış ise else bloğu çalışacaktır.

**condition**: Koşul ifadesi, dönüş değeri [boolean](https://endrcn.dev/nodejs/data-types/#Booleans) olan bir değerdir. [Karş](https://endrcn.dev/nodejs/operators/)[ılaştırma operatörlerinin](https://endrcn.dev/nodejs/operators/#Karsilastirma_Operatorleri) ve [Mantıksal operatörlerin](https://endrcn.dev/nodejs/operators/#Mantiksal_Operatorler) en çok kullanıldığı alan burasıdır. Eğer koşul ifadesi **true** dönerse, devamındaki blok çalıştırılır.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2021-12-19-at-20.06.47.png)

Koşullarla Programı Yönlendirme

Bir if koşulunda, adından da anlaşılacağı gibi **if** olmazsa olmazdır. else if ve else blokları, koşulda opsiyonel olarak ihtiyaca göre kullanılır. En basit haliyle bir **if koşulu**:

```javascript
if (condition) {
    // condition ifadesi true ise bu alandaki kodlar çalışacak.
}
```

Eğer, koşulun olumsuz olduğu durumda çalıştırılması gereken özel bir kod parçamız varsa bu durumda **else** bloğunu kullanmamız gerekir.

```javascript
if (condition) {
    // condition ifadesi <strong>true</strong> ise bu alandaki kodlar çalışacak.
} else {
    // condition ifadesi <strong>false</strong> ise bu alandaki kodlar çalışacak.
}
```

Eğer birbiriyle sıralı bağlantı olan birden fazla koşulumuz varsa, bu durumda **else if** bloğunu kullanmamız gerekir.

```javascript
if (condition1) {
    // condition1 koşulu doğruysa çalışacak
} else if (condition2) {
    // condition2 koşulu doğruysa çalışacak
} else {
    // iki koşul da yanlışsa çalışacak
}
```

Else if bloğu, birden fazla da olabilir.

```javascript
if (condition1) {
    // condition1 koşulu doğruysa çalışacak
} else if (condition2) {
    // condition2 koşulu doğruysa çalışacak
} else if (condition3) {
    // condition3 koşulu doğruysa çalışacak
} else {
    // iki koşul da yanlışsa çalışacak
}
```

Son olarak, iç içe if blokları tanımlayabiliriz. Herhangi bir sınırlama yoktur.

```javascript
if (condition1) {
    if (conditionA) {
        
    } else if (conditionB) {
        
    } else {
        
    }
} else if (condition2){
    if (conditionC) {
        
    } else if (conditionD) {
        
    } else {
        
    }
} else {
    if (conditionE) {
        
    } else if (conditionF) {
        
    } else {
        
    }
}
```

## If Koşulunda Bloklar

If koşulu tanımındaki süslü parantezler "{}" kullanılmadan da yazılabilir. Yalnız bu durumda if koşulundan sonraki ilk satır koşula tabi olacaktır. Diğer satırlar, koşuldan bağımsız olarak çalışacaktır.

```javascript
if (condition)
    console.log("Burası koşula bağlı ekrana basılır.")
console.log("Burası koşuldan bağımsız ekrana basılır.")
```

Yukarıdaki örnek, else if ve else blokları için de geçerlidir.

## Koşul Operatörü

[Operatörler](https://endrcn.dev/nodejs/operators/) yazımızda bahsetmediğimiz koşul operatörü, if koşulunu tek satırda yazmamızı sağlar.

```javascript
// syntax
condition ? "condition is true" : "condition is false";
```

Eğer koşul doğruysa ? ile : arasındaki alan, eğer koşul yanlışsa : dan sonraki alan çalıştırılır.

```javascript
var num1 = 5, num2 = 10;
console.log(num1 > num2 ? "num1 büyüktür num2" : "num1 küçük veya eşittir num2");
```

Birden fazla koşulu da tek satırda yazabiliriz ancak bu okunabilirliği azaltacaktır.

```javascript
var result = (num1 > num2) ? "num1 büyüktür num2" : (num1 < num2) ? "num1 küçüktür num2" : "num1 eşittir num2";
console.log(result); // Output: num1 küçüktür num2
```

Buraya kadar hep teorik ilerledik. Biliyorum ki bu durumda kimsenin aklında bir şey kalmayacak. Halbuki if koşulları, uygulama geliştirirken bolca kullanacağımız ve el hafızamızla yazmamız gereken kod parçalarıdır. Bu nedenle birkaç örnekle mantığını iyice zihnimize yerleştirelim.

## Örnekler

Aşağıdaki senaryolara göre gerekli if koşullarını yazalım.

- Bir dosyadan okuduğumuz iki cümle var. Bu cümleler eşitse ekrana **Eşit**, değilse de **Eşit değil** yazan kod parçasını yazınız.

```javascript
var sentence1 = "Hello, world!";
var sentence2 = "You are awesome!";

if (sentence1 == sentence2)
    console.log("Eşit");
else
    console.log("Eşit değil");
```

Bu örneği koşul operatörünü kullanarak tek satırda da yazabilirdik.

```javascript
var sentence1 = "Hello, world!";
var sentence2 = "You are awesome!";
console.log(sentence1 == sentence2 ? "Eşit" : "Eşit değil");
```

- Maaşları eşit olmayan iki çalışanımız var. Bu çalışanlardan maaşı düşük olanın maaşını %50, yüksek olanın maaşını %20 artıran kod parçasını yazınız.

```javascript
var emp1 = {name: "Ender", salary: 4000};
var emp2 = {name: "Can", salary: 6000};
if (emp1.salary < emp2.salary) {
    emp1.salary *= 1.5;
    emp2.salary *= 1.2;
} else {
    emp1.salary *= 1.2;
    emp2.salary *= 1.5;
}
console.log("Emp1 new salary:", emp1.salary);
console.log("Emp2 new salary:", emp2.salary);
```

- Veritabanından çektiğimiz üç ayrı müşterimiz; 66 yaşındaki AB, 23 yaşındaki BC ve 32 yaşındaki CD dir. Bu müşterilerden en küçüğünün adını yazınız.

```javascript
var cus1 = {name: "AB", age: 66}
var cus2 = {name: "BC", age: 23}
var cus3 = {name: "CD", age: 32}
if (cus1.age < cus2.age) { // AB, BC'den küçükse bu bloğa girer.
    if (cus1.age < cus3.age) { // AB, CD'den küçükse bu bloğa girer.
        console.log(cus1.name);
    } else if (cus1.age > cus3.age) { // AB, CD'den küçükse bu bloğa girer.
        console.log(cus3.name)
    } else { // AB ile CD'nin yaşları eşit
        console.log(cus3.name,"=",cus1.name);
    }
} else if (cus1.age > cus2.age) { // AB, BC'den büyükse bu bloğa girer.
    if (cus2.age < cus3.age) { // BC, CD'den küçükse bu bloğa girer.
        console.log(cus2.name);
    } else if (cus2.age > cus3.age) { // BC, CD'den küçükse bu bloğa girer.
        console.log(cus3.name)
    } else { // AB ile CD'nin yaşları eşit
        console.log(cus3.name,"=",cus2.name);
    }
} else { // AB ile BC'nin yaşları eşitse
    if (cus2.age < cus3.age) { // BC, CD'den küçükse bu bloğa girer.
        console.log(cus2.name,"=",cus1.name);
    } else if (cus2.age > cus3.age) { // BC, CD'den küçükse bu bloğa girer.
        console.log(cus3.name)
    } else { // AB ile CD'nin yaşları eşit
        console.log(cus3.name,"=",cus2.name,"=",cus1.name);
    }
}
// Output: BC
```

Harika gidiyorsunuz! Node.js öğrenme yolunda kritik bir adımı aştınız. Bir sonraki adım olan **döngüler** yazımıza geçebilir ve bu muhteşem macerada kendinizi daha da geliştirebilirsiniz.
