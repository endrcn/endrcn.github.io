---
layout: post
title: TamperMonkey ile Sitelerde Özel Script Çalıştırma
tags: [userscript, tampermonkey]
author: endrcn
date: "2022-05-07"
categories: [ Apps ]
---

Bazen tarayıcı tabanlı oyunlarda, bazen de sürekli kullandığımız web sitelerindeki bazı alanları kendimize göre düzenlemek isteyebiliyoruz. Bu gibi durumlarda imdadımıza TamperMonkey yetişir. TamperMonkey, bir Chrome eklentisidir ve bir web sitesinde kendi yazdığımız Javascript kodunu çalıştırabilmemizi sağlar. Eğer Chrome kullanmıyorsanız, Firefox’ta da benzer işleri yapan GreaseMonkey eklentisini kullanabilirsiniz.

<iframe width="560" height="315" src="https://www.youtube.com/embed/dCyAdLMAk2w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

# Kurulum

Öncelikle eklentiyi indirmekle başlayalım. Bunun için [Chrome Web Mağazasına](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=tr) girip eklentiyi kurmalıyız. Mağazaya girdiğimizde sağda Chrome’a Ekle butonuna basmak, eklentiyi kurmak için yeterlidir.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img1.png)

Eklenti yüklendikten sonra tarayıcının sol üst köşesindeki eklenti alanına pinleyelim. Böylece eklentiye ulaşmamız ve yönetmemiz daha kolay olacak.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img2.png)

Bu işlemlerden sonra artık kullanıma geçebiliriz.

# Nerelerde Kullanılır ?

Kullandığınız web sitelerinde ya da oynadığınız tarayıcı tabanlı oyunlarda artık Javascript çalıştırabileceğiniz bir eklentiniz var. Biraz da Javascript bilgisine sahipseniz yapabilecekleriniz hayal gücünüze kalmış. Oyunlardaki premium özellikleri kopyalayıp eklenti ile premium olmadan kullanabilir, web sitelerindeki çerez bildirimlerini kapatabilir, reklamları engelleyebilir -bunun için adblocker var :)- ya da web sitelerinde çalışan bir uygulamanız varsa müşterinizin web sitesine uygulayıp demolarınızda kullanabilirsiniz.

# Nasıl Kullanılır?

Kurulum aşamasını geçtikten sonra artık nasıl kullanacağımıza bakabiliriz. Tarayıcının sağ üst kısmında bulunan eklentiler alanından TamperMonkey eklentisinin simgesine tıklayıp, açılan menüden Dashboard kısmına tıklayalım.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img3.png)

Dashboard ekranına geçtiğinizde boş bir ekrana sahip olacaksınız. Bu ekranda Installed Userscripts alanında daha önce oluşturduğunuz script’leri görebilirsiniz. Hemen solundaki + simgesi ile de yeni bir script oluşturabilirsiniz.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img4.png)

Bu ekran görüntüsünde listede yer alan Local Widget satırı, benim daha önce oluşturduğum bir script, sizi yanıltmasın. + simgesine tıkladığınızda script yazabileceğiniz bir editör sizi karşılayacak.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img5.png)

İlk 10 satırda yer alan bilgiler, yazacağınız script’in adı, versiyonu vs gibi bilgileri verdiğimiz alandır. Aşağıda gerekli alanlarla ilgili bilgiler yer alıyor.

|İsim|Açıklama|
|@name|Script’in adı. Script listesinde görünecek isim.|
|@namespace|Script’in namespace’i. Varsayılan olarak kalabilir.|
|@version|Script versiyonu. Script listesinde versiyon olarak görünür.|
|@description|Script açıklaması.|
|@author|Script’i yazanın bilgisi, Ad Soyad<email> formatında yazabilirsiniz.|
|@match|Script’in hangi sitede ve hangi sayfalarında çalışacağını belirlediğimiz alan.|
|@icon|Script’in iconu. Script listesinde görünür.|
|@grant|Varsayılan olarak kalabilir.|

