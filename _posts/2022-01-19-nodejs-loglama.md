---
title: "Node.js - Loglama (Logging)"
date: "2022-01-19"
layout: post
author: endrcn
categories: [NodeJS]
---

Loglama, bir uygulamanın en temel ve en önemli parçalarından biridir. Şimdiye kadarki tüm yazılarımızda logları built-in olarak gelen _console_ modülünü kullanarak ekrana basmıştık. Şimdi loglama konusunda biraz daha detaylı inceleme yapalım.

## Console

_console_ modülü, loglama için kullanılan en temel modüldür. Javascript/Node.js öğrenen herkes ilk olarak console modülü ile tanışır.

```javascript
console.log("Hello World!");
```

Ancak console modülünün tek metodu **_log()_** metodu değildir. Diğer loglama metotlarını da inceleyelim.

### console.error(msg)

Bir hatayı ekrana basmak için kullanılır.

```javascript
console.error("There is an error");
```

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-15-at-02.13.34.png)

```javascript
console.error(new Error("There is an error"));
```

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-15-at-02.14.55.png)

### console.warn(msg)

Uyarı mesajlarını loglamak için kullanılır.

```javascript
console.warn("This is a warning message");
```

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-15-at-02.16.23.png)

### console.info(msg)

Bilgi mesajlarını loglamak için kullanılır.

console.info("info");

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-15-at-02.17.50.png)

### console.table(json)

Bir JSON verisini tablo olarak gösterebilmemizi sağlar.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-15-at-02.21.10.png)

En sık kullanılan loglama metotlarını görmüş olduk. Ek olarak bir JSON objesini loglamaya çalıştığımızda iç içe objeler olması durumunda tamamını göremeyiz.

```javascript
let json = {
    settings: {
        application: {
            name: "App",
        },
        api: {
            url: "http://",
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Content-Length": "200",
                "Token": "Bearer abc"
            }
        }
    }
}
console.log(json);
```

Log çıktısı:

```javascript
{
  settings: {
    application: { name: 'App' },
    api: { url: 'http://', method: 'POST', headers: [Object] }
  }
}
```

Çıktıdan da gördüğümüz gibi headers objesinin detayları loglanmadı. Tüm detayları loglamak için JSON objesini string'e çevirmemiz gerekir. Bunun için [JSON.stringify()](https://endrcn.dev/nodejs/built-in-modules/#JSON_Methods) metodunu kullanırız.

```javascript
console.log(JSON.stringify(json));
/*
Output:
{"settings":{"application":{"name":"App"},"api":{"url":"http://","method":"POST","headers":{"Content-Type":"application/json","Content-Length":"200","Token":"Bearer abc"}}}}
*/
```

## console.time(label)/console.timeEnd(label)

_time()_ ve _timeEnd()_ metotları, bir kod parçasının harcadığı süreyi ölçer ve loglar. time() metodu, ölçümü başlatmamızı, timeEnd() metodu da bitirmemizi sağlar. İki metodun birbirine bağlandığı kısım verilen _**label**_ parametresidir. Bir süre ölçmek için time() metoduna verilen label, timeEnd() metoduna da verilmelidir.

```javascript
function add(...args) {
    let total = 0;
    for (let i = 0; i < args.length; i++) {
        total += args[i];
    }
    return total;
}

console.time("addTime");
add(1,2,3,4,5);
console.timeEnd("addTime"); // Output: addTime: 0.042ms
```

Eğer timeEnd() metoduna gönderilen label parametresi time() metodu ile başlatılmadıysa bir uyarı fırlatılır.

```javascript
console.time("addTime");
add(1,2,3,4,5);
console.timeEnd("add");
// Output: (node:65103) Warning: No such label 'add' for console.timeEnd()
```

Genel hatlarıyla loglama işleminin _console_ modülü ile nasıl yapıldığını gördük. Ancak geniş bir uygulamada logları formatlı basmak yüksek önem taşır. **_Bu nedenle console modülünün, büyük projelerde kullanılması önerilmez._**

## Winston Modülü ile Loglama

Büyük uygulamalar için loglama yapıları genelde makinelerin okuması üzerine tasarlanır. Bu şekilde tasarlanmasının en temel sebebi, log uygulamalarının loglarımızı takip edebilmesini ve alarm üretebilmesini sağlamaktır. Ayrıca projelerde bir log standardı oluşturmak, takım halinde geliştirilen projelerde tüm ekibin aynı düzende log oluşturması ve özellikle production(canlı) ortamda oluşan hataları görebilmek için oldukça kritik bir öneme sahiptir.

