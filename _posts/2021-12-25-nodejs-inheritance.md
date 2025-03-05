---
title: "Node.js - Kalıtım (Inheritance)"
date: "2021-12-25"
layout: post
author: endrcn
categories: [NodeJS]
tags: [kalıtım,inheritance,nodejs,node.js,javascript]
---

Bir önceki [Node.js Temelleri - Sınıflar (Classes)](https://endrcn.dev/nodejs/classes/) makalemizde sınıf tanımlama, constructor, property, metodlar ve erişim belirteçlerinden bahsetmiştik. Bu yazımızdaysa hedefimiz kalıtım(inheritance) kavramına değinmek ve uygulamalı nasıl ve ne zaman kullanılacağını göstermektir.

**Kalıtım(Inheritance),** genel bir sınıftan daha özel bir sınıf türeterek, genel sınıfın özelliklerinin özel sınıfta da kullanılabilmesidir. Böylece kod tekrarından ve anlam karmaşalarından uzak, temiz ve anlaşılır bir kod geliştirebiliriz.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2021-12-26-at-00.33.16.png)

Kalıtım(Inheritance)

Bir sınıftan yeni bir sınıf türetmek için [sınıf tanımlama](https://endrcn.dev/nodejs/classes/#https://endrcn.dev/nodejs/classes/#Sinif_Tanimlama) aşamasında **extends** anahtar kelimesini kullanırız. Şekildeki gibi bir yapıyı oluşturmaya çalışalım. Öncelikle Person sınıfını oluşturalım.

```javascript
class Person {
   #firstName;
   #lastName;
   constructor(firstName, lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
   }

   get firstName(){return this.#firstName;}
   set firstName(val) {this.#firstName = val;}

   get lastName(){return this.#lastName;}
   set lastName(val) {this.#lastName = val;}

   getName() {
      return this.firstName + " " + this.lastName;
   }
}
```

Yukarıda standart bir sınıf oluşturduk. Buraya kadar anlayamadığınız bir yer olduysa [Node.js Temelleri - Sınıflar (Classes)](https://endrcn.dev/nodejs/classes/) makalemizi okumanızı öneririm.

Şimdi de oluşturduğumuz Person sınıfından bir Student sınıfı türetelim. Ardından da Student ve Person sınıflarından birer nesne tanımlayalım.

```javascript
class Student extends Person {}

let person = new Person("Ender", "CAN");
let student = new Student("Can", "ENDER");

console.log("Person Name:",person.getName()); // Output: Person Name: Ender CAN
console.log("Student Name:",student.getName()); // Output: Person Name: Can ENDER
```

Türetilen bir sınıf, üst sınıftaki property ve metotları da kendine alır. Yukarıdaki örnekte de Student sınıfında tanımlanmış bir getName() metodu olmamasına rağmen, nesnesi üzerinden getName() metodunu çağırabildik ve çıktı olarak alabildik. Aynı durum constructor ve property'ler için de geçerli. Ancak alt sınıfta tanımlanmış bir metoda üst sınıf erişmez. Eğer Person sınıfının nesnesinden Student altında tanımlanmış bir metoda erişmeye çalışırsak **TypeError** hatası alırız.

## Super

Üst sınıfta tanımlanmış metotlara alt sınıftan erişmek için **super** anahtar kelimesini kullanırız. Student sınıfına bir metot tanımlayarak genişletelim.

```javascript
class Student extends Person {
    introduceYourself() {
        return "Merhaba, ben "+ super.getName();
    }
}


let person = new Person("Ender", "CAN");
let student = new Student("Can", "ENDER");

console.log("Student Name:",student.introduceYourself()); // Output: Student: Merhaba, ben Can ENDER
console.log("Person Name:",person.introduceYourself()); // Output: TypeError: person.introduceYourself is not a function
```

Yukarıdaki örnekte introduceYourself metodunun içinde **super** anahtar kelimesini kullanarak getName() metodunu kullandık.

## Overriding

Overriding, üst sınıfta tanımlanmış metotları alt sınıfta yeniden tanımlamaya verilen isimdir. getName() metodunu Student sınıfında tekrar tanımlayalım.

```javascript
class Student extends Person {
    introduceYourself() {
        return "Merhaba, ben " + super.getName();
    }

    getName() {
        return "Name: " + super.lastName + " " + super.firstName;
    }
}

let person = new Person("Ender", "CAN");
let student = new Student("Can", "ENDER");

console.log("Student:", student.getName()); // Output: Student: Name: ENDER Can
console.log("Person:", person.getName()); // Output: Person Name: Ender CAN
```

*getName()* metodunu Student sınıfının nesnesinden çağırdığımızda ***override*** edilmiş hali çalışacaktır. Ancak Person sınıfının nesnesinden çağırdığımızda orijinal hali çalışacaktır. Override işlemi constructor için de aynı şekilde geçerlidir. Örneğin Student sınıfında öğrencinin okul ismini de alan bir constructor metodu oluşturalım.

```javascript
class Student extends Person {

    #schoolName;

    constructor(firstName, lastName, schoolName) {
        super(firstName, lastName);
        this.#schoolName = schoolName;
    }

    get schoolName() { return this.#schoolName; }

    introduceYourself() {
        return "Merhaba, ben " + super.getName();
    }

    getName() {
        return "Name: " + super.lastName + " " + super.firstName + " - " + this.schoolName;
    }
}
```

Student sınıfının constructor metodu içinde super() ile üst sınıfın constructor metodunu çağırarak aynı işlemleri tekrar yapmaktan kurtulduk. Artık öğrencinin yine adını ve soyadını aldığımız, ayrıca bir de okul ismini aldığımız bir constructor oluşturduk. Bundan böyle student nesnesini oluştururken yeni constructor'u kullanmalıyız.

let person = new Person("Ender", "CAN");
let student = new Student("Can", "ENDER", "YTÜ");

console.log("Student:", student.getName()); // Output: Student: Name: ENDER Can - YTÜ
console.log("Person:", person.getName()); // Output: Person Name: Ender CAN

Student sınıfının getName() metodunu güncelleyerek constructor ile aldığımız okul adını döndürdük. Tam bu noktada **this** ile **super** anahtar kelimelerinin kullanımlarına örnek yapmış olduk. this anahtar kelimesi, bulunduğumuz sınıfın nesnesini temsil ederken, **super** anahtar kelimesi, üst sınıfı temsil eder. Ancak, alt sınıftan üst sınıfın bir metoduna ya da property'ine erişmek için **this** de kullanabiliriz. Burada dikkat edilmesi gereken, **_override_** edilmiş bir metoda **this** ile erişirsek bulunduğumuz sınıftaki hali, **super** ile erişirsek üst sınıftaki hali çalışacaktır. Daha önce tanımladığımız introduceYourself metodumuzda getName metodunun **super** ile kullanımını, yeni tanımlayacağımız introduce metodunda da **this** ile kullanımını görelim.

```javascript
class Student extends Person {

    #schoolName;

    constructor(firstName, lastName, schoolName) {
        super(firstName, lastName);
        this.#schoolName = schoolName;
    }

    get schoolName() { return this.#schoolName; }

    introduceYourself() {
        return "Merhaba, ben " + super.getName();
    }

    introduce() {
        return "Merhaba, ben " + this.getName();
    }

    getName() {
        return "Name: " + super.lastName + " " + super.firstName + " - " + this.schoolName;
    }
}

let student = new Student("Can", "ENDER", "YTÜ");

console.log("introduceYourself - super:", student.introduceYourself()); // Output: introduceYourself - super: Merhaba, ben Can ENDER
console.log("introduce - this:", student.introduce()); // Output: introduce - this: Merhaba, ben Name: ENDER Can - YTÜ
```

Kalıtım, önemli ve sık kullandığımız bir konudur. Bu nedenle detaylarına hakim olmak bizim için önemlidir. Şimdiye kadar hep kendi oluşturduğumuz bir sınıftan farklı bir sınıf oluşturduk. Şimdiyse Node.js içinde tanımlı olan bir sınıftan yeni bir sınıf tanımlamayı görelim. Node.js'te ön tanımlı olarak gelen Error sınıfında, constructor sadece mesaj parametresi alır. Projemizde vereceğimiz hata mesajlarının formatının Error sınıfından farklı olarak daha detaylı olmasını istediğimizi düşünelim. Ancak standart Error yapısından da vazgeçmeyelim. Bu durumda CustomError adında bir sınıfı Error sınıfından türetebiliriz.

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

Yukarıdaki kod parçasını açıklamak gerekirse;

- extends anahtar kelimesini kullanarak Error sınıfından CustomError sınıfı türetildi.
- Error sınıfında sadece message parametresi alan constructor metodu override edilerek code, message ve description parametrelerini alması sağlandı.
- super() metodu ile Error sınıfının constructor'u çağrılarak alınan parametreler birleştirilerek tek parametre haline getirildi.
- Alınan parametreler, CustomError içinde public property'lere atandı.

Böylece Error sınıfının tüm özelliklerine sahip ancak mesajı istediğimiz formatta veren bir sınıfa sahip olduk. Artık hata mesajı gönderirken CustomError sınıfını kullanarak gönderebiliriz. Aşağıda Error ve CustomError sınıflarını kullanarak hata fırlatan kod parçalarını görelim ve çıktılarını gözlemleyelim.

```javascript
throw new Error("There is an error");
/*
Output:
Error: Not Found - Page Not Found
    at Object.<anonymous> (/Users/endrcn/PersonalProjects/nodejs_tutorial/Day9 - Interitance/inheritance.js:64:7)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
*/

CustomError örneği:

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

CustomError sınıfı kullanıldığında çıktıda message haricinde code ve description alanları da dönüyor.

Müthiş gidiyorsunuz! [Sınıflar](https://endrcn.dev/nodejs/classes/) ve **Kalıtım** konularını da tamamlayarak OOP dünyasına hızlı bir giriş yaptınız. Bundan böyle yazdığınız kodlar makineden uzaklaşıp insana yaklaşacak. Bir sonraki makalemizde burada ufak dokunduğumuz **hatalar ve hata yakalama** konularına değineceğiz.
