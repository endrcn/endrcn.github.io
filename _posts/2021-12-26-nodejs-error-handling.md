---
title: "Node.js - Hata Yakalama (Error Handling)"
date: "2021-12-26"
layout: post
author: endrcn
categories: [NodeJS]
tags: [error handling, hata yakalama,node.js,nodejs, try-catch]
---

Hata yakalama, projelerin önemli bir parçasıdır. Node.js'te bir hata oluşması durumunda process çöker ve bu da uygulamamızın kapandığı anlamına gelir. Javascript'te fırlatılan hataların tipleri vardır. Bu tipleri bir tabloda inceleyelim.

<table><tbody><tr><td>Hata</td><td>Açıklama</td></tr><tr><td>SyntaxError</td><td>Yazılan kodda bir hata var. Söz dizimi hatası.</td></tr><tr><td>URIError</td><td>encodeURI() fonksiyonu hata verirse fırlatılır.</td></tr><tr><td>RangeError</td><td>Sınır hatası.</td></tr><tr><td>TypeError</td><td>Tür hatası.</td></tr><tr><td>ReferenceError</td><td>Referans hatası.</td></tr></tbody></table>

JS Hata Türleri

Eğer bir hata fırlatıldığında yakalanması ve hata durumunda projede yapılacak işlemler gerçekleştirilmelidir. Bu işlemlere örnek olarak kullanıcıya bilgi mesajı gösterilmesi verilebilir. Böylece kullanıcıyı hata durumunda yönlendirebilir ve kullanım tecrübesini artırabiliriz. Ayrıca daha önce söylediğim gibi, uygulamanın çökmesinin önüne geçebiliriz.

## Try-Catch

Hata yakalamak için kullandığımız bloklar **try-catch** bloklarıdır.

```javascript
// Syntax
try {
    // Codes
} catch (err) {
    console.error(err);
}
```

Hata oluşabileceğini düşündüğümüz kodlarımızı try bloğunda yazmalıyız. Eğer **try** bloğunda yazdığımız kodlarımızda bir hata fırlatılırsa, hatanın oluştuğu satırdan sonraki satırlar çalıştırılmadan **catch** bloğuna atlanır.

```javascript
try {
    console.log("Try Bloğu")
    console.log(ender);
    console.log("Hatadan sonraki kod");
} catch (err) {
    console.log("Catch Bloğu")
    console.error(err);
}
/*
Output:
Try Bloğu
Catch Bloğu
ReferenceError: ender is not defined
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day10 - ErrorHandling/error_handling.js:3:17)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
*/
```

Yukarıdaki örnekte "_Hatadan sonraki kod_" mesajı ekrana basılmadan direkt "_Catch Bloğu_" ekrana basıldı. Hata tipi de **ReferenceError** olarak gönderildi. Bunun sebebi, **ender** değişkeninin tanımlı olmamasıdır.

## Throw

**throw** anahtar kelimesi, herhangi bir durumda hata fırlatmamızı sağlar. Böylece kendi hata yönetimimizi yapabiliriz.

// Syntax
throw new Error();