Winston modülü ile loglama yapmak için bazı tanımlar ve ayarlar yapmamız gerekir. Bunlar; log formatı ve seviyelerini belirleme, transport tanımlama ve logger oluşturma. Her birini ayrı başlıklar altında inceleyelim. Öncelikle winston modülünü kuralım.

```javascript
npm i winston --save
```

### Logger Oluşturma

Loglama yapısını kurmak için ilk yapacağımız iş, bir logger oluşturmaktır. Bunun için _winston_ modülünün **_createLogger()_** metodunu kullanırız. Tanımladığımız logger, projemizin her yerinde kullanılacağı için [modül olarak tanımlayıp export](https://endrcn.dev/nodejs/module-export/) edelim..

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-19-at-00.06.38.png)

logger.js içine winston modülünü import edip logger'i oluşturalım. createLogger() metodu bir object parametresi alır. Bu obje, loglama ayarlarımızı barındırır. Aşağıda bir loglama yapısında en gerekli ayarların detaylarına değineceğiz.

```javascript
const winston = require("winston");

const logger = winston.createLogger({
    format: winston.format.json(),
    transports: [
        //
        // - Write all logs with importance level of `error` or less to `error.log`
        // - Write all logs with importance level of `info` or less to `combined.log`
        //
        new winston.transports.File({ filename: 'error.log', level: 'error' }),
        new winston.transports.File({ filename: 'combined.log' }),
    ]
});

module.exports = logger;
```

Yukarıdaki kod parçasıyla error loglarını(seviyeler kısmında detaylandıracağım) error.log, diğer seviyelerdeki logları combined.log dosyasına JSON tipinde basan bir _logger_ oluşturduk ve export ettik. Böylece oluşturduğumuz _logger_'i farklı dosyalardan import edebileceğiz. Şimdi adım adım temel ayarlardan bahsedelim.

### Transport

Transport, logların basılma biçimini belirlememizi sağlar. Oluşan logları bir dosyaya basabileceğimiz gibi direkt terminale de bastırabiliriz. Ayrıca log seviyelerine göre bu kararı farklılaştırabiliriz. Örneğin yukarda yaptığımız gibi error loglarını farklı dosyaya diğer seviyelerdeki logları başka bir dosyaya yazabiliriz. İsterseniz başka seviyedeki logları da terminale yazdırabiliriz.

```javascript
const { transports, createLogger, format, addColors } = require("winston");

const transportList = [];

let transport = new transports.Console({
    format: format.json()
});

transportList.push(transport);

transport = new transports.File({
    format: format.simple(),
    filename: 'error.log',
    level: 'error'
})

transportList.push(transport);
```

Yukarıdaki kodu açıklayalım:

