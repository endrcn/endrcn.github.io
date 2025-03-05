---
title: "Node.js - Built-in Methods"
date: "2021-12-29"
layout: post
author: endrcn
categories: [NodeJS]
tags: [js modules,nodejs modülleri,node.js modules, built-in modules]
---

Şimdiye kadar makalelerimizde genelde tüm programlama dillerinde var olan ve küçük farklar olsa da kullanımları oldukça benzeyen yapılardan bahsettim. Bu makalemizle birlikte artık daha çok Node.js'e özel sık kullanılan metotlar ve onların kullanımları hakkında bilgi vermeye çalışacağım.

## Genel Metotlar

Bir Node.js projesinde herhangi bir yerde erişebildiğimiz global olarak tanımlanmış metotlardır. Aşağıda bu metotları tanıyalım.

### parseFloat(String)

String tanımlanmış sayısal bir veriyi Number tipine dönüştürür. Dönüş değeri number tipinde ondalıklı bir sayıdır.

```javascript
parseFloat("11.12"); // Output: 11.12
console.log(typeof parseFloat("11.12"), parseFloat("11.12")) // Output: number 11.12
```

Eğer değer sayısal bir ifade ile başlamıyorsa içermiyorsa dönüş değeri NaN(Not a Number) olacaktır.

```javascript
parseFloat("ender12") // Output: NaN
```

Eğer değer sayısal bir ifade ile başlıyorsa dönüş değeri başlangıçtaki sayısal değerdir.

```javascript
parseFloat("12ender") // Output: 12
```

### parseInt(String)

parseFloat metoduna oldukça benzer. Tek farkı dönüş değerinin ondalıklı değil tam sayı olmasıdır.

```javascript
parseInt("11.12"); // Output: 11
console.log(typeof parseInt("11.12"), parseInt("11.12")) // Output: number 11
```

### isNaN(Number)

**NaN**, Not a Number ifadesinin kısaltmasıdır ve number tipindedir.

```javascript
console.log(typeof NaN); // Output: number
```

Eğer isNaN() fonksiyonuna verilen parametre number değilse sonuç **true**, aksi halde **false** dönecektir. Bu değer tamamen sayısal ifade içeren bir string olsa dahi sonuç **false** dönecektir.

```javascript
console.log(isNaN(123)) // Output: true
console.log(isNaN("123")) // Output: false
```

## JSON Methods

JSON, açılımı **J**ava**S**cript **O**bject **N**otations. Object tipinin yapısı JSON'dur. Javascript'te oldukça sık kullanılan bir yapıdır. JSON sınıfına ait iki metot vardır.

### JSON.stringify(JSONObject)

Bir JSON objesini string haline dönüştürmek için kullanılır.

```javascript
var user = {
    firstName: "Ender",
    lastName: "CAN",
    age: 30
}

console.log(JSON.stringify(user));
/*
Output:
{"firstName":"Ender","lastName":"CAN","age":30}
*/
```

stringify metodunun opsiyonel iki parametresi daha vardır. Bunlardan ilki **replacer**, ikincisi **space** parametresidir. Öncelikle space parametresinden başlayalım. Bir JSON objesini string'e dönüştürürken eğer sadece ilk parametreyi kullanırsak, dönüşen string tek satırda oluşacaktır. Eğer **space** parametresini kullanırsak oluşan string belirtilen boşluk sayısına göre daha düzenli görünecektir. space parametresine number yerine string bir ifade de girebiliriz. Bu durumda boşluk yerine parametrede verilen string değeri yazılır.

```javascript
console.log(JSON.stringify(user, null, 4));
/*
Output:
{
    "firstName": "Ender",
    "lastName": "CAN",
    "age": 30
}
*/
console.log(JSON.stringify(user, null, 2));
/*
Output:
{
  "firstName": "Ender",
  "lastName": "CAN",
  "age": 30
}
*/
console.log(JSON.stringify(user, null, "ENDER"));
/*
Output:
{
ENDER"firstName": "Ender",
ENDER"lastName": "CAN",
ENDER"age": 30
}
*/
```

