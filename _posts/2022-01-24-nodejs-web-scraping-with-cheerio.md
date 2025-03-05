---
title: "Node.js - Cheerio ile Web Scraping/Crawling"
date: "2022-01-24"
layout: post
author: endrcn
categories: [NodeJS]
image: "assets/imagesArchitecture-of-web-scraping.jpg"
---

[Regex ile Web Scraping](https://endrcn.dev/nodejs/web-scraping-with-regex/) makalesinin devamı niteliğindeki bu yazımızda aynı projeyi bu kez _[cheerio](https://github.com/cheeriojs/cheerio)_ modülünü kullanarak nasıl yapacağımızı anlatmaya çalışacağım. Cheerio modülü, Node.js içinde jQuery metotlarını kullanmamızı sağlayan bir modüldür. Böylece sanki tarayıcıdaymış gibi kod geliştirerek verileri ayıklayabiliriz.

<iframe width="560" height="315" src="https://www.youtube.com/embed/fjI1Tfz3bRk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Veri Çekme

Veri çekme adımlarını [Regex ile Web Scraping](https://endrcn.dev/nodejs/web-scraping-with-regex/#Veri_Cekme) makalesinden inceleyebilirsiniz. Direkt kodu ekleyelim:

```javascript
const request = require("request-promise");
 
getExchangeInfo();
 
async function getExchangeInfo() {
    let body = await request({
        url: "https://www.qnbfinansbank.enpara.com/hesaplar/doviz-ve-altin-kurlari",
        method: "GET"
    });
    console.log(body);
}
```

## Cheerio ile Web Scraping

Cheerio modülü bize jQuery metotlarını Node.js içinde kullanma imkanı verdiği için, veri ayıklama geliştirmesine başlamadan önce Chrome Developer Tools kullanarak direkt tarayıcı üzerinde jQuery ile istediğimiz verileri ayıklayabiliriz. Haydi başlayalım.

İlk işimiz veri kazıyacağımız adresi tarayıcıdan açıp ardından Dev Tools'u açalım. Dev Tools'u açmanın birçok yolu var. İlki web sayfasında sağ tıklayıp Öğeyi İncele(Inspect) seçeneğini seçmektir. Ya da Mac için _Cmd+Option+i_, Windows için F12 klavye kısayollarıyla da açabiliriz.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-22.35.32.png)

Ardından Dev Tools'un sol üst köşesindeki Inspect iconuna tıklayıp web sitesinden ayıklamak istediğimiz alanın üzerine mouse imlecini getirip HTML'de ilgili alana gidelim.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-22.40.18.png)

Bu işlemle birlikte USD, EUR ve Altın alanlarına erişmek için erişmemiz gereken sınıf(lar)ı görebildik.

- **_enpara-gold-exchange-rates__table_** sınıfıyla tanımlanmış bir div altında
- **_enpara-gold-exchange-rates__table-item_** sınıfına sahip elemanlar
- Bu elemanların altında da _span_ etiketleriyle ayrılmış ve bizim sahip olmak istediğimiz değerler.

Şimdi bu alandaki verileri çekmek için _jQuery_ yazmaya başlayabiliriz. Dev Tools üzerinden Console tab'ına tıkladığımızda bu web sitesi üzerinde çalışacak javascript kodu yazabileceğimiz alan açılır.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-23.04.22.png)

İlk olarak jQuery kullanarak bulduğumuz kapsayıcı sınıfın alt elemanlarını çekelim.

```javascript
$(".enpara-gold-exchange-rates__table").find(".enpara-gold-exchange-rates__table-item");
```

Ardından bu elemanlar üzerinde dönerek içlerindeki _span_ etiketlerinin verilerine erişebiliriz.

```javascript
$(".enpara-gold-exchange-rates__table").find(".enpara-gold-exchange-rates__table-item").each((index, item) => {
    console.log($(item).find("span").text());
});
```

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-23.19.31.png)

Son olarak bu değerleri bir çıktı olarak istediğimiz objeye yazalım.

