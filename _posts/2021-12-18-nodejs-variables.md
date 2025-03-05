---
title: "Node.js - Değişkenler (Variables)"
date: "2021-12-18"
layout: post
author: endrcn
categories: [NodeJS]
tags: [degiskenler,nodejs,variables,node.js]
---

**Değişkenler**, programlamanın en temel taşlarından biridir. Değişkenler, bellekte saklanan verilere erişmemiz için kullandığımız, verilerin depolandığı bellek adresini ifade eder. Bir değişken tanımlarken hatırlaması kolay ve değişkenin hangi veriyi sakladığını anlatan bir isim verilmesi önemlidir. Bir değişken ismi verilirken sadece İngilizce karakterler ve alt çizgi (_) kullanılmalıdır. Diğer karakterler kullanılmamalıdır.

Değişken tanımlama için kullanılan iki tane anahtar kelime vardır. **_var_** ve **_let_** anahtar kelimelerini kullanarak bir değişken tanımlanabilir. Aşağıdaki örnekte var ve let anahtar kelimeleri kullanılarak değişken tanımlama gösterilmiştir.

```javascript
var first_name;
let lastName;
```

Bir değişken tanımlanırken standart haline gelen iki format vardır. Bunlara **snake_case** ve **camelCase** adı verilir. Değişken ismindeki her bir kelimeyi _ ile ayırarak yapılan yazıma snake_case, ilk kelimesi küçük harf, sonraki her bir kelimenin baş harfi büyük olarak tanımlanan yazıma da camelCase adı verilir. Yukarıdaki örnekte first_name bir snake_case örneği, lastName değişkeni de bir camelCase örneğidir. Eğitim boyunca iki formatı da kullanacağız.

## Değer Atama

Bir değişkene değer atama işlemi “=” operatörü ile gerçekleştirilir. Formatı aşağıdaki gibidir:

```javascript
var first_name = "Ender";
let lastName = "CAN";
```

Atama işlemi tanımlama sırasında yapılabildiği gibi, tanımlamadan sonra da herhangi bir anda yapılabilir.

```javascript
first_name = "Elon"
lastName = "Musk"
```

**_Not:_** _Atama sırasında verilecek değerleri ve nasıl atama yapılacağına [Veri Tipleri](https://endrcn.dev/nodejs-data-types) konumuzda değineceğiz_.

## Sabitler

Sabitler, tanımlanma biçimi olarak değişkenlere oldukça benzerler. Tanımdaki tek fark kullanılan anahtar kelimedir. Değişkenleri var ve let ile tanımlarken, sabitler için **const** kullanılır. Ayrıca bir sabit tanımlandıktan sonra artık değiştirilemez.

```javascript
const PI = 3.14;
```

Eğer **const** ile tanımlanmış bir değişkenin değerini değiştirmek istersek aşağıdaki gibi bir hata alınacaktır:

```javascript
PI = 12;
   ^
TypeError: Assignment to constant variable.
```

Son olarak sabitler tanımlanırken genelde snake_case ve büyük harf kullanılır. Böylece kodlamanın ilerleyen satırlarında kullanılan değerin sabit mi yoksa değişken mi olduğu kolayca anlaşılabilir.

## Global Değişkenler

**Global değişkenler**, uygulama kodlarının her yerinden kolayca erişebileceğimiz değişkenlerdir. Çok fazla kullanılması çakışmalara sebep olacağından fazla tercih edilmez. Tanımlama sırasında var, let, const anahtar kelimeleri yerine, değişken isminin başına **global.** ifadesi eklenir.

```javascript
global.name = "Ender";
```

Değişken çağrılırken ise sadece ismiyle çağrılmalıdır.

```javascript
console.log(name)
```

Tanımlanmış bir global değişkeni silmek için **delete** anahtar kelimesi kullanılır.

```javascript
delete global.name;
```

Silinmiş bir global değişkene erişilmek istendiğinde aşağıdaki gibi bir hata alınacaktır.

```javascript
console.log(name)
            ^
ReferenceError: name is not defined
```

**_Not_**_:Javascript’te satır sonunda noktalı virgül konulması şart değildir. Konulsa da konulmasa da çalışmaya bir etkisi olm_az.

Bir sonraki yazımızda, değişkenlerin devamı niteliğindeki [veri tiplerinden](https://endrcn.dev/nodejs/data-types/) bahsedeceğiz. Böylece değişken tanımlama yazımızı tamamlamış olacağız.
