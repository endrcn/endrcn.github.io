---
title: "Node.js - Çevre Değişkenleri (Environment Variables)"
date: "2022-01-11"
layout: post
author: endrcn
categories: [NodeJS]
---

Bir önceki yazımızda [Node.js ile CLI uygulaması geliştirmeyi](https://endrcn.dev/nodejs-arguments/) ve bu uygulamaya argümanlar ile nasıl veri aktarabileceğimizi anlatmıştım. Bu yazıdaysa aynı işlevi farklı bir biçimde gerçekleştiren ve özellikle büyük uygulamalar geliştirdiğimizde tercih edeceğimiz **çevre değişkenleri (environment variables, environments)** aktarımından bahsedeceğim.

<iframe width="560" height="315" src="https://www.youtube.com/embed/czO4F5PfhVw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Çevre Değişkenleri Nedir? Neden İhtiyaç Duyarız?

Çevre değişkenleri -ortam değişkenleri de deniyor-, işletim sistemi düzeyinde saklanan değişkenlerdir. Genelde bir yazılım geliştirildikten sonra bir yayınlama sürecine gireriz. Bu yayınlama sürecinde genelde _Development_ > _Test_ > _QA_ > _Prod_ şeklinde adlandırdığımız 4 farklı ortamda uygulamamız geliştirilir, test edilir ve yayınlanır. İşte tam da bu noktada ortamlara uygun parametreleri(Örn: DB bağlantı bilgileri, Log Path, Log seviyesi vs.) uygulamamıza aktarmamız gerekir. Ortamlara göre değişen bu bilgileri ayrı ayrı sabit dosyalarda tutmak çok ilkel ve kullanışsız bir yöntem olacağından, bu gibi durumlarda ortam değişkenlerinden yararlanırız.

![_config.yml]({{ site.baseurl }}/assets/images/deployment.png)

Kaynak: [https://medium.com/venture-garden-group-technology-blog/effective-development-environments-development-test-staging-pre-production-and-production-environmen-a3a85cd349b2](https://medium.com/venture-garden-group-technology-blog/effective-development-environments-development-test-staging-pre-production-and-production-environmen-a3a85cd349b2)

## Nasıl Kullanılır?

Node.js'te bir çevre değişkenine erişmek için **process.env** objesini kullanırız.

console.log(process.env);

Çıktı olarak ortamdaki tüm değişkenleri verir.

![_config.yml]({{ site.baseurl }}/assets/images/env-2.png)

Uygulamaya özel değişkenlerimizi, uygulamayı komut satırında çağırmadan önce tanımlayabiliriz. Şimdi _main.js_ adında bir dosya oluşturup NODE_ENV değişkenini ekrana yazdıralım.

```javascript
// main.js
const NODE_ENV = process.env.NODE_ENV;
console.log("NODE_ENV: ",NODE_ENV);
```

Yazdığımız uygulamayı aşağıdaki şekilde çalıştırıp çıktıyı görelim:

```javascript
NODE_ENV=production node main.js
/*
Output:
NODE_ENV: production
*/
```

Oldukça basit bir kullanıma sahip olan bu yöntem, bizi büyük bir yükten kurtarıyor. Peki biz bu parametreleri uygulamayı çalıştırırken her seferinde elle mi vereceğiz? Bunun için bir sürü yöntem mevcut ancak ben burada üç tanesinden bahsedeceğim.

## Bash Dosyası

Uygulamamızı çalıştırırken kullandığımız komutu ve tanımlayacağımız parametreleri bir bash dosyasında tanımlayabiliriz. Bu dosya Linux ve Mac'te sh uzantılı, Windows'ta ise bat uzantılı olacaktır. Böylece uygulamamızı her çalıştırdığımızda ortam değişkenlerini tekrar tekrar tanımlamamıza gerek olmayacaktır.

Örnek bir bash dosyası içeriği şu şekilde olabilir:

```javascript
//start.sh
NODE_ENV=production PORT=5000 LOG_LEVEL=info node main.js
```

Bunun gibi start.sh dosyalarını her ortam için bir kez hazırlayıp sonrasında kullanabiliriz. Tabii çalıştırmak için işletim sistemi düzeyinde sistem kullanıcısına çalıştırma izni vermek gerekir. Onu da aşağıdaki şekilde yapabiliriz.

```bash
chmod +x start.sh
```

Son olarak çalıştırmak için aşağıdaki komutu kullanabiliriz.

```bash
./start.sh
```

Bu yöntem ile çalıştırmak, uygulamayı işletim sistemi kullanıcısı sunucuda login olduğu sürece ayakta tutacaktır. Bunun yerine _nohup_ komutuyla uygulamayı kullanıcıdan bağımsız olarak çalıştırabiliriz. Böylece sunucudan çıksak dahi çalışmaya devam eder.

```bash
nohup ./start.sh > app.log &
```

Yukarıdaki komutla; start.sh dosyasını kullanıcı oturumundan bağımsız şekilde çalıştırdık ve uygulamaya ait logları da app.log dosyasına yönlendirdik.

## dotenv Modülü

[dotenv](https://www.npmjs.com/package/dotenv) modülü, çevre değişkenlerini bir dosyada(.env) tanımlayıp uygulamamızda kullanabilmemizi sağlar. Böylece ortam değişkenlerimizi uygulama çalıştırma aşamasından ayırmış oluruz. Bir örnek üzerinden kullanımını görelim. Modülü kullanabilmek için öncelikle modülü kurmamız gerekir.

```bash
npm i dotenv
```

Kurulum tamamlandıktan sonra modülü kullanabiliriz. **main.js** dosyasına modülü import edelim.

```javascript
// main.js
require('dotenv').config();
```

Yukarıda yapılan işlem; _dotenv_ modülünün içindeki _config()_ metodunun çağrılmasıdır. Bu işlemi daha önce gördüğümüz modül import'una benzetmek isteseydik şu şekilde de yapabilirdik.

```javascript
// main.js
const dotenv = require('dotenv');
dotenv.config();
```

_config()_ metodu, process.env objesine .env dosyası içindeki değişkenlerin eklenmesini sağlar. .env dosyası, her satırda bir environment tanımlanacak şekilde environment=value şeklinde tanımların bulunduğu dosyadır.

```javascript
// .env
DB_HOST=localhost
DB_NAME=endrcn
DB_PASS=e123c
PORT=5000
```

_main.js_ dosyasındaki kullanımını görelim:

```javascript
//main.js
require("dotenv").config();

console.log("DB_HOST:", process.env.DB_HOST);
console.log("DB_NAME:", process.env.DB_NAME);
console.log("DB_PASS:", process.env.DB_PASS);
console.log("PORT:", process.env.PORT);
```

Son olarak çıktımız şu şekilde olacak:

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-11-at-03.20.22.png)

## Docker

[Docker](https://www.docker.com/), başlı başına ayrı bir konu. Temel olarak, işletim sistemi içinde ayrı bir container yapısı kurup uygulamanız için gerekli olan her şeyi kendi içinde barındıran bir yapı kurmamızı sağlar. Böylece ortamdan bağımsız bir pakete sahip oluruz. Kurulum ve kullanımı oldukça kolaylaştırdığı için özellikle On-premise(uygulamanın cloud değil de müşterinin iç sunucusunda çalışması) olarak geliştirdiğimiz uygulamalarda tercih edilir. Docker, uygulamaya ait bir ortam oluşturduğu için çevre değişkenlerini de docker'i ayağa kaldırmak için kullandığımız YAML dosyasında tanımlayabiliriz. Eğer uygulamamızı [dockerize](https://github.com/docker/labs/tree/master/developer-tools/nodejs-porting/) edersek, bundan önceki başlıklarda anlattığım yapıları kullanmadan direkt olarak çevre değişkenlerini tanımlayabiliriz.

Çevre değişkenleri konusunun da sonuna geldik. Bir sonraki yazımızda görüşmek dileğiyle.