```javascript
let json = {
    usd: {},
    eur: {},
    gold: {}
}
$(".enpara-gold-exchange-rates__table").find(".enpara-gold-exchange-rates__table-item").each((index, item) => {
    let currency = $($(item).find("span")[0]).text();
    let buy = $($(item).find("span")[1]).text();
    let sell = $($(item).find("span")[2]).text();
console.log(currency, buy, sell);
    if (currency.includes("USD ($)")) {
        json.usd.buy = buy;
        json.usd.sell = sell;
    } else if (currency.includes("EUR (€)")) {
        json.eur.buy = buy;
        json.eur.sell = sell;
    } else if (currency.includes("Altın")) {
        json.gold.buy = buy;
        json.gold.sell = sell;
    }
});
console.log(json);
```

Çıktı olarak elde ettiğimiz sonuç:

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-23.29.17.png)

Son olarak çıktımızı tam olarak elde edebilmek için değerleri döngü içinde sayısal hale getirelim.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-23.32.49.png)

Tüm işimizi tarayıcı üzerinde bitirdik. Şimdi bu kodları Node.js üzerinde gerçekleştirelim. İlk olarak _cheerio_ modülünü kuralım.

```javascript
npm i cheerio
```

Ardından cheerio modülünü uygulamaya import edip kullanmaya başlayalım. _Request-promise_ modülü ile web sitesinden aldığımız HTML verisini cheerio modülü ile işlenebilir hale getiriyoruz.

```javascript
const request = require("request-promise");
const cheerio = require("cheerio");

async function getExchangeInfo() {
    let body = await request({
        url: "https://www.qnbfinansbank.enpara.com/hesaplar/doviz-ve-altin-kurlari",
        method: "GET"
    });
    let $ = cheerio.load(body);
}
```

Son olarak extractWithCheerio adında bir fonksiyon oluşturup, tarayıcıda yazdığımız kodları oluşturup içine ekleyelim.

```javascript
function extractWithCheerio($) {
    let json = {
        usd: {},
        eur: {},
        gold: {}
    }
    $(".enpara-gold-exchange-rates__table").find(".enpara-gold-exchange-rates__table-item").each((index, item) => {
        let currency = $($(item).find("span")[0]).text();
        let buy = parseFloat(parseFloat($($(item).find("span")[1]).text().replace(",", ".")).toFixed(3));
        let sell = parseFloat(parseFloat($($(item).find("span")[2]).text().replace(",", ".")).toFixed(3));
        if (currency.includes("USD ($)")) {
            json.usd.buy = buy;
            json.usd.sell = sell;
        } else if (currency.includes("EUR (€)")) {
            json.eur.buy = buy;
            json.eur.sell = sell;
        } else if (currency.includes("Altın")) {
            json.gold.buy = buy;
            json.gold.sell = sell;
        }
    });
   return json
}
```

Kodun son hali aşağıdaki gibi olacaktır.

```javascript
const request = require("request-promise");
const cheerio = require("cheerio");

try {
    getExchangeInfo();
} catch (err) {
    console.error("ERR", err);
}

async function getExchangeInfo() {
    let body = await request({
        url: "https://www.qnbfinansbank.enpara.com/hesaplar/doviz-ve-altin-kurlari",
        method: "GET"
    });
    let $ = cheerio.load(body);
    let result = extractWithCheerio($);
    console.log(result);
}

function extractWithCheerio($) {
    let json = {
        usd: {},
        eur: {},
        gold: {}
    }
    $(".enpara-gold-exchange-rates__table").find(".enpara-gold-exchange-rates__table-item").each((index, item) => {
        let currency = $($(item).find("span")[0]).text();
        let buy = parseFloat(parseFloat($($(item).find("span")[1]).text().replace(",", ".")).toFixed(3));
        let sell = parseFloat(parseFloat($($(item).find("span")[2]).text().replace(",", ".")).toFixed(3));
        if (currency.includes("USD ($)")) {
            json.usd.buy = buy;
            json.usd.sell = sell;
        } else if (currency.includes("EUR (€)")) {
            json.eur.buy = buy;
            json.eur.sell = sell;
        } else if (currency.includes("Altın")) {
            json.gold.buy = buy;
            json.gold.sell = sell;
        }
    });
   return json
}
```

Çıktıyı da görelim:

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-23-at-23.53.38.png)

Cheerio ile web scraping yazımızın sonuna geldik. Umarım faydalı bir içerik olmuştur. İyi çalışmalar.
