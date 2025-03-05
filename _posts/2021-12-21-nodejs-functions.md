---
title: "Node.js - Fonksiyonlar (Functions)"
date: "2021-12-21"
layout: post
author: endrcn
categories: [NodeJS]
tags: [fonksiyon,javascript fonksiyonlar, nodejs, node.js]
---

Bir uygulama geliştirirken her şeyi tek sayfada, alt alta yazmaya çalıştığınızı, bir matematiksel hesabı onlarca kez yaptığınızı, veritabanından aynı veriyi defalarca çektiğinizi bir düşünün. Bu şekilde yazılmış kodların bakımı da okunması da oldukça zor olurdu. İşte tam da burada devreye **fonksiyon** giriyor.

![_config.yml]({{ site.baseurl }}/images/functions.png)

Kaynak: studytonight.com

**Fonksiyonlar**, birçok yerde kullanacağınız kod parçalarını bir kez yazmanızı, ihtiyacınız olduğu zaman sadece tanımladığınız fonksiyon ismiyle çağırarak kod tekrarından kaçınmanızı sağlar.

## Fonksiyon Tanımlama

Fonksiyonlar tanımlanırken **function** anahtar kelimesi kullanılır. Bir fonksiyon tanımlanırken alacağı parametreler, bir dönüş değeri olup olmayacağı ve ismi belirlenir. Fonksiyon isimlerinde [değişken](https://endrcn.dev/nodejs/variables/) tanım kuralları aynen geçerlidir.

```javascript
// Syntax
function functionName (param1, param2, ...) {
    // Codes
}
```

Fonksiyonlar ikiye ayrılırlar. Dönüş değeri olanlar ve dönüş değeri olmayanlar. Bir fonksiyondan veri döndürmek için **return** anahtar kelimesi kullanılır. Eğer bir veri döndürülmeyecekse herhangi bir işlem yapmaya gerek yoktur. Dönüş değeri olan bir fonksiyon tanımı örneği olarak, girilen iki sayıyı toplayıp, toplamı dönen bir fonksiyon tanımlayalım.

```javascript
function add(num1, num2) {
    return num1 + num2;
}
```

Yukarıdaki örnekte **add**, fonksiyon ismi, **num1** ve **num2** parametreler ve **return num1 + num2** de dönüş değeridir. Fonksiyonların dönüş değeri herhangi bir [veri tipinde](https://endrcn.dev/nodejs/data-types/) olabilir.

Dönüş değeri olmayan(void) fonksiyonlar ise işlemi yapan ancak dönüşte bir veri üretmeyen fonksiyonlardır. Void fonksiyonlara da bir örnek yapalım. Örneğimizde ekrana log basan bir fonksiyon yazalım.

```javascript
function writeLog(logParam){
    console.log(logParam);
}
```

Fonksiyonların parametre alması zorunlu değildir. Hiç parametre almayan bir fonksiyon da tanımlayabiliriz.

```javascript
function methodWithoutParameter() {
    console.log("Parametre yok");
}
```

Bir fonksiyon tanımlandığında çalışmaz. Sadece bellekte yer tutar ve çalıştırılmayı bekler.

## Fonksiyon Çağırma

Tanımlanmış bir fonksiyonu çağırmak için ismi yazılır. Eğer parametre alıyorsa, parametreleri de verilir/verilmez. Yukarıda tanımladığımız fonksiyonları çağıralım.

```javascript
console.log(add(2,3)); // output: 5
writeLog("bir log yaz"); // output: bir log yaz
methodWithoutParameter(); // output: Parametre yok
```

Fonksiyonlar çağrılırken tanımda belirlenen parametrelerin aktarılması zorunlu değildir. Ancak bu durumda fonksiyonlar doğru çalışmayabilir.

```javascript
console.log(add()); // output: NaN
```

**Ek bilgi:** _NaN_ - Not a Number ifadesinin kısaltmasıdır.

## Parametre Aktarımı

Bir fonksiyon, **function** anahtar kelimesi, isim, parametreler ve dönüş değerinden oluşur. Parametreler, fonksiyonların yeniden kullanılabilir olmalarını sağlarlar. Böylece aynı kod parçasını farklı değerler için çalıştırarak farklı sonuçlar elde edebiliriz. Parametre aktarımı konusunda iki durum söz konusudur.

### Pass by Reference

Pass by Reference, fonksiyona bir parametre olarak verilen değişkenlerin memory'deki adresleri olarak gönderilmesini ifade eder. Javascript'te array ve object tipindeki değişkenler referans/adres olarak aktarılır.

Bir değişkenin referans değerinin gönderilmesi, eğer bu değişken fonksiyon içinde bir değişikliğe uğrarsa, fonksiyon dışında da değişir anlamını taşır. Bir örnekle gösterelim.

```javascript
// Pass by Reference Example
function changeReferencedValue(varObj) {
    varObj.name = "CAN";
    console.log("Fonksiyon içinde varObj", varObj);
}

var varObj = {
    name: "Ender"
}
console.log("Fonksiyon çalışmadan önce varObj=",varObj);
changeReferencedValue(varObj);
console.log("Fonksiyon çalıştıktan sonra varObj=",varObj);
/\*
Output:
Fonksiyon çalışmadan önce varObj= { name: 'Ender' }
Fonksiyon içinde varObj { name: 'CAN' }
Fonksiyon çalıştıktan sonra varObj= { name: 'CAN' } --> Değişti!
\*/
```

Fakat, eğer fonksiyon içinde parametreye yeni bir değer atarsak, yeni bir değişken gibi algılanır ve dışarıdan gönderilen değişkenle bağlantısı kopar. Bu nedenle de fonksiyon içindeki değişiklik dışarıdakini etkilemez.

```javascript
// Pass by Reference Example2
function changeReferencedValue2(varObj) {
    varObj = { name: "CAN" };
    console.log("Fonksiyon içinde varObj", varObj);
}

var varObj = {
    name: "Ender"
}
console.log("Fonksiyon çalışmadan önce varObj=", varObj);
changeReferencedValue2(varObj);
console.log("Fonksiyon çalıştıktan sonra varObj=", varObj);
/\*
Output: 
Fonksiyon çalışmadan önce varObj= { name: 'Ender' }
Fonksiyon içinde varObj { name: 'CAN' }
Fonksiyon çalıştıktan sonra varObj= { name: 'Ender' } --> Değişmedi!
\*/
```

### Pass by Value

Pass by Value, bir değişkenin referans değerinin değil de direkt değerinin aktarılması anlamına gelir. Javascript'te default parametre aktarımı pass by value'dir. Yani dışarıdan verilen değişkenin değeri, fonksiyonun argümanına kopyalanır.

```javascript
// Pass by Value Example
function changeParamValue(number) {
    number = 2;
    console.log("Fonksiyon içinde number =", number);
}

var number = 5;
console.log("Fonksiyon çalışmadan önce number=",number);
changeParamValue(number);
console.log("Fonksiyon çalıştıktan sonra number=",number);
/\*
Output:
Fonksiyon çalışmadan önce number= 5
Fonksiyon içinde number = 2
Fonksiyon çalıştıktan sonra number= 5 --> Değişmedi
\*/
```

### Arguments

Bir fonksiyon tanımlarken parametre sayısını bilemeyebiliriz. Bu gibi durumlarda **arguments** imdadımıza yetişir. Örneğin gönderilen tüm parametreleri toplayan bir fonksiyona ihtiyacımız var. Bu durumda aşağıdaki gibi bir tanım yapabiliriz.

```javascript
function sum(){
    let total = 0;
    for (let i=0;i<arguments.length;i++) {
        total += arguments\[i\];
    }
    return total;
}

let result = sum(2,3,4,5,6,7,8);
console.log(result) // output: 35
```

Yukarıdaki örnekte sum fonksiyonu hiç parametre almıyor gibi görünse de, javascript'te fonksiyon çağrılırken gönderilen parametreler **arguments** dizisi olarak saklanır. Böylece sınırsız parametre alabilen fonksiyonlara sahip olabiliriz.

## Fonksiyon Dönüş Yapıları

Fonksiyon tanımlama başlığı altında bir fonksiyonun temel dönüş yapılarından bahsetmiştik. Bu konuyu başlıklara ayırarak biraz daha irdeleyelim.

### Return

En temel fonksiyon dönüş yapısı **return** anahtar kelimesiyle yapılandır. Bir fonksiyonda yapılan işlemler sonucu ortaya çıkan değeri fonksiyonun çağrıldığı yere göndermek için kullanılır. Bunu bir örnek ile pekiştirelim.

```javascript
function square(number) {
    let result = number \* number;
    return result;
}

let num = 5;
let squareOfNumber = square(num);
console.log(squareOfNumber); // output: 25
```

Yukarıdaki örneği adım adım açıklayalım:

1. **square** adında bir fonksiyon tanımlandı, bu fonksiyon **number** adında bir parametre alıyor.
2. **num** adında bir değişken tanımlanıyor ve değeri **5** olarak veriliyor.
3. Tanımlanan square fonksiyonu, num değişkeni parametre olarak verilerek çağrılıyor ve sonucu **squareOfNumber** değişkenine atanıyor
4. Sonuç ekrana basılıyor.

### Void

Void fonksiyonlar, herhangi bir dönüş değeri olmayan fonksiyonlardır. Bu tip fonksiyonlar genelde bir işlem sonucunda dönüş değerinin önemsenmediği durumlarda tanımlanır.

### Callback

Bir fonksiyona parametre aktarırken sadece değişkenler kullanılmaz. Tanımladığımız bir fonksiyona parametre olarak farklı bir fonksiyon da verebiliriz. İşte parametre olarak verdiğimiz bu fonksiyonların genel isimlendirmesi **callback**'tir. Callback fonksiyonlar, parametre olarak verildikleri fonksiyonlardaki işlemler bittikten sonra çağrılan fonksiyonlardır. Bunun için bir örnek yapalım: Veritabanından müşterileri getiren bir fonksiyon tanımlayalım. Fonksiyon, müşterileri çektikten sonra bir callback ile dönüş sağlasın

```javascript
function getCustomers(query, cb) {
   let result = db.getFromDB(query, function(customers){
      // Some customer processes
      cb(customers);
   });
}

getCustomers({},function(customers){
    console.log(customers);
});
```

Yukarıdaki örneği satır satır açıklayalım

1. getCustomers adında bir fonksiyon tanımlandı. Bu fonksiyon query ve cb adında iki değişken alıyor. cb parametresi, callback fonksiyonumuzu aktaracağımız parametredir.
2. db'den getFromDB adında bir fonksiyonumuz olduğunu varsayıyoruz. Bu fonksiyon da aynı şekilde callback ile çalışan asenkron bir fonksiyondur.
3. getFromDB den aldığımız customers verisi, cb fonksiyonuna parametre verilerek çağrılıyor.
4. Tanımlanan getCustomers fonksiyonu çağrılıyor. İlk parametresine {} değeri veriliyor, ikinci parametresine de bir fonksiyon tanımlanıyor. Bu fonksiyon, callback fonksiyonudur.

4\. adımı biraz daha açalım. callback fonksiyonu, getCustomers fonksiyonunun içinden çağrılır ve parametresi de çağrıldığı yerde verilir. Fonksiyon çağrıldığında da tanımlandığı yerdeki log ekrana basılır.

## Arrow Functions

[Ecmascript 6](https://en.wikipedia.org/wiki/ECMAScript#6th_Edition_%E2%80%93_ECMAScript_2015) ile birlikte gelen bu arrow function'lar, adını tanımlanma biçiminden alırlar. Bir fonksiyon tanımlarken kullandığımız **function** anahtar kelimesi, arrow function tanımlarken kullanılmaz.

```javascript
// Syntax
const func = (param1, param2, ...) => {
   // Function area
}
// Function call
func(a,b,...)

Arrow function yapısının callback olarak kullanımı daha yaygındır. Yukarıdaki callback fonksiyon örneğini bir de arrow function ile yapalım.

function getCustomers(query, cb) {
   let result = db.getFromDB(query,(customers) => { // arrow function
      // Some customer processes
      cb(customers);
   });
}

getCustomers({},(customers) => { // arrow function
    console.log(customers);
});
```

## İç İçe Fonksiyonlar (Nested Functions)

Bir fonksiyon içinde başka bir fonksiyon tanımlanabilir. Bu durumda içerdeki fonksiyona dışarıdan erişilemez.

```javascript
// A nested function example
function getScore() {
  var num1 = 2,
      num2 = 3;

  function add() {
    return name + ' scored ' + (num1 + num2);
  }

  return add();
}

getScore(); // Returns "Chamakh scored 5"
```

Harikasınız! Fonksiyonlar konusunu tamamlayarak Node.js öğrenme serüveninizde BÜYÜK BİR ADIM attınız.

## Kaynakça

- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions?retiredLocale=tr#preservation\_of\_variables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions?retiredLocale=tr#preservation_of_variables)
- [https://flexiple.com/javascript-pass-by-reference-or-value/](https://flexiple.com/javascript-pass-by-reference-or-value/)
- [https://www.studytonight.com/javascript/javascript-functions](https://www.studytonight.com/javascript/javascript-functions)