**replacer** parametresi, bir fonksiyon alır. Bu fonksiyonda yapılan işlemler string'e dönüştürülecek objeyi etkiler ve string bu dönüşümlerden sonra oluşur. Örneğin; bir fonksiyon tanımlayarak user objesindeki firstName ve lastName alanlarının karakterlerini küçük harf yapalım.

```javascript
let result = JSON.stringify(user, (key, value) => {
    if (key == "firstName" || key == "lastName") {
        value = value.toLowerCase();
    }
    return value;
});

console.log(result);
/*
Output:
{"firstName":"ender","lastName":"can","age":30}
*/
```

Yukarıdaki örnekte [arrow function](https://endrcn.dev/nodejs/functions/#Arrow_Functions) kullanarak bir _**replacer**_ fonksiyonu oluşturduk ve bu fonksiyonda bir koşul tanımlayarak dönen field'larda eğer key değeri firstName ya da lastName ise toLowerCase() fonksiyonu -bu fonksiyona String metotları altında değineceğiz- ile tüm harflerini küçülttük.

### JSON.parse(String)

JSON.parse metodu, JSON formatında yazılmış bir string değerini parametre olarak alır (örn: JSON.stringify çıktısı). Dönüş değeri JSON objesidir.

```javascript
let jsonString = '{"firstName":"ender","lastName":"can","age":30}';
let jsonObj = JSON.parse(jsonString);
console.log(typeof jsonObj); // Output: object
console.log(jsonObj);
/*
Output:
{ firstName: 'ender', lastName: 'can', age: 30 }
*/
```

Eğer parametre olarak verilen string değeri JSON formatına uygun bir veri içermiyorsa [SyntaxError](https://endrcn.dev/nodejs/error-handling/) hatası fırlatılır. Bu nedenle dönüşümlerin [try-catch](https://endrcn.dev/nodejs/error-handling/) bloklarının kullanılması uygulamanın çökmesini engeller.

## Array Methods

Dizi(Array) sınıfına ait metotlar, uygulama geliştirirken çok sık başvuracağımız metotlardır. Tüm metotları tek tek açıklamak gibi bir düşüncede değilim. En sık kullanılanlardan bahsedelim. Aşağıdaki metotların tamamı bir dizi değişkeni üzerinde çalışır, başlıklardaki **array** ifadesi, array tipinde tanımlanmış bir değişkeni ifade eder.

### array.find(function)

Bir dizi içinde bir bilgi aramak için kullanılır. Dönüş değeri; sonuç bulunursa bulunan eleman, sonuç bulunamazsa **_null_**'dur. Aldığı parametre ise bir fonksiyondur. Bu fonksiyon, dizinin her bir elemanı için çağrılır.

```javascript
var arr = [1, 2, "ender", 4, 5];
let result = arr.find((item) => {
    return item == "ender";
})

console.log(result); // Output: ender
```

Yukarıdaki örnekte "ender" değerine sahip dizi elemanı find metoduyla arandı. **find()** metoduna verilen fonksiyon parametresinin true dönmesi, elemanın bulunduğunu gösterir. find() metodu, dizinin başından itibaren aramaya başlar ve ilk eşleşmede aramayı bitirir. Elemanları obje olan bir dizide aramanın nasıl yapılacağını da görelim.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var searchValue = "Leyla";
let result = arr.find((item) => {
    return item.name == searchValue;
});

console.log(result); // Output: { name: 'Leyla', score: 8 }
```

Bu kez item değeri bir object olacağı için eşleşmeyi objenin "name" alanında arıyoruz. Buradaki karşılaştırmalar farklı [operatörlerle](https://endrcn.dev/nodejs/operators/#Karsilastirma_Operatorleri) de yapılabilir.

### array.findIndex(function)

**findIndex()** metodu, find() metodu ile birebir aynı yapıda çalışır. find() ile aralarındaki fark ise, dönüş değerinin elemanın kendisi değil, dizideki index değeri olmasıdır. Eğer eleman bulunamazsa dönüş değeri **-1** olur.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var searchValue = "Leyla";
let result = arr.findIndex((item) => {
    return item.name == searchValue;
});

console.log(result); // Output: 3
```

### array.includes(value)

includes() metodu, dizi içinde eleman arama metodudur. Parametre olarak bir değer/değişken alır. Bu değere eşit olan veri bulunursa **_true_**, bulunamazsa **_false_** döner.

```javascript
var arr = [1, 2, "ender", 4, 5];
let result = arr.includes("ender")

console.log(result); // Output: true
```

includes() metodu ikinci bir opsiyonel parametre alır. Bu parametre de aramaya kaçıncı index'ten başlanacağını belirtir. Yukarıdaki örneği bir de start parametresini verip denediğimizde sonuç **_false_** olacaktır.

```javascript
var arr = [1, 2, "ender", 4, 5];
let result = arr.includes("ender", 3)

console.log(result); // Output: false
```

### array.indexOf(value)

**indexOf**() metodu, dizi içinde eleman arama metodudur. Parametre olarak bir değer/değişken alır. Bu değere eşit olan veri bulunursa bulunan ilk değerin index'i, bulunamazsa **_-1_** döner.

```javascript
var arr = [1, 2, "ender", 4, 5];
let result = arr.indexOf("ender")

console.log(result); // Output: 2
```

**indexOf**() metodu ikinci bir opsiyonel parametre alır. Bu parametre de aramaya kaçıncı index'ten başlanacağını belirtir. Yukarıdaki örneği bir de start parametresini verip denediğimizde sonuç **_-1_** olacaktır.

```javascript
var arr = [1, 2, "ender", 4, 5];
let result = arr.indexOf("ender", 3)

console.log(result); // Output: -1
```

### array.every(function)

**every**() metodu, dizinin tüm elemanlarının belirtilen koşula uygun olup olmadığını kontrol eder. Eğer elemanların tamamı koşula uygunsa **_true_**, herhangi biri koşula uygun değilse **_false_** döner.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var score = 6;
let result = arr.every((item) => {
    return item.score > score;
});

console.log(result); // Output: false
```

### array.filter(function)

filter() metodu, dizi içinde sadece bir eleman değil, birden fazla eleman aramak için kullanılır. Verdiğimiz koşula uygun olan elemanları ayrı bir dizi oluşturarak döner. Eğer koşula uygun eleman bulamazsa boş dizi döner. Skoru 6'dan büyük olan elemanları filtreleyelim.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var score = 6;
let result = arr.filter((item) => {
    return item.score > score;
});

console.log(result);
/*
Output:
[
  { name: 'Can', score: 10 },
  { name: 'Elif', score: 7 },
  { name: 'Leyla', score: 8 }
]
*/
```

### array.forEach(function)

**forEach**() metodu, bir döngü metodudur. Dizinin her bir elemanı için parametre olarak aldığı fonksiyonu çalıştırır. Herhangi bir dönüş değeri yoktur. [for...in](https://endrcn.dev/nodejs/loops/) döngüsünün metotlaştırılmış hali olarak düşünebiliriz.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
let result = arr.forEach((item) => {
    console.log(item.name+" - "+item.score);
});

/*
Output:
Ender - 5
Can - 10
Elif - 7
Leyla - 8
*/
```

### array.map(function)

**map()** metodu, dizi içindeki elemanları farklılaştırmak için kullanılır. Dönüş değeri yeni bir dizidir. Örneğin yukarıdaki obje dizisinin name field'larından oluşan yeni bir dizi oluşturmak istersek aşağıdaki gibi bir kod yazmamız gerekir.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var score = 6;
let result = arr.map((item) => {
    return item.name;
});

console.log(result);
/*
Output:
Ender
Can
Elif
Leyla
*/
```

### array.reduce(function, initialValue)

Dizinin elemanları üzerinde işlemler yapıp, sonuçta sadece bir değer döner. Bu değer, genelde fonksiyonun ikinci parametresi üzerinden yönetilir. Örneğin, yukarıda kullandığımız obje dizisindeki skorların toplamına ihtiyacımız olsun.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];
var initialScore = 0;
let result = arr.reduce((total, item) => {
    return total + item.score;
}, initialScore);

console.log(result); // Output: 30
```

İlk parametre ile verilen fonksiyon, diğer metotlardan farklı olarak ilk parametresi, initialValue değerinin kümülatif toplamını verir. Yani her bir eleman dönüşü sonucunda yapılan hesaplamanın sonucu, bir sonraki eleman için çağrılan fonksiyonun ilk parametresi olarak gönderilir. Böylece tüm elemanlar için yapılan kümülatif hesaplamaya sahip oluruz. Tüm döngü tamamlandığında da dönüş değeri bu değerdir.

### array.sort(function)

**sort**() metodu, dizi elemanlarını **kendi içinde** sıralar. Fonksiyon parametresi alır ama zorunlu değildir. Eğer bir parametre verilmeden kullanılırsa, kullanıldığı diziyi küçükten büyüğe(a->z) şeklinde sıralar.

```javascript
var arr = [4,3,6,3,7,6,5,9,8,2,1];
arr.sort();
console.log(arr); // Output: [ 1, 2, 3, 3, 4, 5, 6, 6, 7, 8, 9 ]
```

sort() metoduna bir parametre olarak fonksiyon tanımlanabilir. Tanımlanan fonksiyon, dizinin elemanlarını ikili şekilde gezer ve bu elemanları parametre olarak verir.

```javascript
var arr = [4,3,6,3,7,6,5,9,8,2,1];
arr.sort((a, b) => { return a - b});
console.log(arr); // Output: [ 1, 2, 3, 3, 4, 5, 6, 6, 7, 8, 9 ]
```

Fonksiyonun dönüş değerine göre de yer değiştirip değiştirmeyeceğine karar verir.

Dönüş değerleri:

- **pozitif** ise; ilk değer ikinciden büyük olarak işaretlenir.
- **negatif** ise; ilk değer ikinciden küçük olarak işaretlenir.
- **0** ise; ilk değer ile ikinci değer eşit olarak işaretlenir.

Yukarıdaki örnekten yola çıkarsak, a=ilk değer, b=ikinci değerdir. Eğer diziyi küçükten büyüğe doğru sıralamak istersek dönüş değerini **a-b** olarak belirlememiz gerekir. Eğer büyükten küçüğe sıralamak istersek dönüş değerini **b-a** olarak belirlemeliyiz.

```javascript
var arr = [4,3,6,3,7,6,5,9,8,2,1];
arr.sort((a, b) => { return b - a});
console.log(arr); // Output: [ 9, 8, 7, 6, 6, 5, 4, 3, 3, 2, 1 ]
```

### array.splice(index, howmany, item1, ..., itemN)

**splice()** metodu, bir diziye eleman eklemek ya da silmek için kullanılır. Aldığı parametreler;

- **index** - Ekleme/Silmeye hangi index'ten başlanacağını belirtir.
- **howmany** - Silinecek eleman sayısını belirtir. Eğer eleman silmek istemiyorsak 0 olarak vermeliyiz.
- **item1...N** - Eklenecek elemanlar. Aralarına virgül konularak yazılmalı.

Örneğin, obje dizimizdeki Can ile Elif arasına yeni bir eleman ekleyelim.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];

arr.splice(2, 0, {name: "Sena", score: 2});

console.log(arr);
/*
Output:
[
  { name: 'Ender', score: 5 },
  { name: 'Sena', score: 2 },
  { name: 'Can', score: 10 },
  { name: 'Elif', score: 7 },
  { name: 'Leyla', score: 8 }
]
*/
```

index parametresine 2 değerini verme sebebimiz, splice fonksiyonunun dizinin 2. index'inden itibaren eklemesini istememizdir. Dizide Elif isimli kişinin index'ini bildiğimizde bunu yapabiliyoruz. Ancak uygulama geliştirirken index değerini bilmemiz pek mümkün olmuyor. Bu durumda yukarıda gördüğümüz findIndex metodundan yararlanabiliriz.

```javascript
var arr = [
    { name: "Ender", score: 5 },
    { name: "Can", score: 10 },
    { name: "Elif", score: 7 },
    { name: "Leyla", score: 8 }
];

let index = arr.findIndex((item) => item.name == "Elif");
arr.splice(index, 0, {name: "Sena", score: 2});

console.log(arr);
/*
Output:
[
  { name: 'Ender', score: 5 },
  { name: 'Sena', score: 2 },
  { name: 'Can', score: 10 },
  { name: 'Elif', score: 7 },
  { name: 'Leyla', score: 8 }
]
*/
```

Yukarıdaki örnek, uygulama geliştirirken kullanacağımız yapıya çok daha yakın bir kod parçası oldu. Bir de [arrow function](https://endrcn.dev/nodejs/functions/#Arrow_Functions) yapısının en sade kullanımına bir örnek de görmüş olduk. Eğer herhangi bir işlem yapmadan direkt return değeri göndereceğimiz bir kod yazacaksak bu şekilde kullanabiliriz. Silme işlemine de bir örnek görelim. Eklediğimiz Sena isimli kişiyi bu kez silelim. Bu kez **howmany** parametresine 1 eleman silmek istediğimiz için 1 yazıyoruz.

```javascript
let index = arr.findIndex((item) => item.name == "Sena");
arr.splice(index, 1);

console.log(arr);
/*
Output:
[
  { name: 'Ender', score: 5 },
  { name: 'Can', score: 10 },
  { name: 'Elif', score: 7 },
  { name: 'Leyla', score: 8 }
]
*/
```

### array.join(seperator)

join() metodu, dizi elemanlarını verilen _seperator_ değeri ile ayrılacak şekilde birleştirir. Dönüş değeri String tipindedir.

```javascript
var arr = [1,2,3,"ender","can",true,3.14];
let strArr = arr.join();

console.log(strArr) // Output: 1,2,3,ender,can,true,3.14
strArr = arr.join(" -> ");
console.log(strArr) // Output: 1 -> 2 -> 3 -> ender -> can -> true -> 3.14
```

### Array.isArray(obj)

isArray() metodu, [static tanımlı](https://endrcn.dev/nodejs/classes/#Static_Method_ve_Static_Property) bir metottur. Bu nedenle direkt bir array değişken ile değil Array sınıfı ile çağrılır. Parametre ile verilen değişkenin bir dizi olup olmadığını söyler. Dönüş değeri [Boolean](https://endrcn.dev/nodejs/data-types/#Booleans)'dır. Eğer dizi ise **_true_**, değilse **_false_** döner.

```javascript
var arr = [1,2,3];
console.log(Array.isArray(arr)); // Output: true
var str = "1,2,3";
console.log(Array.isArray(str)); // Output: false
var obj = {"name":"Ender","age":30};
console.log(Array.isArray(obj)); // Output: false
```

## String Methods

String sınıfına ait metotlar, en sık kullandığımız bir başka metot grubudur. Özellikle en çok başvuracağımız metotları anlatmaya çalışacağım. Metot başlıklarında kullandığım **_str_** ifadesi, bir string değişkeni ifade eder.

### str.includes(value, _start_)

includes() metodu, bir string değer içinde farklı bir değeri aramak için kullanılır. Dönüş değeri [Boolean](https://endrcn.dev/nodejs/data-types/#Booleans)'dır. Eğer aranan değer string içinde bulunursa ise **_true_**, değilse **_false_** döner. Aramalar büyük-küçük harf duyarlıdır.

```javascript
var str = "Ender Can";

console.log(str.includes("Ender")); // Output: true
console.log(str.includes("ender")); // Output: false
```

includes() metodu opsiyonel olarak ikinci bir parametre alabilir. Bu parametre, aramaya kaçıncı karakterden başlanacağını belirlememizi sağlar. String'teki karakter index'leri dizilerde de olduğu gibi 0'dan başlar.

```javascript
var str = "Ender Can";
console.log(str.includes("Ender", 2)); // Output: false
```

### str.indexOf(value, _start_)

**indexOf()** metodu, **_includes()_** metodu ile aynı şekilde çalışır. Fark, dönüş değerinde oluşuyor. Dönüş değeri bulunan değerin başlangıç index değeridir. Eğer değer bulunamazsa **_-1_** döner.

```javascript
var str = "Ender Can";

console.log(str.indexOf("Can")); // Output: 6
console.log(str.indexOf("can")); // Output: -1
```

**Ek Bilgi:** **lastIndexOf()** metodu; indexOf() metodu ile aynı işlemi string'in sonundan başlayarak yapar. Böylece ilk eşleşmenin değil son eşleşmenin başlangıç index'ini döner.

### str.startsWith(value) & str.endsWith(value)

**startsWith()** metodu, bir string değişkenin parametre ile verilen değer ile başlayıp başlamadığını kontrol eder. Dönüş değeri Boolean'dır. Eğer string verilen parametre değeri ile başlıyorsa **_true_**, başlamıyorsa **_false_** döner. Kontrol büyük-küçük harf duyarlıdır.

```javascript
var str = "Ender Can";
```

console.log(str.startsWith("Ender")); // Output: true
console.log(str.startsWith("ender")); // Output: false
console.log(str.startsWith("Can")); // Output: false

**endsWith()** metodu ise string'in verilen değer ile bitip bitmediğini kontrol eder. Eğer string verilen parametre değeri ile bitiyorsa **_true_**, başlamıyorsa **_false_** döner. Kontrol büyük-küçük harf duyarlıdır.

```javascript
var str = "Ender Can";

console.log(str.endsWith("Ender")); // Output: false
console.log(str.endsWith("can")); // Output: false
console.log(str.endsWith("Can")); // Output: true
```

### str.match(regexp)

match() metodu, en sık kullandığım metotlardan biridir. İlerleyen zamanlarda Regular Expressions ile ilgili makaleler yayınladığımda bu metoda çok sık başvuracağız. match() metodu, parametre ile verilen RegExp ifadesini string içinde arar. Eğer ifadeyi string içinde bulursa bir dizi içinde bulduğu ifadeyi döner. Eğer bulamazsa **_null_** döner.

```javascript
var str = "Hello World";
let match = str.match(/World/g);
console.log(match); // Output: [ "World" ]

match = str.match(/world/gi);
console.log(match) // Output: [ "World" ]
```

Kullandığımız regex ifadesine girmeyeceğim. Regular Expressions bölümünde tüm detaylarıyla anlatacağım.

### str.replace(searchValue, newValue)

replace() metodu, en sık kullanılan bir diğer metottur. Bu metodun birden fazla kullanım biçimi var. Öncelikle aldığı parametrelerin alabileceği değerlerden bahsedelim.

- **_searchValue_**: String ya da Regexp tipinde veri alabilir.
- **_newValue_**: String ya da fonksiyon tipinde veri alabilir.

searchValue parametresi String tipinde verilirse, replace metodu String veride ilk karşılaştığı ifadeyi dönüştürür. Diğerlerine dokunmaz.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var replacedStr = str.replace("javascript", "JS");
console.log(replacedStr); // Output: JS öğrenirken javascript string metodlarından replace metodunu inceliyoruz
```

Yukarıdaki örnekte de sadece bir "javascript" ifadesini JS olarak değiştirdiğini görüyoruz. Eğer tüm ifadeleri değiştirmesini istiyorsak searchValue parametresini RegExp tipinde vermemiz ve _global flag(g)_ kullanmamız gerekir. Aynı örneği RegExp ile yapalım.

var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var replacedStr = str.replace(/javascript/g, "JS");
console.log(replacedStr); // Output: JS öğrenirken JS string metodlarından replace metodunu inceliyoruz

newValue parametresine verilen String verisinin değişim için kullanıldığını anladık. Bir de fonksiyon tipinde verilen newValue parametresinin nasıl çalıştığını inceleyelim. Genelde fonksiyon parametresi, basit bir dönüşüm yerine daha kapsamlı dönüşümler için kullanılır. Örneğin yukarıdaki örnekteki javascript kelimesinin tüm harflerini büyüten bir replace fonksiyonu yazalım.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var replacedStr = str.replace(/javascript/g, (match) => {
    return match.toUpperCase();
});
console.log(replacedStr); // Output: JAVASCRIPT öğrenirken JAVASCRIPT string metodlarından replace metodunu inceliyoruz
```

Fonksiyonun dönüş değeri, değiştirilecek ifadeyi temsil eder. Yukarıdaki örnekte de toUpperCase() metodu ile string'in tüm harflerini büyüttük ve fonksiyonun dönüş değeri olarak verdik.

Not: Arrow function tanımında eğer içeride işlem yapılmayıp direkt dönüş değeri verilecekse süslü parantezler ve return anahtar kelimesi kullanılmadan da tanım yapılabilir. Yukarıdaki kodu bir de bu şekilde yazalım.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var replacedStr = str.replace(/javascript/g, (match) => match.toUpperCase());
console.log(replacedStr); // Output: JAVASCRIPT öğrenirken JAVASCRIPT string metodlarından replace metodunu inceliyoruz
```

### str.substring(startIndex, _endIndex_)

**substring()** metodu, bir string verinin içinden bir parçayı almak için kullanılır. Hem başlangıç hem de bitiş için index değeri alır. İkinci parametre _opsiyoneldir_. Eğer ikinci parametre verilmezse başlangıç index'inden itibaren string sonuna kadar tüm parça döner.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var subStr = str.substring(0, "javascript".length);
console.log(subStr); // Output: javascript
```

Tabii her zaman bu kadar basit olmayacak. Genelde kodlama sırasında verinin nerede başladığını bilmeyiz. Bu durumda indexOf() metodu ile önce javascript ifadesinin nereden başladığını bulmamız gerekir.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var subStr = str.substring(str.indexOf("javascript"), "javascript".length);
console.log(subStr); // Output: string
```

Peki ya geçmiyorsa? Bu durumda indexOf metodu -1 dönecek ve substring fonksiyonunun çalışma koşullarında eğer startIndex 0'dan küçük verilmişse 0 olarak alması gerektiği yer aldığından dönüşüm 0. indisten başlayacaktır. Yazdığımız kod yanlış parçayı çıkaracaktır.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
var subStr = str.substring(str.indexOf("strng"), "string".length);
console.log(subStr); // Output: javasc
```

Çıktı olarak "string" ifadesini beklerken sonuç "javasc" oldu. Böyle durumlarla karşılaşmamak için öncelikle bir kontrol koymak yararımıza olacaktır.

```javascript
var str = "javascript öğrenirken javascript string metodlarından replace metodunu inceliyoruz";
let strIndex = str.indexOf("strng");
if (strIndex > -1) {
    var subStr = str.substring(str.indexOf("strng"), "string".length);
}
console.log(subStr); // Output: undefined
```

Sonucun **_undefined_** olmasının sebebi [Scope](https://endrcn.dev/nodejs/scopes/) erişimleri ile ilgilidir. subStr değişkeni if scope içinde tanımlandığı ve bu if'e girmediği için sonuç **_undefined_** çıktı.

### toUpperCase() & toLowerCase()

toUpperCase() ve toLowerCase() metotları, sırasıyla verilen string'in tüm harflerini büyütmeyi ya da küçültmeyi sağlar. Kullanımı basittir.

```javascript
var str = "hello World!";
var upperStr = str.toUpperCase();
console.log(upperStr); // Output: HELLO WORLD!
var lowerStr = upperStr.toLowerCase();
console.log(lowerStr); // Output: hello world!
```

### trim()

**trim()** metodu, string'in başındaki ve sonundaki boşluk karakterlerini temizler. Örnekte çıktıları tırnaklar içinde gösterelim ki fark anlaşılsın:

```javascript
var str = "     Hello World!     ";
console.log("'"+str+"'"); // Output: '     Hello World!     '
console.log("'"+str.trim()+"'"); // Output: 'Hello World!'
```

Eveeeeeet! String Metotlarının da sonuna geldik. Built-in Methods konusu yazmakla bitmez bir konu olduğu burada kesiyorum. En sık ihtiyaç duyulan metotlardan bahsetmeye çalıştım. Faydalı olacağına inanıyorum. Konuyu sonlandırmadan önce son bir bilgi daha vermek istiyorum.

Makaleleri yazarken örnekler için kullandığım Visual Studio Code'da(ya da başka bir IDE/Editor) otomatik tamamlama özelliği vardır. Bu özellik sayesinde değişkenler ya da sınıflar üzerinde kullanabileceğiniz metot/property'leri görebilirsiniz.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-02-at-14.40.31.png)

Metot Tamamlama Örneği

Aynı zamanda bir metodun signature(imzasını) yani alacağı parametreleri, tiplerini ve ne iş yaptığını görebilirsiniz.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-02-at-14.40.38.png)

Method Signature

Geniş kapsamlı bir konuyu burada noktalıyoruz. Javascript/Node.js yolculuğunuzda artık eliniz çok güçlendi. Artık temellerden daha ileri seviye konulara adım adım ilerliyoruz. Bir sonraki adımımız Promise yapıları ve Asenkron fonksiyonlar olacak.
