---
title: "Node.js - Destructuring Assignment"
date: "2021-12-27"
layout: post
author: endrcn
categories: [NodeJS]
tags: [nodejs,node.js,destructuring]
---

**Destructuring Assignment**, [array ve object](https://endrcn.dev/nodejs-data-types/) tipindeki değişkenlerin içinde bulunan değerleri, ayrı birer değişkene kolayca aktarmamızı sağlayan bir yapıdır. Tüm makale boyunca kullanacağımız dizi ve objemizi görelim.

```javascript
const list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const user = {
    firstName: "Ender",
    lastName: "CAN",
    age: 30,
    id: 1234567890
}
```

Destructuring, Javascript'e ES6 ile eklenen bir yapıdır. Bu yapı eklenmeden önce bir dizinin ya da objenin elemanlarını almak için aşağıdaki gibi ayrı ayrı değişkenlere index'ler belirtilerek atamalar yapılması gerekirdi.

```javascript
// Array
let one = list[0];
let two = list[1];

// Object
let firstName = user.firstName;
let lastName = user.lastName;
```

## Array Destructuring

Bir dizinin elemanlarını ayrı ayrı değişkenlere atamamız gerektiğinde destructuring yapısını kullanırız.

```javascript
let [one, two] = list;
```

Yukarıdaki basit örnekte **one** ve **two** adında iki değişken oluşturup bu değişkenlerin içine list dizisindeki ilk iki eleman atanır. Değişkenleri ayrı da tanımlayıp ardından destructuring uygulayabiliriz. Destructuring uygulandığında sadece atama yapılır, ana diziden eleman silinmez.

```javascript
let one, two;
[one, two] = list;


console.log(one, two); // Output: 1 2
console.log(list); // Output: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

Dizinin kalan elemanlarını da tek bir değişkende toplamak istersek **...** operatöründen yararlanırız. ... operatörü ile belirtilen değişkene kalan değerler dizi olarak atanır.

```javascript
let one, two, rest;
[one, two, ...rest] = list;

console.log(one, two); // Output: 1 2
console.log(rest); // Output: [3, 4, 5, 6, 7, 8, 9, 10]
```

Kalan elemanları alacak olan değişken, destructuring aşamasında en sonda yer almalıdır. Eğer ardından bir değişken daha tanımlanmak istenirse **SyntaxError** hatası fırlatılır.

```javascript
let one, two, rest, ten;
[one, two, ...rest, ten] = list; // Output: SyntaxError: Rest element must be last element
```

Destructuring aşamasında atama yapılan değişkenlere default değerler de verilebilir. Bu durumda dizide olmayan ya da boş değere sahip olan bir eleman atanmaya çalışıldığında default değer atanır.

```javascript
let one, two, three, four;
let lst = [1, undefined, 3];
[one = "a", two = 2, three = "b", four = 4] = lst;

console.log(one, two, three, four) // Output: 1 2 3 4
```

Yukarıdaki örnekte **lst** dizisinde two değişkenine karşılık gelen eleman undefined ve four değişkenine karşılık herhangi bir eleman olmamasından kaynaklı default değerler atanmıştır. Diğer değişkenlere (one, three) karşılık gelen dizi elemanları olduğundan default değerler yerine dizideki değerler atanmıştır.

### Dizi Dönen Fonksiyonlarda Destructuring

Dönüş değeri dizi olan [fonksiyonlara](https://endrcn.dev/nodejs-functions/) da yukarıdaki tüm işlemleri uygulayabiliriz. Uygulama sırasında atama [operatörünün](https://endrcn.dev/nodejs-operators/) (=) sağ tarafında dizi dönen fonksiyonu çağırmak yeterlidir.

```javascript
function getList(){
    return list;
}
[one, two] = getList();
console.log(one, two); // Output: 1 2
```

Dizi dönen fonksiyonlarda **destructuring** uygularken dönen dizideki bazı elemanları atlatarak atama yapabiliriz. Bunun için atlamak istediğimiz elemanın indisine karşılık gelen alanı boş bırakmak yeterlidir.

```javascript
[one, , three] = getList();
console.log(one, three); // Output: 1 3

[,,,four] = getList();
console.log(four); // Output: 4
```

### Swap İşlemleri

Destructuring kullanarak iki değişkenin değerlerinin birbirlerine atanmasını kolayca sağlayabiliriz. Öncelikle bu işlemi destructuring kullanmadan nasıl yapacağımıza bir bakalım.

```javascript
let a = 5;
let b = 3;

let tmp = a;
a = b;
b = tmp;

console.log("a="+a, "b="+b); // Output: a=3 b=5
```

Destructuring kullanmadan yaptığımız örnekte _tmp_ adında bir geçici değişkene ihtiyaç duyduk. Bir de aynı işlemi destructuring ile nasıl yapacağımıza bakalım.

```javascript
let a = 5;
let b = 3;

[a, b] = [b, a];

console.log("a="+a,"b="+b); // Output: a=3 b=5
```

Hem fazladan bir değişkene ihtiyaç duymadık hem de işlemimizi tek satırda yapabildik. Dizilerde destructuring yapısının sonuna geldik.

## Object Destructuring

**Destructuring assignment**, dizileri ayrıştırdığı gibi objeleri de ayrıştırır. Farklı olarak, dizilerde tanım sıralaması önemliyken, objelerde key ismi önemlidir.

```javascript
const user = {
    firstName: "Ender",
    lastName: "CAN",
    age: 30,
    id: 1234567890
}
```

Yukarıdaki user objesinden id ve firstName alanlarını ayrı bir değişkene **destructuring** yöntemiyle atayalım.

```javascript
let { id, firstName} = user;
console.log(id, firstName); // Output: 1234567890 Ender
```

Değişkenleri önceden tanımlayıp ardından atama işlemi de yapabiliriz. Bu durumda tüm atama işlemini parantezler içine almalıyız. Aksi halde [SyntaxError](https://endrcn.dev/nodejs-error-handling/) hatası fırlatılır.

```javascript
let age, lastName;
({age, lastName} = user);

console.log(age, lastName); // Output: 30 CAN
```

Eğer değişken isimlerini obje içindekinden farklı yapmak istersek, **destructuring** aşamasında değişiklik yapmak istediğimiz key'in değerine yeni değişkenimizin adını yazmamız gerekir.

```javascript
const {firstName: name, lastName: surname} = user;
console.log(name, surname); // Output: Ender CAN
```

Dizilerde olduğu gibi, objelerde de default değer atama işlemi vardır. Eğer destructuring ile almak istediğimiz field, obje içerisinde mevcut değilse, default değer ilgili değişkene atanır.

```javascript
var {firstName = "Sena", school = "YTÜ"} = user;
console.log(firstName, school); // Output: Ender YTÜ
```

Yukarıdaki son iki işlemi bir arada da kullanabiliriz. Yani hem yeni değişken ismi tanımlayıp hem de default değer ataması yapabiliriz.

```javascript
const {firstName: name = "Sena", schoolName: school = "YTÜ"} = user;
console.log(name, school); // Output: Ender YTÜ
```

Yine dizilerde olduğu gibi, obje içinde yer alan ancak atama işlemlerimizde yer almayan diğer alanları tek değişkende toplayabiliriz.

```javascript
const {firstName, lastName, ...rest} = user;
console.log(firstName, lastName) // Output: Ender CAN
console.log(rest); // Output: { age: 30, id: 1234567890 }
```

Muhteşem! Destructuring konusunun temellerini tamamladınız. Destructuring, kod kalitenizi geliştirecek ve okunabilir kod yazmanızı kolaylaştıracaktır. Node.js geliştirme yolunda kod kalitenizi artırmaya devam ettiğiniz için tebrik ederim.