Yukarıdaki tabloda yer alan en önemli alan @match alanıdır. Bu alan, yazdığımız script’in hangi sitede çalışacağını ve bu sitenin hangi sayfalarında çalışacağını belirleyen alandır. Örneğin @match https://www.google.com/ yazdığımızda, script sadece google.com adresinde çalışacaktır. Ancak google’nin alt sayfalarında da çalışmasını istediğimiz bir script yazmak istersek bu durumda tanımı şu şekilde yapmak gerekir:

```
@match    https://www.google.com/*
```

Bu tanımla birlikte artık google.com ve alt sayfalarda çalışmasını sağladık. * operatörü bize bir şey yazılması ya da hiçbir şey yazılmaması durumunda eşleşme olmasını sağlar. Düzenli ifadelerle ilgili daha detaylı bilgi almak isterseniz Regex Nedir? Nasıl Çalışır? Kullanım Amacı Nedir? makalesinden faydalanabilirsiniz.

Meta alanlarına ait detayları verdiğimize göre artık script’i nasıl yazacağımıza ve bir örnek ile gösterme aşamasına geçebiliriz. Tahmin edebileceğiniz gibi script’i yazacağımız alan Your code here” ile belirtilen alan. Nasıl çalıştığını anlatabilmek için bir örnek yapalım.

Hedefimiz; google arayüzünün arkaplanını siyah yapmak olsun. Bunun için öncelikle sadece anasayfada çalışan bir script oluşturalım ve meta alanlarını dolduralım.

```js
// ==UserScript==
// @name         Google Gece Modu
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Google Gece Modu
// @author       endrcn
// @match        https://google.com/
// @icon         https://www.google.com/s2/favicons?sz=64&domain=endrcn.dev
// @grant        none
// ==/UserScript==
 
(function() {
    'use strict';
 
    // Your code here...
})();
```

Scriptimizi oluşturduk, artık scriptimiz Installed Userscripts listesinde aşağıdaki gibi görünecektir.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img6.png)

Şimdi çalışacak kodları yazalım. Basitçe yapacağımız iş, HTML sayfasında bulunan body etiketinin arkaplan rengini siyah olarak değiştirmek olacak. Bunun için gerekli kod parçası:

```js
document.body.style.backgroundColor = "black";
```

Tüm script’i ve sonucunu görelim.

```js
// ==UserScript==
// @name         Google Gece Modu
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Google Gece Modu
// @author       endrcn
// @match        https://www.google.com/
// @icon         https://www.google.com/s2/favicons?sz=64&domain=endrcn.dev
// @grant        none
// ==/UserScript==
 
(function() {
    'use strict';
 
    document.body.style.backgroundColor = "black";
})();
```

Sonuç:

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img7.png)

Yalnız meta alanındaki @match kısmında sadece google anasayfasını belirttiğimiz için alt sayfalara bu script etki etmedi.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img8.png)

Tüm sayfalarda çalışmasını sağlamak için yukarıda belirttiğim gibi * operatöründen yararlanabiliriz.

![_config.yml]({{ site.baseurl }}/assets/images/post_images/tampermonkey/img9.png)

Arada bir bar beyaz kaldı ancak script’imizin çalıştığını görebiliyoruz. Tamamını siyah yapmak için siz daha detaylı bir script yazabilirsiniz tabii ki. Böylece alıştırma da yapmış olursunuz. Script’in son hali aşağıdaki gibi oldu:

```js
// ==UserScript==
// @name         Google Gece Modu
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Google Gece Modu
// @author       endrcn
// @match        https://www.google.com/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=endrcn.dev
// @grant        none
// ==/UserScript==
 
(function() {
    'use strict';
 
    document.body.style.backgroundColor = "black";
})();
```

Umarım faydalı olmuştur. İyi çalışmalar.