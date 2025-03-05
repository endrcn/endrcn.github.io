---
title: "Node.js - Generators"
date: "2022-01-13"
layout: post
author: endrcn
categories: [NodeJS]
---

Generators, bazı iteratif işlemlerimizi yapmamızı sağlayan fonksiyon türüdür. Generator tipindeki fonksiyonlar, bir döngü (iterasyon) içinde çağrıldıklarında yeni sonuçlar döner. Dönüşü return ile değil **yield** anahtar kelimesi ile yapılır. Döndürdüğü değerler genelde birbiriyle ilişkili değerlerdir.

Generator fonksiyonların çıkış noktası, asenkron programlamada kullandığımız callback yapısındaki sorunları çözmek ve sanki senkron progralama yapıyor gibi asenkron programlamayı yapabilmemizi sağlamaktır. Şimdi üst üste yaptığımız tanımları adım adım görelim.

Generator fonksiyon, normal fonksiyonlardan farklı olarak çağrıldığı anda içinde tanımlanmış tüm işleri yapmaz. İlk yield dönüşüne kadar olan kısmı işletir ve döner. Ayrıca çağrılma biçimleri de normal fonksiyonlardan farklıdır.

![_config.yml]({{ site.baseurl }}/assets/images/1_7X8rtWOiz5RKENZ_vugmKg.png)

Normal Functions vs Generators

## Generators Tanımlama

Bir generator fonksiyonu tanımlamak için aşağıdaki gibi bir syntax kullanıyoruz.

```javascript
function * getNumber() {
    yield 1;
    yield 2;
    yield 3;
}
```

Yukarıdaki kodu incelediğimizde:

