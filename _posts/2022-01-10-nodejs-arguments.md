---
title: "Node.js - Komut Satırı Argümanları (Command Line Arguments)"
date: "2022-01-10"
layout: post
author: endrcn
categories: [NodeJS]
---

Node.js ile geliştirdiğimiz bir uygulamayı çalıştırırken, uygulama içine argümanlar iletmek basit ama bir o kadar önemli bir işlevdir. Bu basit ama gerekli olan işlev, özellikle bir CLI(**C**ommand **L**ine **I**nterface) uygulaması geliştirirken sıkça kullandığımız bir özelliktir. Uygulamaya argümanları aşağıdaki formatta gönderebiliriz.

```bash
node script.js arg1 arg2 arg3
```

Bu şekilde gönderdiğimiz argümanları, uygulama içerisinde kullanabilmek için **process.argv** dizisi kullanılır. **process.argv** dizisinin ilk iki elemanı **node** ve **script.js** tir.

```javascript
// script.js
console.log(process.argv);
```

Script.js uygulamasını çalıştırdığımızda çıktı aşağıdaki gibi olur.

![_config.yml]({{ site.baseurl }}/assets/images/cli-1.png)

Uygulama çalıştırılırken her bir argüman birbirinden boşluk karakteriyle ayrılır. Bu nedenle eğer içinde boşluk geçen bir string'i argüman olarak göndermek istersek, tırnak içine almalıyız.

**process.argv** dizisinde her zaman ilk iki eleman node komutu ve çalıştırılan dosya adı olacağından, Array.slice() metoduyla ilk iki elemanı atabiliriz.

```javascript
const arguments = process.argv.slice(2);
console.log(arguments);
```

![_config.yml]({{ site.baseurl }}/assets/images/cli-2.png)

Son olarak genelde CLI uygulamalarını çalıştırırken bazı flag'ler ile bir sonraki parametrenin ne olduğuna dair bilgi verilir. Böylece parametrelerin sıralaması önemini yitirir. Bunun için yukarıdaki yöntemlerle kendimiz bir yapı kurabiliriz ancak daha önce de söylediğimiz gibi bu tarz özel işleri yapan birçok [modül](https://endrcn.dev/nodejs-modules/) mevcuttur. Bu tarz parametre alan CLI uygulaması geliştirmek için kullanabileceğimiz popüler bir modül de [yargs](https://www.npmjs.com/package/yargs) modülüdür. Modül kullanımıyla ilgili detaylara modülün NPM sayfasından ulaşabilirsiniz.

## Örnek

Parametre olarak dosya yolu(path) alan ve dosyayı okuyup içindeki verileri ekrana basan bir uygulamayı örnek olarak geliştirelim. Öncelikle ana dizinde bir _data_ klasörü oluşturup altına da _text.txt_ adında bir metin dosyası yükleyelim.

![_config.yml]({{ site.baseurl }}/assets/images/cli-3.png)

Ardından ana dizinde _readfile.js_ adında bir dosya oluşturup yukarıda gördüğümüz gibi argümanları alalım.

```javascript
// readfile.js
const arguments = process.argv.slice(2);
```

Dosya okumak için kullanacağımız **fs** modülündeki readFile ve existsSync metotlarını [destructuring assignment](https://endrcn.dev/nodejs-destructuring/) yöntemiyle dosyamıza import edelim.

```javascript
const { readFile, existsSync } = require("fs");
const arguments = process.argv.slice(2);
```

Son kullanıcıdan gelen parametreye güvenemeyeceğimizi aklımızdan çıkarmayıp, argümanın gerçekten gönderilip gönderilmediğini ve argümanla gelen dosyanın gerçekten var olup olmadığını kontrol edelim.

```javascript
const { readFile, existsSync } = require("fs");
const arguments = process.argv.slice(2);
let filePath = arguments[0];
if (!filePath) {
    console.log("Please enter a filePath");
    console.log("Format: node readfile.js [filePath]");
    process.exit(1);
} else if (!existsSync(filePath)) {
    console.log("File Not Found: " + filePath);
    process.exit(1);
}
```

**Not:** _process.exit()_ metodu, uygulamanın kapanmasını sağlar.

Son olarak dosyamızı okuyabiliriz. Bunun için fs modülünden import ettiğimiz _readFile()_ metodunu kullanacağız. Son durumda uygulama kodumuz aşağıdaki halini aldı.

```javascript
const { readFile, existsSync } = require("fs");
const arguments = process.argv.slice(2);
let filePath = arguments[0];
if (!filePath) {
    console.log("Please enter a filePath");
    console.log("Format: node readfile.js [filePath]");
    process.exit(1);
} else if (!existsSync(filePath)) {
    console.log("File Not Found: " + filePath);
    process.exit(1);
}

readFile(filePath, "utf-8", (err, fileContent) => {
    console.log(fileContent);
});
```

**Not:** fs modülü, çok sık kullanılan ve dosyalar üzerinde işlem yapmamızı sağlayan [built-in](https://nodejs.org/api/fs.html) bir modüldür.

Uygulamamızı argüman vermeyerek, hatalı argüman vererek ve doğru argüman vererek çalıştırdığımızda aşağıdaki gibi sonuçlar elde ederiz.

![_config.yml]({{ site.baseurl }}/assets/images/cli-4.png)

Harika bir iş çıkartıyorsunuz. Bu şekilde devam. Bir sonraki yazımızda çevre değişkenlerinden (environment variables) bahsedeceğiz. İyi çalışmalar.