**throw** ile hata fırlattığımızda, aynı sistemin hata fırlatmasında olduğu gibi uygulama çöker. Bu nedenle mutlaka **try-catch** blokları içinde kullanılmalıdır. throw kullanımına örnek olması açısından, alınan parametrelerin kontrolünü yapıp, beklenenden farklı parametre gelmesi durumunda hata fırlatan bir [fonksiyon](https://endrcn.dev/nodejs-functions/) yazalım.

```javascript
function checkParameterType(number, str) {
    if (typeof number != "number") throw new Error("number parameter type must be number");
    if (typeof str != "string") throw new Error("str parameter type must be String");

    return true;
}

try {
    let result = checkParameterType(5, 2);
    console.log("checkParameterType Result:", result);
} catch (err) {
    console.error(err);
}
/*
Output:
Error: str parameter type must be String
    at checkParameterType (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day10 - ErrorHandling/error_handling.js:14:39)
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day10 - ErrorHandling/error_handling.js:20:18)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
*/
```

Hata fırlatmadığı durumda da çıktı aşağıdaki gibi olacaktır.

```javascript
try {
    let result = checkParameterType(5, "2");
    console.log("checkParameterType Result:", result);
} catch (err) {
    console.error(err);
}
/*
Output: checkParameterType Result: true
*/
```

## Finally

**try bloğu**; hata verebilecek kod parçasını, **catch bloğu**; hata fırlatıldığında yapılacak işlemin kod parçasını, **finally bloğu** ise her durumda çalışacak kod parçasını barındırır. finally, catch bloğunun ardından tanımlanır ve kullanılır.

```javascript
// Syntax
try {
   // Codes
} catch (err) {
   // Error handling
} finally {
   // Other Codes
}
```

Finally bloğunu kullanmasak da kod çalışacaksa neden bu bloğa ihtiyacımız var ki diyebiliriz. try-catch bloğunun içinde return ile dönüş yapılsa dahi finally bloğu çalışacaktır. Ancak dışarda yazılan diğer kodlar çalışmaz.

```javascript
try {
    console.log(ender);
    return;
} catch (err) {
    console.error(err);
    return;
} finally {
    console.log("finally")
}

console.log("out of scope");
/*
Output:
ReferenceError: ender is not defined
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day10 - ErrorHandling/error_handling.js:34:17)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
finally
*/
```

Yukarıdaki kod örneğinin çıktısında "**finally**" mesajı yer almasına rağmen, "**out of scope**" mesajı yer almıyor. Bunun nedeni catch bloğunda **return** ile dönülmesindendir. Finally bloğu try ve catch bloğunda ne olursa olsun mutlaka çalışır. Bir dosyayı okumak için açtığımızda kapatmak ya da DB bağlantısını yaptıktan sonra kapatmak için finally bloğunu kullanabiliriz.

## Error Nesnesi

Node.js'te hatalar için kullanılan sınıf, ön tanımlı olarak gelen Error sınıfıdır. Bir hata fırlatıldığındaysa Error sınıfının bir nesnesi oluşmuş demektir. Bir Error nesnesini ekrana bastığımızda; hata adı, mesajı ve stack bilgisini görürüz. Bu alanlara ayrı ayrı da erişebiliriz.

```javascript
try {
    console.log(ender)
} catch (err) {
    console.error("ERR Name:",err.name);
    console.error("ERR Message:",err.message);
    console.error("ERR Stack:",err.stack);
}
/*
Output:
ERR Name: ReferenceError

ERR Message: ender is not defined

ERR Stack: ReferenceError: ender is not defined
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day10 - ErrorHandling/error_handling.js:34:17)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
*/
```

[Node.js Temelleri - Kalıtım (Inheritance)](https://endrcn.dev/nodejs-inheritance/) makalemizde örnek olarak gösterdiğimiz gibi, Error sınıfından alt sınıflar oluşturarak projemize özel hatalar oluşturabiliriz. Aynı örneği burada da görelim.

```javascript
class CustomError extends Error {
 
    constructor (code, message, description) {
        super(`{"code": ${code}, "message": "${message}", "description": "${description}"}`);
        this.code = code;
        this.message = message;
        this.description = description;
    }
 
}
```

Kullanımı:

```javascript
throw new CustomError(404, "Not Found", "Page Not Found");
/*
Output:
CustomError: Not Found
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day9 - Interitance/inheritance.js:64:7)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47 {
  code: 404,
  description: 'Page Not Found'
*/
```

Hatalar ve hata yakalama konusunu tamamlamış olduk. Node.js ya da herhangi bir programlama dilinde hata yakalama konusunu bilmek oldukça önemlidir. Bu önemli konuyu tamamlayarak yolunuza emin adımlarla devam ettiğiniz için kutlarım :)