- Generator fonksiyon tanımında * karakterinden yararlanırız.
- Dönüş değerini **yield** anahtar kelimesiyle yaparız.
- Birden fazla yield kullanabiliriz. [Fonksiyonlar yazımızda](https://endrcn.dev/nodejs/functions/) gördüğümüz gibi return'u sadece bir kez kullanabiliyorduk.

**Not:** * karakteri her iki tarafla da birleştirilebilir.

```javascript
function * getNumber() {}
function* getNumber() {}
function *getNumber() {}

let getNumber = function * () {}
let getNumber = function* () {}
let getNumber = function *() {}
```

[Arrow function](https://endrcn.dev/nodejs/functions/#Arrow_Functions) yapısını kullanarak generator tanımı yapamıyoruz. Tanımlamaya çalışırsak SyntaxError hatası alırız.

let generator = *() => {} // SyntaxError
let generator = ()* => {} // SyntaxError
let generator = (*) => {} // SyntaxError

Bir generator fonksiyon tanımladığımızda dönüş değeri **value** ve **done** alanlarına object tipinde bir veri olur. _value_, **yield** ile dönülen değer, _done_ ise son yield'a gelinip gelinmediği yani iterasyonun sonu olup olmadığını belirten boolean bir değerdir.

```javascript
{
   value: any
   done: Boolean
}
```

## Generators Çağırma

Bir generator fonksiyonun normal fonksiyondak farklı şekilde çağrıldığını söylemiştik. Normal bir fonksiyon gibi çağırdığımızda dönüş değeri **Object [Generator] {}** olacaktır.

```javascript
let gen = getNumber();
console.log(gen); // Output: Object [Generator] {}
```

Generator fonksiyonların bir iterator oluşturduğunu düşünürsek, bu iterasyonu ilerletmek için bir yapıya ihtiyaç duyduğumuzu söyleyebiliriz. Generators, iterasyonu sağlamak için üç tane metoda sahiptir.

- **next()**: Bir sonraki yield sonucunu object olarak döner.
- **return()**: Generator fonksiyonun sonlanmasını sağlar. Dönüş değeri aynı yapıdadır, eğer bir parametre verilirse dönüş objesindeki _value_ alanı bu değere sahip olacaktır.
- **throw()**: Generator fonksiyonu sonlandırarak bir hata fırlatır. [Try-catch](https://endrcn.dev/nodejs/error-handling/#Try-Catch) ile yakalanmalıdır.

Buraya kadar sürekli tanımlar ve açıklamalar yaptık ancak Generator fonksiyonları anlamak için örneklere ihtiyacımız var. Haydi başlayalım.

## Generators Kullanım

Generator fonksiyonların kullanımını örnekler üzerinden açıklamanın daha doğru olacağını düşünüyorum.

**Örnek 1:** İlk iterasyonda 1, ikincide 3, üçüncüde 5 ve dördüncüde 7 sonucu dönen generator fonksiyon tanımlayalım.

```javascript
function* getNumber() {
    yield 1;
    yield 3;
    yield 5;
    yield 7;
}

let number = getNumber();

console.log(number.next());
console.log(number.next());
console.log(number.next());
console.log(number.next());
console.log(number.next());
/*
Output:
{ value: 1, done: false }
{ value: 3, done: false }
{ value: 5, done: false }
{ value: 7, done: false }
{ value: undefined, done: true }
*/
```

Sonuçlarda object döndüğü için next() metodundan sonra direkt value değerini de ekrana bastırabiliriz.

```javascript
console.log(number.next().value);
console.log(number.next().value);
console.log(number.next().value);
console.log(number.next().value);
console.log(number.next().value);
/*
Output:
1
3
5
7
*/
```

**Örnek 2:** Her çağrıldığında çift sayıları sırayla dönen sonsuz bir generator fonksiyon yazalım.

```javascript
function* evenNumbers() {
    let num = 0;
    while (true) {
        yield num;
        num += 2;
    }
}

let even = evenNumbers();

console.log(even.next().value);
console.log(even.next().value);
console.log(even.next().value);
console.log(even.next().value);
/*
Output:
0
2
4
6
*/
```

Sonsuz tanımlanmış bir generator fonksiyonu bitirmek için **return()** metodunu kullanabiliriz.

```javascript
console.log(even.next().value); // 0
console.log(even.next().value); // 2
console.log(even.next().value); // 4
console.log(even.next().value); // 6
console.log("RETURN",even.return(-1).value); // -1
console.log(even.next()); // {value: undefined, done: true}
```

**Not:** Bir generator fonksiyonu her çağrıldığında yeni bir iterasyon oluşturur ve bu iterasyonlar birbirlerinden bağımsız işletilir.

```javascript
console.log(evenNumbers().next().value); // 0
console.log(evenNumbers().next().value); // 0
console.log(evenNumbers().next().value); // 0
console.log(evenNumbers().next().value); // 0
console.log(evenNumbers().next().value); // 0
```

Yukarıdaki örneği bir de for...of döngüsü kullanarak yapalım.

```javascript
let even = evenNumbers();

for (let num of even) {
    console.log(num);
    if (num > 10) {
        break;
    }
}
console.log(even.next());
/*
Output:
0
2
4
6
8
10
12
{ value: undefined, done: true }
*/
```

Yukarıdaki örnekte sondaki event.next() metodunu özellikle ekledim. for...of döngüsü ile iterasyon yaptığımızda, döngü ile birlikte generator iterasyonu da sonlanır. Örnekte de gördüğümüz gibi döngü dışında next() ile yeni değer istediğimde _done: true_ döndü.

**Örnek 3:** Recursive fonksiyon ile yazılan fibonacci dizisini generator fonksiyon ile yazalım.

```javascript
// Recursion
function fibonacci(n) {
    if (n <= 1)
        return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

console.log(fibonacci(9)); // Output: 34
```

Generator ile fibonacci dizilimini adım adım gösteren fonksiyonumuz

```javascript
// Generator
function * fibonacci(seed1, seed2) {
  while (true) {
    yield (() => {
      seed2 = seed2 + seed1;
      seed1 = seed2 - seed1;
      return seed2;
    })();
  }
}
const fib = fibonacci(0, 1);
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2
console.log(fib.next().value); // 3
console.log(fib.next().value); // 5
console.log(fib.next().value); // 8
console.log(fib.next().value); // 13
console.log(fib.next().value); // 21
console.log(fib.next().value); // 34
```

Generators konusunun da sonuna geldik. Artık Node.js'in temellerini tamamladık diye düşünebiliriz. İlerleyen konularda artık bir API nasıl oluşturulur, loglama nasıl olmalı, ölçekleme nasıl yapılır gibi daha derin konulara geçeceğiz. Böyle devam :)

## Kaynaklar

- [https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5](https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5)
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)
- [https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e](https://codeburst.io/what-are-javascript-generators-and-how-to-use-them-c6f2713fd12e)
