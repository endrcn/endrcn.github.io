---
title: "ExpressJS - Middleware Nasıl Çalışır?"
date: "2023-05-13"
layout: post
author: endrcn
categories: [NodeJS]
---

ExpressJS, Node.js ile web geliştirme yapan neredeyse herkesin hayatı boyunca en az bir kez kullandığı bir framework. Oldukça kolay adapte olabilmemizin yanı sıra, geliştirmeyi de çok kolaylaştırdığı için hızlıca popülerleşmiş ve neredeyse bir standart haline gelmiştir. Bugün Node.js öğrenmek istediğinizde karşınıza çıkacak olan ilk framework ExpressJS olacaktır.

Genel hatlarıyla ExpressJS, web projelerimizde endpoint oluşturmayı, uygulamanın bir portu dinleyerek gelen isteklere cevap verebilmesini kolaylaştıran bir framework olarak karşımıza çıkar. İşte bu isteklerin karşılanması konusunda Middleware dediğimiz yapılara oldukça fazla iş düşer. ExpressJS ile geliştirilen bir uygulamaya atılan istekler, genellikle ilgili endpoint'e gelmeden önce bazı kontrollerden geçmelidir. İşte tam da burada middleware dediğimiz yapılar devreye girer.

![_config.yml]({{ site.baseurl }}/assets/images/express_middlewares.png)

Bir middleware nasıl çalışır konusundan önce, ExpressJS'te bir route nasıl tanımlanır konusuna değinmemiz gerekir. Bu konu netleştiğinde zaten işin mantığını da kavramış oluyoruz. Aşağıda bir route nasıl tanımlanır görelim:

```javascript
var express = require('express');
var app = express();
// Route
app.use((req, res, next) => {
    // Do something
    next();
});
```

Yukarıdaki kodda `app.use()` olarak tanımlanan kısma `route` diyebiliriz. Aslında bu kısım aynı zamanda bir middleware tanımına örnektir. Burada dikkat çekmek istediğim kısım, middleware ile route arasında çok küçük farkların olduğu hatta çoğu zaman hiçbir farkın olmadığıdır. Şimdi `app.use` 'nin anatomisini inceleyelim:

`app.use`, temelde iki parametre alır. İlk parametre endpoint adı, ikinci parametresi callback fonksiyonu. Endpoint adı, opsiyonel bir parametredir ancak callback mutlaka verilmelidir. Yukarıdaki örnekte callback'in üç ayrı parametre döndüğünü görebilirsiniz. Bu parametreleri aşağıdaki şekilde açıklayabiliriz:

* `req` -> Gönderilen request'e ait bilgileri barındırır.
* `res` -> Request'i gönderen kullanıcıya dönülecek bilgileri barındırır.
* `next` -> Bir sonraki router ya da middleware fonksiyonuna gitmek için kullanılan bir fonksiyon parametresidir.

Makalenin başındaki çizimden de anlaşılabileceği gibi, ExpressJS'te router yapısı tanımlanma sırasına göre çalışır. Bir router'in `middleware` olmasını istiyorsak işte o zaman callback ile gelen `next` fonksiyonuna ihtiyacımız var. Bir request geldiğinde bazı işlemler/kontroller yaptıktan sonra router'a gitmesini istiyorsak aşağıdaki gibi bir yapı kullanabiliriz.

```javascript
var express = require('express');
var app = express();
// Route
app.use((req, res, next) => {
    console.log("Middleware");
    next();
});

app.post("/api", (req, res, next) => {
    res.send({success: true});
})
```

Yukarıdaki kod parçası, bir request geldiğinde önce terminale `Middleware` yazar, sonra da response olarak `{success: true}` döner. Aslında middleware yapısı bu kadar basit. Sadece ek bazı bilgilere de ihtiyaç duyabiliriz. Örneğin, biz bazı router'lara özel, sadece ilgili endpointlere istek geldiğinde çalışmasını isteyebiliriz. İşte burada `app.all`, `app.post`, `app.get` gibi yapılar devreye giriyor. Bunları kısaca açıklamak gerekirse:

* `app.all` -> Herhangi bir metod ile request geldiğinde yakala
* `app.post` -> Post metodu ile gönderilen request'leri yakala
* `app.get` -> Get metodu ile gönderilen request'leri yakala

Tabii bunlarla sınırlı değil. Tüm request metodları için bu yapıyı kullanabiliriz.

Son olarak özel bir endpoint için bir middleware nasıl tanımlanır görelim:

```javascript
var express = require('express');
var app = express();
// Route
app.all("/api",(req, res, next) => {
    console.log("Middleware");
    next();
});

app.post("/api", (req, res, next) => {
    res.send({success: true});
})
```

Yukarıdaki kod parçası, `/api` endpoint'ine bir POST isteği geldiğinde çıktı olarak önce terminale `Middleware` yazacak ve ardından `{success: true}` cevabını dönecektir. Eğer `/api` endpoint'ine bir GET isteği gönderilirse; yine terminale `Middleware` yazacaktır ancak bu kez bir cevap dönmeyecek ve timeout'a düşecektir. Bunun sebebi, `app.get` ile tanımlanmış bir `/api` endpoint'i olmamasıdır.

Böylece ExpressJS'te middleware ve router temellerini anlamış olduk. Bu konu, ExpressJS ile geliştirme yapan geliştiricilerin mutlaka bilmesi ve kullanması gereken bir konu olduğu için önemlidir. Umarım faydalı olmuştur. İyi çalışmalar.