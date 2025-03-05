---
layout: post
author: endrcn
title: "Node.js - Giriş (Introduction)"
date: "2021-12-17"
categories: [ NodeJS ]
image: assets/images/nodejs_logo.png
tags: [node-js, nodejs-basics, nodejs-temelleri, nodejs-tutorial]
---

![_config.yml]({{ site.baseurl }}/assets/images/nodejs_logo.png)

Merhabalar, her zaman niyetlenip bir türlü başlayamadığım blog yazma konusunda nihayet ilk adımı attım. İlk blog yazımı yazdığım ve bununla birlikte bir Node.js yazı dizisinin başlangıcını yaptığım için oldukça heyecanlı hissediyorum. Bu seride Node.js üzerine bilmeniz gereken temellerden başlayarak zamanla ileri düzey konulara geçmeyi ve gerçek hayat örnekleriyle de öğrendiklerinizin uygulama alanlarını göstermeyi hedefliyorum. Ayrıca bu eğitimi sadece bir blog yazı dizisi olmaktan çıkarıp videolu örneklerle de destekleme yönünde motivasyonum oldukça yüksek.

Gelelim asıl konuya. Node.js kısaca Google tarafından v8 motorunun kullanılarak Javascript kodlarının browser dışında çalışmasının sağlanmasıyla ortaya çıkmış bir teknolojidir. Node.js ile birlikte artık JS kodlarımızı sunucuda çalıştırabilir ve uygulamalar geliştirebiliriz. Her şeyi pure JS kodu yazarak yapabileceğimiz gibi her zaman işlerimizi kolaylaştıran kütüphaneler(modüller) de mevcuttur. Bu kütüphaneleri kullanarak çok daha hızlı şekilde bir web uygulaması geliştirebilir, yapacağınız işleri çok daha az kod yazarak gerçekleştirebilirsiniz.

Bu yazı dizisinde Node.js (aslında Javascript) temelleri ile birlikte, nasıl bir web/konsol uygulaması geliştireceğimizi, uygulama geliştirirken nelere dikkat edilmesi gerektiğini, CPU ve Memory kullanımını nasıl gözetmemiz gerektiğini, performans sorunlarını nasıl çözebileceğimizi öğreneceğiz. Aslında bu konular hangi programlama dili ile çalışırsanız çalışın öğrenilmesi ve uygulanması gereken konulardır.

Yazı dizisinde değinmeyi hedeflediğim konular aşağıdaki gibidir. Yazı dizisine başladıktan sonra mutlaka ufak tefek değişiklikler olacaktır. Bunu zaman gösterecek.

1. [Değişkenler (Variables)](https://endrcn.dev/nodejs-variables/)
2. [Veri Tipleri (Data Structures)](https://endrcn.dev/nodejs-data-types/)
    1. [Strings](https://endrcn.dev/nodejs-data-types/#Strings)
    2. [Numbers](https://endrcn.dev/nodejs-data-types/#Numbers)
    3. [Booleans](https://endrcn.dev/nodejs-data-types/#Booleans)
    4. [Arrays](https://endrcn.dev/nodejs-data-types/#Arrays)
    5. [Objects](https://endrcn.dev/nodejs-data-types/#Objects)
3. [Operatörler(Operators)](https://endrcn.dev/nodejs-operators/)
4. [Koşullar(Conditions)](https://endrcn.dev/nodejs-conditions/)
5. [Döngüler(Loops)](https://endrcn.dev/nodejs-loops/)
    1. [While](https://endrcn.dev/nodejs-loops/#While_Dongusu)
    2. [Do-while](https://endrcn.dev/nodejs-loops/#Do-While_Dongusu)
    3. [For](https://endrcn.dev/nodejs-loops/#For_Dongusu)
    4. [For...in](https://endrcn.dev/nodejs-loops/https://endrcn.dev/nodejs-loops/#For8230in_Dongusu)
6. [Fonksiyonlar(Functions)](https://endrcn.dev/nodejs-functions/)
7. [Değişken Kapsamları(Variable Scopes)](https://endrcn.dev/nodejs-scopes/)
8. [Sınıf / Nesne Kullanımı(Classes & Instances)](https://endrcn.dev/nodejs-classes/)
    1. [Sınıf tanımlama](https://endrcn.dev/nodejs-classes/#https://endrcn.dev/nodejs-classes/#Sinif_Tanimlama)
    2. [Nesne tanımlama](https://endrcn.dev/nodejs-classes/#https://endrcn.dev/nodejs-classes/#Nesne_Tanimlama)
    3. [Property Tanımlama](https://endrcn.dev/nodejs-classes/#Property_Tanimlama)
9. [Kalıtım(Interitance)](https://endrcn.dev/nodejs-inheritance/)
10. [Hata yakalama(Error Handling)](https://endrcn.dev/nodejs-error-handling/)
11. [Destructuring](https://endrcn.dev/nodejs-destructuring/)
12. [Built-in methods](https://endrcn.dev/nodejs-built-in-modules/)
    1. [Global Methods](https://endrcn.dev/nodejs-built-in-modules/#Genel_Metotlar)
    2. [JSON Methods](https://endrcn.dev/nodejs-built-in-modules/#JSON_Methods)
    3. [Array Methods](https://endrcn.dev/nodejs-built-in-modules/#Array_Methods)
    4. [String Methods](https://endrcn.dev/nodejs-built-in-modules/#String_Methods)
13. [Asenkron Fonksiyonlar(Asynchronous Functions)](https://endrcn.dev/nodejs-asynchronous-functions/)
    1. [Callback](https://endrcn.dev/nodejs-asynchronous-functions/#Callback)
    2. [Promise](https://endrcn.dev/nodejs-asynchronous-functions/#Promise)
    3. [Asyc/Await](https://endrcn.dev/nodejs-asynchronous-functions/#Async_Await)
14. [Modül Nedir? Nasıl Kurulur? Nasıl Kullanılır?](https://endrcn.dev/nodejs-modules/)
15. [Modül Oluşturma ve Export (Birden fazla dosyayla çalışma)](https://endrcn.dev/nodejs-module-export/)
16. [Komut Satırı Argümanları (Command Line Arguments)](https://endrcn.dev/nodejs-arguments/)
17. [Çevre Değişkenleri (Environment Variables)](https://endrcn.dev/nodejs-environments/)
18. [Olaylar (Events)](https://endrcn.dev/nodejs-events/)
19. [Generators](https://endrcn.dev/nodejs-generators/)
20. [Loglama](https://endrcn.dev/nodejs-loglama/)
    1. [Console](https://endrcn.dev/nodejs-loglama/#Console)
    2. [Winston](https://endrcn.dev/nodejs-loglama/#Winston_Modulu_ile_Loglama)
21. Web Scraping/Crawling
    1. [Regex ile Web Crawling](https://endrcn.dev/nodejs-web-scraping-with-regex/)
    2. [Cheerio Modülü ile Web Crawling](https://endrcn.dev/nodejs-web-scraping-with-cheerio/)
22. Express.js
23. Socket.io
