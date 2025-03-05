---
title: "Node.js - Events"
date: "2022-01-12"
layout: post
author: endrcn
categories: [NodeJS]
---

Node.js'in yapısına kısaca değindiğimiz [Asenkron Fonksiyonlar](https://endrcn.dev/nodejs/asynchronous-functions/) makalesinde de söylediğimiz gibi; Node.js Single Threaded çalışan ve bir Event Loop üzerinden işlemlerini yürüten bir Javascript run-time environment'tir. Events, bu yapıyı daha iyi kavramamızı sağlayacak bir modüldür.

Javascript, Olay Yönelimli (Event-Driven) bir programlama dili olduğundan olaylar(events) sıkça kullanılan yapılardır. Events kavramını bir cümleyle özetlemek istersek, bir olay oluştuğunda tetiklenen ve bir dinleyici(listener) tarafından yakalanarak ilgili fonksiyonların çalışmasını sağlayan yapıya verilen isimdir. "events" modülü built-in bir modüldür bu nedenle herhangi bir kurulum gerektirmez.

## Nasıl Kullanılır?

EventEmitter'i kullanmak için öncelikle import etmemiz gerekir. Bunun için _events.js_ adında bir dosya oluşturup, [destructuring assignment](https://endrcn.dev/nodejs/destructuring/) kullanarak events modülü içindeki EventEmitter [sınıfını](https://endrcn.dev/nodejs/classes/) import edelim. Ardından bu sınıfa ait bir nesne üretelim.

```javascript
// events.js
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();
```

Şimdiyse olay dinleyici(event listener) tanımı yapalım. Event Listener, bir olay gerçekleştiğinde bu olayı yakalayıp gerekli işlemleri yapmamızı sağlayan yapılardır. Tanımlamanın iki yöntemi var. Birincisi on() metodu kullanılarak yapılan tanım.

```javascript
// events.js
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();

eventEmitter.on("event", (data) => {
    console.log("Message is handled", data);
});
```

İkinci yöntem ise addListener metodudur.

```javascript
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();

eventEmitter.on("event", (data) => {
    console.log("Event is handled", data);
});

eventEmitter.addListener("message", (data) => {
    console.log("Message is handled", data);
});
```

İki metodun da aldığı temel iki parametre var. İlk parametre dinlenecek olayın adı (eventName), ikincisi ise olay gerçekleştiğinde çalışacak fonksiyondur.

![_config.yml]({{ site.baseurl }}/assets/images/addlistener.png)

Bir olay olduğundaysa emit() metodu ile tetiklemeyi gerçekleştirebiliriz. Uygulamamıza emit metotlarını da ekleyelim.

```javascript
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();

eventEmitter.on("event", (data) => {
    console.log("Event is handled", data);
});

eventEmitter.addListener("message", (data) => {
    console.log("Message is handled", data);
});

eventEmitter.emit("message", "ender", "can", 30);
eventEmitter.emit("event", { firstName: "ender", lastName: "can", age: 30 });
```

Bu uygulamayı çalıştırdığımızda çıktı aşağıdaki gibi olacaktır.

![_config.yml]({{ site.baseurl }}/assets/images/event_output.png)

**Not:** Bir eventEmitter nesnesi üzerinden tanımlanmış bir listener, sadece aynı nesneden tetiklenen emit leri yakalar. Bu nedenle [referansı](https://endrcn.dev/nodejs/classes/) kaybetmemek önemlidir.

_event_ isimli listener, gönderdiğimiz objenin tamamını ekrana basabilmişken, _message_ isimli listener ise sadece "ender" çıktısını üretti. Bunun sebebi, emit ile gönderilen parametre sayısı ile listener fonksiyonundaki parametre sayısının farklı olması. Kısaca emit() metodu ile kaç parametre gönderiliyorsa listener fonksiyonunda da o kadar parametre alınmalıdır. _message_ event listener tanımını aşağıdaki şekilde güncellediğimizde tüm parametreleri alabiliriz.

```javascript
eventEmitter.addListener("message", (firstName, lastname, age) => {
    console.log("Message is handled", firstName, lastname, age);
});
```

Ayrıca destructuring ile gönderilen tüm parametreleri tek değişkende de toplayabiliriz. Bu değişken bir dizi olacaktır. Nasıl tanımlayacağımızı görelim:

```javascript
eventEmitter.addListener("message", (...args) => {
    console.log("Message is handled", args);
});
```

Kodun son hali ve çıktısı aşağıdaki gibi olacaktır.

```javascript
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();

eventEmitter.on("event", (data) => {
    console.log("Event is handled", data);
});

eventEmitter.addListener("message", (...args) => {
    console.log("Message is handled", args);
});

eventEmitter.emit("message", "ender", "can", 30);
eventEmitter.emit("event", { firstName: "ender", lastName: "can", age: 30 });
```

![_config.yml]({{ site.baseurl }}/assets/images/event_output2.png)

**Not:** Aynı listener'dan birden fazla oluşturabiliriz. Bu durumda ilgili event tetiklendiğinde(emit) iki listener'da çalışacaktır.

Temel hatlarıyla bir olayın(event) nasıl tetiklendiğini ve nasıl dinlendiğini gördük. Biraz daha detaylara inelim.

## Listener İşlemleri

Şimdiye kadar gördüğümüz on() ve addListener() metodları, tanımlandığı isimle tetiklenen tüm olayları dinler. Peki ya bir olayı sadece bir kez dinlemek ve bir kez çalıştırmak istersek? Bu durumda EventEmitter sınıfının once() metoduna ihtiyaç duyarız. Tanımlama biçimi on() ve addEventListener() metotlarıyla birebir aynıdır.

```javascript
eventEmitter.once("eventOnce", (...args) => {
    console.log("eventOnce is handled", args);
});
```

Test edebilmek için her bir event için ikişer kez emit() yapalım. Son durumda uygulamamız ve çıktımız:

```javascript
const { EventEmitter } = require("events");
const eventEmitter = new EventEmitter();

eventEmitter.on("event", (data) => {
    console.log("Event is handled", data);
});

eventEmitter.addListener("message", (...args) => {
    console.log("Message is handled", args);
});

eventEmitter.once("eventOnce", (...args) => {
    console.log("eventOnce is handled", args);
});

eventEmitter.emit("message", "ender", "can", 30);
eventEmitter.emit("message", "data");
eventEmitter.emit("event", { firstName: "ender", lastName: "can", age: 30 });
eventEmitter.emit("event", { firstName: "harika", lastName: "can", age: 30 });
eventEmitter.emit("eventOnce", { firstName: "ender", lastName: "can", age: 30 });
eventEmitter.emit("eventOnce", { firstName: "ender", lastName: "can", age: 30 });
```

![_config.yml]({{ site.baseurl }}/assets/images/event_once.png)

Uygulamanın 20. ve 21. satırlarında _eventOnce_ isimli olay iki kez tetiklenmesine rağmen listener sadece birini ekrana bastı. Çünkü _once()_ metodu, ilk tetiklemeden itibaren dinlemeyi durdurur.

EventEmitter sınıfına ait farklı metotlar da mevcuttur. Ben burada en sık kullandıklarımıza değindim. Tüm metotlara ve açıklamalarına [Node.js dokümantasyonundan](https://nodejs.org/api/events.html#events) erişebilirsiniz. Node.js'in önemli özelliklerinden birini daha uygulamış olduk. İyi çalışmalar dilerim :)
