---
title: "Node.js - Modül Oluşturma ve Export"
date: "2022-01-07"
layout: post
author: endrcn
categories: [NodeJS]
---

[Node.js Temelleri](https://endrcn.dev/nodejs/introduction/) serisinde şimdiye kadar tüm örneklerimizi sadece bir dosya üzerinden anlattık. Ancak bir uygulamayı sadece bir dosya ile geliştiremeyeceğimiz çok açık. Bu nedenle uygulamada kullanacağımız sınıfları, metotları vs. ayrı birer dosyada tutup bu verileri export etmeliyiz.

[Node.js - Modüller](https://endrcn.dev/nodejs/modules/) makalesinde bir modülü çalışma dosyamıza nasıl import edeceğimizi görmüştük. Aslında gördüğümüz require anahtar kelimesi ile oluşturduğumuz dosyaları da aynı şekilde import edebiliriz.

const SomeClass = require("./SomeClass");

Tabii bir dosyayı import edebilmek için bazı kurallar var. Bir dosyayı modül gibi import edebilmek için öncelikle dosyanın export edilmesi gerekir. Bir örnek üzerinde görelim. Öncelikle proje klasörümüzde bir lib klasörü oluşturup içine Conversations.js tanımlayalım. Ana dizine de main.js dosyamızı oluşturalım.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-07-at-01.39.45.png)

Modül Oluşturma Dizin Yapısı

lib/Conversations.js dosyasının içinde bir sınıf oluşturalım ve bu sınıfı **module.exports** ile export edelim.

```javascript
// lib/Conversations.js
class Conversations {
    constructor() { }

    find() {
        return "conv";
    }

    getConversations() {
        return ["conv1", "conv2"];
    }
}

module.exports = Conversations;
```

Bu şekilde tanımlanmış bir dosyayı artık projemizin herhangi bir yerinde dosya yolunu vererek çağırabiliriz. main.js' e dosyamızı import edelim.

```javascript
// main.js
const Conversations = require("./lib/Conversations");
const convInstance = new Conversations();
let result = convInstance.find();
console.log(result); // Output: conv
```

Yukarıdaki kodları açıklamak gerekirse:

- 2. satırda, require ile lib altındaki Conversations.js dosyasını main.js'e import ettik.
- 3. satırda, import ettiğimiz dosyada bir sınıf export edildiği için, sınıftan bir [nesne](https://endrcn.dev/nodejs/classes/) türettik.
- 4. satırda, sınıfın find() metodu çağrılarak result değişkenine atadık.
- 4. satırda, sonucu ekrana bastık.

Oluşturduğunuz bir modül dosyasını doğru şekilde import ettiğinizde VS Code bunu tanıyacaktır ve otomatik tamamlamada ilgili sınıfa ait metot ve property'ler görünecektir.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-07-at-01.48.31.png)

**Not:** require ile import aşamasında dosyanın uzantısını belirtmedik ancak belirtsek de sorunsuz çalışacaktır.

Tabii ayrı dosyalarda sadece sınıf barındırmayabiliriz. İçinde fonksiyonlar barındıran bir obje oluşturup bunu da export edebiliriz. Örneğin yukarıdaki sınıfı obje olarak gönderelim. Bunun için lib klasörü altında ConversationObject.js adında başka bir dosya oluşturalım.

```javascript
const conversations = {
    find: () => {
        return "conv";
    },
    getConversations: () => {
        return ["conv1", "conv2"];
    }
}

module.exports = conversations;
```

Bu şekilde export edilen bir dosyayı yukarıdaki ile aynı şekilde import edip kullanabiliriz. Ayrıca [destructuring assignment](https://endrcn.dev/nodejs/destructuring/) kullanarak sadece kullanacağımız metodu import etme seçeneğimiz de var. Bunu nasıl yapacağımızı görelim.

```javascript
const { find } = require("./lib/ConversationObject");

let result = find();

console.log(result); // Output: conv
```

Eğer import ettiğimiz ana dosyamızın içinde başka bir find metodu varsa bir çakışma olacaktır. Bu durumda destructuring assignment sırasında metoda farklı bir isim verebiliriz.

```javascript
const { find: convFind } = require("./lib/ConversationObject");

let result = convFind();

console.log(result); // Output: conv
```

Bu yazımızla birlikte artık uygulama geliştirme aşamasında ihtiyacınız olan en önemli konulardan birini görmüş olduk. Bir sonraki yazımızda uygulamaya dışardan parametre almayı ve çevre değişkenlerini anlatmaya çalışacağım.
