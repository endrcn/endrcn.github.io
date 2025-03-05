---
layout: post
author: endrcn
title: "Node.js - Veri Tipleri (Data Types)"
date: "2021-12-18"
categories: [NodeJS]
tags: [ data types, nodejs, veri tipleri ]
---

**Veri tipleri**, tüm programlama dillerinde var olan temel taşlardan biridir. Veri tiplerinin programlamada doğru seçilmesi performans ve uygulanabilirlik açısından önemlidir. Tipleri doğru öğrenip uygulamak, geliştirmedeki kaliteyi artıracaktır. En temel yapılardan biri olduğu için tüm eğitim boyunca kullanacağımız veri tiplerini zamanla aklınızda oturtmuş ve doğru seçimleri kolayca yapacak düzeye gelmiş olacaksınız. Javascript’te veri tipleri atama sırasında belirlenir. Değişkenin veri tipi, atanan değere göre belirlenir.

Bir değişkenin veri tipinin ne olduğunu sorgulamak için **typeof()** methodu kullanılır.

```javascript
var name = "Ender";
console.log(typeof(name)) // output: string
```

## Strings

Teknik olarak karakterlerin birleşiminden oluşur. Metinleri temsil etmek için **string** veri tipi kullanılır. Bir değişkeni string yapabilmek için, atanan değerin çift tırnak (") ya da tek tırnak (') arasına alınması yeterlidir.

```javascript
var firstName = "Ender";
var lastName = 'CAN';
var strNumber = "3.14"
var strNumber2 = "3"

console.log(typeof firstName) // output: string
console.log(typeof strNumber) // output: string
```

## Numbers

Number tipi, nümerik verileri tanımlamak için kullanılır. Numerik verilerle matematiksel işlemler yapabiliriz. Ondalıklı sayılar için nokta ile ayrım yapılmalıdır. Tam sayılar için **Integer**, ondalıklı sayılar için **Float** isimlendirmesi kullanılır. Farklı programlama dillerinde bu iki tip ayrı veri tipleri olmasına rağmen Javascript’te her iki tip de **number** olarak tanımlanmıştır.

```javascript
var number1 = 3
var number2 = 3.14

console.log(typeof(number1)) // output: number
console.log(typeof(number2)) // output: number
````

## Booleans

Boolean veri tipi, bir durumu temsil eder. Alabileceği iki değer vardır. Bunlar **true** ve **false** değerleridir. Boolean bir değişken aşağıdaki gibi tanımlanır.

```javascript
var bool = true
console.log(typeof bool) // output: boolean
````

[Koşullar ve Operatörler](https://endrcn.dev/nodejs/conditions-and-operators) başlığı altında daha detaylı anlatacağız ancak burada değinmeden geçmeyi doğru bulmadım. Bir değişkenin tersini not (!) operatörü ile alabiliriz. “!” operatörünü kullanarak bir değişken true ise false, false ise true yapabiliriz.

```javascript
var bool = true
console.log(bool) // output: true
console.log(typeof bool) // output: boolean
bool = !bool
console.log(bool) // output: false
````

Boolean olmayan bir değişenin de “!” operatörü kullanarak tersi alınabilir.

```javascript
var num = 5
console.log(!num) // output: false
var str = "ender"
console.log(!str) // output: false
var arr = ["ender","can"]
console.log(!arr) // output: false
```

Boş değere sahip farklı tipteki değişkenlerin “!” operatörü ile tersi alındığında sonuç **true** olacaktır.

```javascript
var num = 0
console.log(!num) // output: true
var str = ""
console.log(!str) // output: true
````

## Arrays

Bir program geliştirirken listelere sıklıkla ihtiyaç duyarız. Liste halindeki verileri saklamak için kullanılan veri tipi array’dir. Bir liste(dizi) tanımlamak için verileri köşeli parantezler arasında yazmamız gerekir.

```javascript
var arr1 = ["ender", "can", true, 14]
console.log(arr1); // output: [ 'ender', 'can', true, 14 ]
console.log(typeof arr1) // output: object
```

Dizilerin bir elemanına erişmek için index’lerden yararlanılır. Bir dizinin ilk elemanının index’i 0’dır. Bu durumda N elemanlı bir dizinin sonuncu elemanına ulaşmak için N-1. index’ini almak gerekir. Yukarıda tanımladığımız **_arr1_** dizisinin ilk elemanına erişelim.

```javascript
var firstItem = arr1[0];
console.log(firstItem) // output: "ender"
```

Bir dizinin son elemanına erişelim.

```javascript
var len = arr1.length;
var lastItem = arr1[len - 1];
console.log(lastItem) // output: lastItem
```

Yukarıdaki kod parçasında **arr1.length** komutu, **_arr1_** dizisinin boyutunu döndürür. Bu değeri de len değişkenine atadık. Dizinin index’leri 0’dan başladığı için sonuncu elemanına erişmek istediğimizde **_len-1_** index’ine sahip elemanı çağırmamız gerekir.

Dizide olmayan bir index’e erişilmeye çalışıldığında bir hata alınmaz. Sadece dönüş undefined olacaktır.

```javascript
console.log(arr1[len]); // output: undefined
```

Şimdiye kadar bir dizi tanımlamayı, bir elemanına erişmeyi ve boyutunu almayı gördük. Peki bu dizinin elemanını değiştirmek, bir diziye eleman eklemek ya da bir diziden eleman çıkarmak istersek bunu nasıl yapabiliriz? Bu işlemler için array tipine özel methodlar mevcuttur.

Bir diziye eleman eklemek için **_push()_** methodu kullanılır. Boş bir dizi oluşturup ardından içine elemanlar yükleyelim.

```javascript
var arr2 = [];
console.log(arr2); // output: []
arr2.push("ender")
arr2.push("can")
arr2.push(true)
arr2.push(30)
console.log(arr2) // output: [ 'ender', 'can', true, 30 ]
```

Ayrıca bir diziye eleman eklemenin ikinci yöntemi splice() metodudur. Methodun aldığı parametreler; **_splice(start, deleteCount, newItem)_**. Aldığı parametrelerden de anlaşıldığı gibi splice metodu diziden eleman silmek için de kullanılır. Aşağıda bir eleman silmek için splice methodunu nasıl kullanacağımızı da göstereceğiz. Şimdi bir eleman ekleme işlemini **splice** metoduyla nasıl yaparız bakalım.

```javascript
arr2.splice(0, 0, "newItem");
console.log(arr2) // output: [ 'newItem', 'ender', 'can', true, 30 ]
```

Yukarıdaki kod parçasında kullanılan splice methodunun aldığı parametreler sırasıyla; **0 ->** elemanın ekleneceği index, **0 ->** silinecek eleman sayısı, “newItem” -> eklenecek elemanın değeri.

Bir dizinin elemanını değiştirmek için ilgili elemanın indisine atama yapılması yeterlidir.

```javascript
arr2[0] = "can"
arr2[1] = "ender"
arr2[3] = 32
console.log(arr2) // output: [ 'can', 'ender', 'can', 32, 30 ]
```

Bir diziden eleman silmek için birden fazla yöntem var; bu yöntemlerden ilki pop() methodu. Bu method, dizinin son elemanını siler.

```javascript
arr2.pop();
console.log(arr2) // output: [ 'can', 'ender', 'can', 32 ]
arr2.pop();
console.log(arr2) // output: [ 'can', 'ender', 'can' ]
```

İkinci yöntem splice() methodunu kullanmaktır. Bu method ile istenen index’ten başlanıp belirtilen sayı kadar silme işlemi yapılabilir. Methodun aldığı iki parametre vardır.

```javascript
arr2.splice(0, 1);
console.log(arr2) // output: [ 'ender', 'can', 32, 30 ]
arr2.splice(1, 1);
console.log(arr2) // output: [ 'ender', 32, 30 ]
console.log(arr2) // output: [ 'ender', 32 ]
```

splice methoduna verilen parametreler; **0 ->** silmeye başlanacak index değeri, **1 ->** silinecek eleman sayısı.

**_Not:_** _Bir diziden bir ara eleman silindiğinde, tüm dizi elemanları bir sola kayar. Bu nedenle eleman silme sırasına dikkat edilmelidir._

## Objects

**Objeler**, Javascript’te önemli bir yere sahiptir. Birçok alanda kullanılan objelerin tanımlanmasında süslü parantezler “{}” kullanılır. Boş bir obje tanımlaması aşağıdaki gibi yapılır

```javascript
var obj = {}
console.log(obj); // output: {}
console.log(typeof obj) // output: object
```

Objeler, key-value çiftlerinden meydana gelirler. Bir objedeki bir değere erişmek için key’ler kullanılır. Key’ler, string verilerdir, value alanları herhangi bir veri tipinde olabileceği gibi fonksiyon ya da bir nesne de olabilirler. Dolu bir obje oluşturma biçimi aşağıdaki gibidir.

```javascript
var obj = {
    aString: "value1",
    anInteger: 5,
    "1aFloat": 3.14,
    "an-Array": ["ender", "can"]
}
```

Obje tanımlarken eğer key değeri değişken tanımına uyuyorsa tırnak içine yazılması zorunlu değildir. Ancak değişken tanımına uymayan key’ler mutlaka tırnak içinde yazılmalıdır. Çift ya da tek tırnak farkı yoktur.

Eğer bir objede iki tane aynı key tanımlanmışsa, son tanımlanan öncekini ezecektir.

```javascript
var obj = {
    aString: "value1",
    anInteger: 5,
    "1aFloat": 3.14,
    "an-Array": ["ender", "can"],
    aString: "value2"
}
console.log(obj);
/**
Output:
{
  aString: 'value2',
  anInteger: 5,
  '1aFloat': 3.14,
  'an-Array': [ 'ender', 'can' ]
}
*/
```

Bir objenin elemanına erişmenin iki yöntemi vardır. İlk yöntem . operatörü ile erişim, ikinci yöntem ise dizilerdeki gibi köşeli parantezler arasına key yazılarak erişimdir.

```javascript
var str = obj["aString"]
console.log(str) // output: value2
var num = obj.anInteger
console.log(num) // output: 5
```

(**.**) Operatörünün kullanılabilmesi için key değerinin değişken tanımına uyması gerekir. Değişken tanımına uymayan key’lere erişim için köşeli parantezler kullanılmalıdır.

```javascript
console.log(obj["1aFloat"]) // output: 3.14
console.log(obj["an-Array"]) // output: [ 'ender', 'can' ]
```

Eğer **.** operatörü ile erişilmeye çalışılırsa syntax error alınacaktır.

```javascript
console.log(obj.1aFloat)
            ^^^
SyntaxError: missing ) after argument list
```

Obje içinde olmayan bir key’e erişilmek istendiğinde sonuç **undefined** olacaktır.

```javascript
console.log(obj.undefinedItem) // output: undefined
```

Bir objeye yeni değer eklemek için, eklenecek key değerine bir atama yapılması yeterlidir.

```javascript
obj.newItem = "newItem"
console.log(obj)
/**
Output:
{
  aString: 'value2',
  anInteger: 5,
  '1aFloat': 3.14,
  'an-Array': [ 'ender', 'can' ],
  newItem: 'newItem'
}
*/
```

Objede bulunan bir değeri değiştirmek istediğimizde, key’e erişip değişiklik yapılabilir.

```javascript
obj["1aFloat"] = "convert to string"
obj.aString = 3;
console.log(obj)
/**
Output:
{
  aString: 3,
  anInteger: 5,
  '1aFloat': 'convert to string',
  'an-Array': [ 'ender', 'can' ],
  newItem: 'newItem'
}
 */
 ```

Bir objeden bir değer silebilmek için **_delete_** komutu kullanılır.

```javascript
delete obj["1aFloat"]
delete obj.aString;
console.log(obj) // output: { anInteger: 5, 'an-Array': [ 'ender', 'can' ] }
```

Tebrikler! **_Node.js öğrenme yolunda önemli bir aşamayı geçtiniz._** Kendiniz için büyük iki adım atmış oldunuz bile. Ancak durmak yok daha yeni başladık. Bir sonraki derste programınızın akışını yönlendirmenizi sağlayacak [operatörleri](https://endrcn.dev/nodejs/operators/) öğrenerek gücümüze güç katacağız.
