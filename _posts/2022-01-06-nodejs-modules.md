---
title: "Node.js - Modüller"
date: "2022-01-06"
layout: post
author: endrcn
categories: [NodeJS]
tags: [js modüller, node.js modüller, nodejs, node.js]
---

Node.js Temelleri serisinde bu makalemizde modüllerden bahsedeceğiz. Modüller, belli başlı işler yapan metodları bir arada sunan kütüphanelerdir. Node.js'te built-in modüller olduğu gibi sonradan projemize ekleyebileceğimiz modüller de mevcuttur.

Bir Node.js uygulaması geliştirirken built-in modüllerle birlikte birçok dış modülü de dahil etmemiz gerekecektir. Node.js'te kullanılan modüller, Node.js ile birlikte gelen [NPM(Node Package Manager)](https://www.npmjs.com/) içinde yer alır. Spesifik sorunları çözen birçok modüle bu depodan ulaşabilirsiniz.

## Modül Yükleme

Bilgisayarınıza Node.js yüklediğinizde, NPM de beraberinde yüklenecektir. İlk olarak projemizi farklı yerlerde de kuracağımızdan -örneğin bir sunucu- hareketle, kullandığımız modüllerin isimlerini ve versiyonlarını barındıran bir package.json dosyasına ihtiyacımız olacak. Bu dosyayı kolayca oluşturmak için projenizin ana dizininde aşağıdaki komutu çalıştırmalısınız.

npm init --yes

Bu komut, projenizin ana dizininde package.json adında bir dosya oluşturacaktır. İçeriği şu şekilde olur:

```javascript
{
  "name": "nodejs_tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "google.js",
  "directories": {
    "example": "examples"
  },
  "scripts": {
    "test": "echo "Error: no test specified" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

--yes parametresini vermeden direkt olarak **npm init** komutunu çalıştırırsanız, package.json'u oluştururken projenizle ilgili bilgileri tek tek soracaktır.

Bir modülü kurmak için yine projemizin ana dizininde aşağıdaki gibi bir komut çalıştırmamız gerekir.

npm i request --save

Bu komut çalıştırıldığında, projenin ana dizininde **node_modules** adında bir klasör oluşturulur ve **request** isimli modülün dosyaları bu klasörün altına kaydedilir.

--save parametresi de yüklenen **request** modülünün adını ve versiyonunu package.json dosyasına kaydeder. Son durumda package.json dosyası aşağıdaki gibi bir hal alır.

```javascript
{
  "name": "nodejs_tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "google.js",
  "directories": {
    "example": "examples"
  },
  "scripts": {
    "test": "echo "Error: no test specified" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "request": "^2.88.2"
  }
}
```

## Modül Kullanımı / Import

Modül yüklemenin en basit şekilde nasıl yapıldığını gördük. Peki yüklediğimiz modülleri projede nasıl kullanabiliriz? Bunun için modülü kullanacağımız dosyaya modülü **import** etmemiz gerekir.

Node.js çıktığından bu yana bir modülü import etmek için **require** kullanılır. Kullanımı aşağıdaki şekildedir.

const request = require("request");

Bu tanımdan sonra artık **request** isimli değişkenin içinde modülün barındırdığı tüm metotlar yer alır. Her modülün içerdiği metotlar/sınıflar doğal olarak farklıdır. Dolayısıyla modüle ait metotları, modülün dokümanından yararlanarak öğrenebiliriz. Modül dokümanları genelde **NPM** ya da **Github**'da paylaşılır. Ancak bazı modüllerin dokümantasyonu ayrı bir websitesi olarak da karşımıza çıkabiliyor.

[Destructuring assignment](https://endrcn.dev/nodejs/destructuring/) kullanarak, import edilen dosyada kullanacağımız metotları da alabiliriz.

```javascript
const {post, get} = require("request");
```

Yukarıdaki tanımla request.post() metodu post değişkenine, request.get() metodu da get değişkenine aktarılacaktır.

**Not:** require yerine import anahtar kelimesini de kullanabilirsiniz. Ancak bunun için bazı ayarlar yapılması gerekir. İlgili ayarlara [Node.js resmi sitesinden](https://nodejs.org/docs/latest-v13.x/api/esm.html#esm_enabling) ulaşabilirsiniz.

Projemize bir modül yüklemeyi ve bu modülü nasıl kullanacağımızı gördük. Bir sonraki makalemizde Node.js'te varsayılan olarak gelen(built-in) modüllerden fs modülünü inceleyeceğiz. Sağlıkla kalın :)