- [Destructuring assignment](https://endrcn.dev/nodejs/destructuring/) kullanarak _winston_ modülü içerisindeki transport, createLogger ve format alanlarını birer değişkene atadık.
- transportList adında bir [array](https://endrcn.dev/nodejs/data-types/) tanımladık. Tanımladığımız transport'ları bu dizide toplayacağız.
- Ardından iki tane transport tanımladık. İlk tanımladığımız transport, terminale basan **Console** tipinde, diğeri ise dosyaya basan **File** tipindedir.

Son olarak tanımladığımız transportList dizisini createLogger metoduna gönderelim.

```javascript
const logger = winston.createLogger({
    transports: transportList
});
```

### Log Seviyeleri

Basılan logların seviyeleri; bir sorunu görmek, alarm üretmek gibi işlemlerde önemli bir yere sahiptir. Winston modülünde bir tanımlama yapmasak da standart log seviyeleri vardır.

```javascript
{
  error: 0,
  warn: 1,
  info: 2,
  http: 3,
  verbose: 4,
  debug: 5,
  silly: 6
}
```

[RFC5424](https://tools.ietf.org/html/rfc5424) standardına uygun olarak her bir log seviyesi bir öncelik sırasına sahiptir. 0; en yüksek öncelikli, 6; en düşük önceliklidir. Ayrıca kendi log seviyelerimizi de tanımlayabiliriz. Aynı şekilde öncelik sırası belirtmemiz gerekir.

```javascript
const myCustomLevels = {
  levels: {
    foo: 0,
    bar: 1,
    baz: 2,
    foobar: 3
  },
  colors: {
    foo: 'blue',
    bar: 'green',
    baz: 'yellow',
    foobar: 'red'
  }
};

const customLevelLogger = createLogger({
  levels: myCustomLevels.levels
});
```

Özel seviyeleri tanımlarken bir de renk belirttik. Loglama sırasında renklendirme yapmak istersek addColors metodunu kullanmamız gerekir.

```javascript
addColors(myCustomLevels.colors);
```

Son olarak; oluşturduğumuz transport'a vereceğimiz level ayarı ile hangi logların basılacağını belirleyebiliriz. Örneğin development ortamında log seviyesini **debug** olarak belirlerken, production ortamındaki log seviyemiz **info** olabilir. Bu ayarı ortama göre verebilmek için [çevre değişkenlerinden](https://endrcn.dev/nodejs/environments/) yararlanabiliriz. Level bilgisini nasıl vereceğimizi görelim:

```javascript
const logger = createLogger({
    level: "info",
    transports: transportList
});
```

Level bilgisi **info** olarak verildiğinde, yukarıda belirttiğimiz önceliklendirmeye göre info ve üstündeki loglar basılır. Yani _**info**_, _**warn**_ ve _**error**_ logları ekrana basılır, diğerleri basılmaz.

### Log Formatı

Log formatı belirlerken projenin ihtiyacına göre hareket etmek gerekir. Ancak loglama sırasında bazı alanların bulunması standart haline gelmiştir. Bunlardan ilki ve en önemlisi _**tarih(timestamp)**_ bilgisidir. Ayrıca her bir logda _**level**_ bilgisi de yer almalıdır. Üçüncü alan da söylemeye gerek olmasa da söyleyelim logun kendisidir :)

Log formatı oluştururken log izleyen uygulamaların okuyabilmesi için oluşturmak doğru bir yöntemdir. Çünkü bu uygulamalar ile logları izleyerek alarm üretebilir ve bu böylece sadece alarm durumlarında logları inceleyebiliriz.

_Winston_ modülünde log formatı oluşturma işlemi için konfigürasyon objesinde **format** alanını kullanırız. Hazır format tiplerini kullanabileceğimiz gibi, kendimize ait bir format belirleyebilir ya da birden fazla formatı birleştirebiliriz. Winston ile gelen json() ve simple() metotlarını kullanarak bir format oluşturabiliriz. Yukarıdaki örneklerde nasıl tanımlayacağımızı gördük. Şimdi kendi formatımızı oluşturalım.

```javascript
const customFormat = format.printf(info => `${info.timestamp} ${info.level.toUpperCase()}: [userid:${info.message.userId}] [client:${info.message.client}] [location:${info.message.location}] [procType:${info.message.procType}] [log:${JSON.stringify(info.message.log)}] [processId:${info.message.processId}]`);

let transport = new transports.Console({
    format: format.combine(
        format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss.SSS' }),
        format.simple(),
        customFormat
    )
});
```

Yukarıdaki kod parçasını adım adım inceleyelim.

- Kendi formatımızı oluşturmak için format.printf() metodunu kullandık. Bu metod bir callback fonksiyonu parametre olarak alır ve bu callback fonksiyonun parametresi de iletilen log verisidir. Bu şekilde gelen log verisinden yararlanarak kendimize ait log formatını oluşturabiliyoruz.
- Terminale yazan bir transport oluşturup formatını belirledik.
- format.combine() metodunu kullanarak birden fazla formatı bir arada kullanmak istedik.
- format.timestamp() metodu ile özel format belirlediğimiz log'a timestamp verisinin gönderilmesini sağladık. Böylece printf metodunun callback'inin aldığı _info_ parametresinin içine timestamp girmiş oldu.
- format.simple() metodu ile basacağımız logun tek satırda basılmasını sağladık.

Böylece kendi formatımızı nasıl oluşturacağımızı da gördük. Son durumda logger.js dosyamız aşağıdaki halini aldı.

```javascript
const { transports, createLogger, format } = require("winston");

const transportList = [];

const customFormat = format.printf(info => `${info.timestamp} ${info.level.toUpperCase()}: [userid:${info.message.userId}] [ip_addr:${info.message.ipAddr}] [location:${info.message.location}] [procType:${info.message.procType}] [log:${JSON.stringify(info.message.log)}]`);

let transport = new transports.Console({
    format: format.combine(
        format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss.SSS' }),
        format.simple(),
        customFormat
    )
});

transportList.push(transport)

const logger = createLogger({
    transports: transportList
});

module.exports = logger;
```

### Loglama

Şimdiye kadar bir logger oluşturma, transport tanımlama, seviyeler ve log formatlarını tanımlamayı gördük. Son olarak tüm bu yaptığımız tanımlamaları nasıl kullanacağımızı görelim. Öncelikle **_main.js_** dosyasına logger.js'i import edelim ve ardından bir info logu basalım.

```javascript
const logger = require("./lib/logger");

logger.info({
    userId: "12345",
    ipAddr: "127.0.0.1",
    location: "main.js",
    procType: "log",
    log: {
        req: {
            foo: "bar"
        }
    }
});
```

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-20-at-00.11.27.png)

Oluşturduğumuz logger, tüm seviyeler için bir metoda sahiptir. Bizim seviyelerimiz error, warn, info,...,silly olarak tanımlı olduğundan bu metotları kullanabiliriz.

```javascript
logger.error()
logger.warn()
logger.info()
logger.http()
logger.verbose()
logger.debug()
logger.silly()
```

Bunların dışında log() metodunu kullanıp, level bilgisini ilk parametresinde verebiliriz.

```javascript
logger.log("info", {});
```

Şimdi aklınızdan her log için bir JSON mı ekleyeceğiz? Ekibi buna nasıl adapte edeceğiz? Log girilirken eksik veri girilmemesini nasıl sağlayacağız? gibi soruların geçtiğini düşünüyorum. Ben bu tarz sorunları çözmek için direkt oluşturulan logger'i kullanmayıp araya bir sınıf koymayı tercih ediyorum. Bu sınıfın da metotları yukarıdaki seviye metotlarını barındırıyor ancak loglanacak her bir veriyi ayrı parametreler olarak alıyor. Böylece ekibin adapte olması ve eksik parametre geçilmesi gibi sorunların önüne geçmiş oluyorum. Bunun için lib klasörü altına bir de loggerClass.js oluşturalım ve sınıfı tanımlayalım.

```javascript
const logger = require("./logger");
//Singleton
let instance = null;
class Logger {

    constructor() {
        if (!instance) {
            this.logger = logger
            instance = this;
        }
        return instance;

    }

    handleLog(log) {
        if (log instanceof Error) return JSON.stringify(log, Object.getOwnPropertyNames(log));
        let resp = log ? JSON.parse(JSON.stringify(log)) : {};
        return resp;
    }

    createLogObject(userId, ipAddr, location, procType, log) {
        return {
            userId,
            ipAddr,
            location,
            procType,
            log
        };
    }

    /**
     * Prints Info Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async info(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.info(logs);
    }

    /**
     * Prints Error Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async error(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.error(logs);
    }

    /**
     * Prints Warn Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async warn(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.warn(logs);
    }

    /**
     * Prints Http Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async http(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.http(logs);
    }

    /**
     * Prints Verbose Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async verbose(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.verbose(logs);
    }

    /**
     * Prints Debug Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async debug(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.debug(logs);
    }

    /**
     * Prints Silly Log
     * @param {String} userId User's ID
     * @param {String} ip IP Address
     * @param {String} location Where the log is written
     * @param {String} procType The process where the log is written
     * @param {any} log Log details
     */
    async silly(userId, ipAddr, location, procType, log) {
        log = this.handleLog(log)
        let logs = this.createLogObject(userId, ipAddr, location, procType, log)
        this.logger.silly(logs);
    }

}

module.exports = new Logger();
```

Bu şekilde bir sınıf tanımlayarak, logları merkezi bir yerden kontrol etmeyi de sağlayabiliriz. Örneğin handleLog metodunda gelen logları kontrol edip hassas bilgileri filtreleme işlemleri yaptırabiliriz.

**Not:** Logger sınıfı _Singleton_ tasarım kalıbını(design pattern) kullanır. _Singleton_ tasarım kalıbı, en basit anlatımıyla bir sınıftan sadece bir nesne üretilebilmesini sağlar.

Logger sınıfını _main.js_'e import edip yukarıdaki loglamayı bir de bu şekilde yapalım.

```javascript
const logger = require("./lib/loggerClass");

logger.info("12345", "127.0.0.1", "main.js", "log", { req: { foo: "bar" } });
```

Böylece çok daha kolay ve anlaşılır bir log formatına sahip olduk. Ayrıca kod yazarken kullandığımız Editor/IDE artık metodun imzasını bize söyleyebilir oldu.

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-20-at-00.31.31.png)

Son olarak çıktımızı da görelim:

![_config.yml]({{ site.baseurl }}/assets/images/Screen-Shot-2022-01-20-at-00.34.20.png)

Bir yazımızın daha sonuna geldik. _Mutlu günler :)_

## Kaynaklar

- [https://github.com/winstonjs/winston](https://github.com/winstonjs/winston)
- [https://www.w3.org/TR/WD-logfile.html](https://www.w3.org/TR/WD-logfile.html)
