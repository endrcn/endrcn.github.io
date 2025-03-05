---
title: "Node.js - Asenkron Fonksiyonlar (Asynchronous Functions)"
date: "2022-01-04"
layout: post
author: endrcn
categories: [NodeJS]
tags: [async, async-await, callback, promise, nodejs, node.js]
---

[Node.js Temelleri](https://endrcn.dev/nodejs-introduction/) yazı dizisinde bu makalemizin konusu **Asenkron Fonksiyonlar**. Daha önce [fonksiyonlar](https://endrcn.dev/nodejs-functions/) konusuna değinmiştik şimdiyse konuyu biraz daha ileri düzeye taşıyalım. Ancak Asenkron fonksiyonları incelemeye başlamadan önce buna neden ihtiyaç duyulduğunu anlamak gerek.

Node.js, Javascript kodlarını sadece bir thread'de(single thread) çalıştırır. Yani bir seferde sadece bir şey oluyor. Bu nedenle uygulamayı bloklayacak kod parçalarından kaçınmamız gerekir. Örneğin senkron şekilde bir dosya okuma ya da sonsuz bir döngü. Bu tarz durumlarda node.js tek thread çalıştığı için bloklanacak ve cevap veremez hale gelecektir.

Şimdiye kadar gördüğümüz tüm fonksiyonlar senkron yapıya sahiptir. Bu da aslında kodumuzu blokladığı anlamına gelir. Ancak çok çok küçük zaman aldıkları için bloklama hissetmiyoruz. Ancak yine de büyük verilerde yapacağımız işlerde kullanacağımız fonksiyonlara dikkat etmemiz gerekir. Aksi halde uygulamamızın cevap süresi uzayacaktır.

Event-loop mimarisi, yazdığımız kodu single thread'de efektif bir biçimde çalıştırmamızı sağlar. Aşağıda Node.js'in nasıl çalıştığını anlatan bir görsel paylaşıyorum ve bunu iyice sindirmenizi tavsiye ediyorum.

![_config.yml]({{ site.baseurl }}/assets/images/nodejs_architecture.jpeg)

Şimdi gelelim asıl konumuz olan asenkron fonksiyonlara. Node.js'te birçok modül asenkron fonksiyonlar barındırır. Bu makalemizde bir modül kullanmadan Node.js ile birlikte gelen asenkron timer fonksiyonlarını kullanacağız.

Öncelikle ekrana sırayla hello ve world yazan bir uygulama geliştirelim.

```javascript
console.log("hello");
console.log("world");
/*
Output:
hello
world
*/
```

Çıktı tamamen senkron bir şekilde sırayla çalışacaktır. Önce hello ardından world yazılacaktır. Peki bunu asenkron bir fonksiyonla yapsaydık?

```javascript
setTimeout(()=>{
    console.log("hello");
},1000);
console.log("world");
/*
Output:
world
hello
*/
```

Bu kez çıktı kod yazma sıramıza göre oluşmadı. setTimeout metodu asenkron bir fonksiyon olduğu için uygulamayı bloklamadı ve önce world yazısını ekrana bastı ardından hello mesajını bastı.

_**Not:** setTimeout metodu 2 parametre alır. Bunlardan ilki callback function, ikinci parametre ise ilk parametrede verilen callback fonksiyonun ne zaman çağrılacağının milisaniye cinsinden değeridir._

Asenkron fonksiyonlarda iki farklı dönüş yapısı vardır. Callback ve Promise. Bu yapılara yakından bakalım.

## Callback

Callback, asenkron fonksiyonlarda kullanılan ilk [dönüş yöntemidir](https://endrcn.dev/nodejs-functions/#Callback). Bir fonksiyon parametresi olarak verilir ve asenkron fonksiyonda işlemler yapıldıktan sonra callback fonksiyonu çağrılır. Yukarıdaki örnekte setTimeout fonksiyonundaki ilk parametre de callback fonksiyona bir örnektir. Şimdi setTimeout asenkron fonksiyonunu kullanan başka bir fonksiyon yazalım.

```javascript
function run(callback) {
    setTimeout(() => {
        callback("hello");
    },10);
}

run((result) => {
    console.log(result)
})

console.log("world");
/*
Output:
world
hello
*/
```

run() metodu, callback alan bir asenkron fonksiyondur. Böylece fonksiyonumuz uygulamamızı bloklamadan çalışıyor. Callback fonksiyonların bir dezavantajı var. O da callback hell diye adlandırılan duruma düşmemiz ve kodun anlaşılırlığının zorlaşmasına sebep olmasıdır. Callback hell, en basit açıklamasıyla iç içe callback'lerin çağrılmasıyla oluşan durumdur. Örneğin üç adet asenkron fonksiyonumuz ve bu asenkron fonksiyonların sırayla çalışmasına ihtiyacımız olsun.

```javascript
// Fonksiyon tanımlama aşaması
function sayHello(cb) {
    setTimeout(() => {
        cb("Hello,");
    }, 2000);
}

function sayEnder(cb) {
    setTimeout(() => {
        cb("Ender");
    }, 1000);
}

function sayCan(cb) {
    setTimeout(() => {
        cb("CAN");
    }, 500);
}

// Çalıştırma aşaması
sayHello((result) => {
    console.log(result);
    sayEnder((ender) => {
        console.log(ender);
        sayCan((can) => {
            console.log(can);
        })
    })
})

/*
Output:
Hello,
Ender
CAN
*/
```

Yukarıdaki örnekte de gördüğünüz gibi üç satırlık kod büyüdü büyüdü büyüdü. Çok daha fazla callback'i iç içe kullanmak zorunda kaldığımız yapılarla karşılaşabiliyoruz. Bu nedenle yönetimi ve anlaşılması oldukça zor kod parçaları ortaya çıkıyor. Bu durumu çözmek adına ortaya Promise yapıları çıktı. Şimdi onu inceleyelim.

## Promise

Callback hell yapılarının can sıkıcı durumundan ötürü Promise yapıları doğmuştur. Promise, bir fonksiyonun asenkron olarak dönüş yapmasını sağlar. Basit bir fonksiyon üzerinden yapısını görelim.

```javascript
function returnPositiveNumber(number) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (typeof number === "number") {
                if (number > 0) {
                    resolve(number)
                } else {
                    resolve(0);
                }
            } else {
                reject(new Error("number parameter is not a number"));
            }
        },1000);
    })
}
```

Yukarıda Promise dönen bir fonksiyon tanımı örneği yaptık. Adım adım açıklayalım.

- return ile bir promise nesnesi döner. Bu promise nesnesi parametreleri resolve ve reject olan bir fonksiyon parametresi alır.
- resolve ve reject de aslında birer fonksiyondur.
- **resolve()**, fonksiyon içinde yapılan işlem başarılı ise dönülür. Parametre olarak dönüş değerini alır.
- **reject()**, fonksiyon içinde yapılan işlemde bir hata olması durumunda dönülür. Parametre olarak hata bilgisini alır.

Şimdiye kadar tanımlamayı gördük. Peki bunu nasıl kullanacağız? Eğer direkt çalıştırırsak dönüş değerinin Promise olduğunu görebiliriz.

```javascript
console.log(returnPositiveNumber(5)); // Output: Promise { <pending> }
```

Promise dönen bir asenkron fonksiyonun dönüşünde then ve catch fonksiyonları kullanılır. Dönüş değeri bir nesne olduğu için, then ve catch fonksiyonları da nokta(.) operatörü ile çağrılır.

```javascript
returnPositiveNumber(5).then((result) => {
    console.log(result);
}).catch(err => {
    console.error(err);
})
// Output: 5
```

- then() metodu, fonksiyon **resolve()** ile döndüğünde tetiklenir.
- catch() metodu ise fonksiyon **reject()** ile döndüğünde tetiklenir.

Bir de catch metodunun çalıştığı hali görelim:

```javascript
returnPositiveNumber("ender").then((result) => {
    console.log(result);
}).catch(err => {
    console.error(err);
})
// Output: Error: number parameter is not a number
```

### Promise Zinciri (Promise Chain)

Promise zinciri, Promise dönen fonksiyonların uç uca bağlanmasıyla oluşturulan yapıdır. Bu yapıyı oluşturabilmek için her then bir sonuç dönmelidir.

```javascript
returnPositiveNumber(5).then((result) => {
    console.log(result); // Output: 5
    return result*result;
}).then((result)=>{
    console.log(result); // Output: 25
    return result*result;
}).then((result)=>{
    console.log(result); // Output: 625
    return result*result;
}).catch(err => {
    console.error(err);
})
```

Ya da Promise'in resolve fonksiyonunun dönüşünde yine promise dönen başka bir fonksiyon gönderilmelidir. Bu örnek için daha önce tanımladığımız returnPositiveNumber() fonksiyonunun yanına bir de returnNegativeNumber() fonksiyonu tanımlayalım.

```javascript
function returnNegativeNumber(number) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (typeof number === "number") {
                if (number < 0) {
                    resolve(number)
                } else {
                    resolve(0);
                }
            } else {
                reject(new Error("number parameter is not a number"));
            }
        }, 1000);
    })
}
```

Şimdi bu iki fonksiyonu peşpeşe ekleyip bir Promise Zinciri oluşturalım.

```javascript
returnPositiveNumber(5)
.then(returnNegativeNumber)
.then((result) => {
    console.log(result); // Output: 0
})
.catch(err => {
    console.log(err);
})
```

Yukarıdaki işlemi açıklayalım

- returnPositiveNumber() fonksiyonu 5 parametresi ile çağrılıyor.
- Ardından then içinde returnNegativeNumber fonksiyonu çağrılıyor. Bu fonksiyonun parametresi, returnPositiveNumber fonksiyonundan dönen değerdir.
- Ardından gelen then metodu ise returnNegativeNumber fonksiyonunun çıktısıdır.

**Not:** catch fonksiyonu, kaç tane then olursa olsun her zaman bir kez ve en sonda tanımlanmalıdır.

## Async / Await

Async/Await yapısı, Promise yapılarının da çok fazla tanımlara boğmasından doğmuş bir ihtiyaçtır. Evet Promise bizi callback hell'den kurtardı ancak bunu yapabilmemiz için hayatımıza birden fazla dönüş tipi ve metot soktu. Şimdi async / await yapısını biraz tanıyalım.

- **async** anahtar kelimesi, fonksiyon tanımlamak için kullanılır.
- **await** anahtar kelimesi, async ya da Promise dönen bir fonksiyonu çağırmak için kullanılır.

Kullanım şeklini görelim.

```javascript
// Syntax
async function methodName(number) {
    return number;
}
```

Yukarıdaki örneği incelediğimizde, normal bir fonksiyon tanımlamaktan tek farkı tanımın başındaki async anahtar kelimesi olduğunu görebiliriz. Yukarıdaki returnPositiveNumber() fonksiyonunu bir de async ile tanımlayalım.

```javascript
async function returnPositiveNumber(number) {
    if (typeof number === "number") {
        if (number > 0) {
            return number;
        } else {
            return 0;
        }
    } else {
        throw new Error("number parameter is not a number");
    }
}
```

Aynı Promise dönen fonksiyonda yaptığımız gibi fonksiyonu direkt çağırıp loglayalım ve sonucu görelim.

```javascript
console.log(returnPositiveNumber(5)); // Output: Promise { 5 }
```

Aynı Promise dönen fonksiyonda olduğu gibi burada da çıktımız Promise oldu. Bunun sebebi; async anahtar kelimesi kullanıldığında aslında fonksiyonumuzu bir Promise fonksiyonuna dönüştürüyor olmasıdır. Buradan yola çıkarak then ve catch metodlarını da kullanabileceğimizi söyleyebiliriz.

```javascript
returnPositiveNumber(5).then(result => {
    console.log(result);
}).catch(err => {
    console.log(err);
});
// Output: 5
```

Bir async fonksiyonun nasıl tanımlandığını gördük. Peki await nasıl çalışır? **await**, temelde then ve catch metotları yerine kullanılan bir anahtar kelimedir. Bu nedenle await kullanabilmemiz için fonksiyonun **Promise dönmesi** gerektiğini söyleyebiliriz. Son bir kuralımız da await anahtar kelimesini kullanabilmek için mutlaka async tanımlanmış bir fonksiyon içinde olmalıyız. Aksi halde [SyntaxError hatası](https://endrcn.dev/nodejs-error-handling/) fırlatılacaktır.

```javascript
await returnPositiveNumber(5); // Output: SyntaxError: await is only valid in async function
```

Bir de doğru kullanım örneği görelim.

async function run() {
    let result = await returnPositiveNumber(5);

    console.log(result); // Output: 5
}
run();

Yukarıdaki örnek, then metodunun çalışmasına karşılık gelir. Catch metodunun çalışmasının karşılığı ise try-catch ifadelerinin kullanımı ile sağlanır.

```javascript
async function run() {
    try {
        let result = await returnPositiveNumber("ender");
        console.log(result);
    } catch (err) {
        console.error(err.message);
    }
}
run();
//Output: number parameter is not a number
```

Yukarıdaki kod bloğu async/await yapısının en doğru kullanımıdır diyebilirim. Çünkü eğer bir hata fırlatılır ve bu yakalanmazsa [Hata Yakalama (Error Handling)](https://endrcn.dev/nodejs-error-handling/) konusunda anlattığımız gibi Node.js uygulaması çöker.

**_Burada önemli bir konuya değinmek istiyorum_**. Async fonksiyonlar içinde eğer callback içeren fonksiyonlarla işlem yapacaksak bu durumda Promise yapısını kullanmamız gerekir. Bunun nedeni, async fonksiyon tanımında dönüşü direkt return ile yapmamızdır. Eğer bir callback function içinde return ile dönersek bu return callback fonksiyonunun return'u olacaktır. Bu durumda da async fonksiyonun return'u çalışmayacağı için fonksiyon cevap veremez.

Yukarıdaki async ile tanımlanmış returnPositiveNumber fonksiyonunu tekrar incelerseniz içinde setTimeout fonksiyonunun olmadığını görebilirsiniz. Bunun sebebi aslında async fonksiyonu anlatabilmektir. Eğer async fonksiyon içinde setTimeout kullanmaya çalışsaydık şöyle bir hatalı kod yazmış olacaktık.

```javascript
async function returnPositiveNumber(number) {
    setTimeout(() => {
        if (typeof number === "number") {
            if (number > 0) {
                return number;
            } else {
                return 0;
            }
        } else {
            throw new Error("number parameter is not a number");
        }
    }, 1000);
}
```

Bu kod parçasında işaretlediğim alanlara dikkat edelim.

- 2. satırda; setTimeout fonksiyonunun dönüş değeri için [arrow function](https://endrcn.dev/nodejs-functions/#Arrow_Functions) yapısı ile bir callback fonksiyonu tanımladık.
- 5. ve 7. satırlardada; return ile sayıyı döndürdük. Fakat bu return, callback fonksiyonunun içinde olduğu için aslında callback fonksiyonun dönüşünü yapmış olduk.
- Sonuç olarak returnPositiveNumber fonksiyonunun dönüşü tanımlanmamış oldu ve çağrılan fonksiyon dönüş değeri tanımlanmadığı için aslında [Void](https://endrcn.dev/nodejs-functions/#Void) bir fonksiyon oldu.
- Eğer bu fonksiyonu çağıracak olursak dönüş değeri **_undefined_** olacaktır.

```javascript
returnPositiveNumber(5).then(console.log) // Output: undefined
```

Toparlayacak olursak; eğer bir fonksiyonun içinde callback dönüş tipine sahip başka bir fonksiyon çalıştırıyorsak, tanımladığımız fonksiyon callback ya da Promise ile dönmelidir. Burada async anahtar kelimesini kullanmamalıyız.

Bir sonraki konumuz tüm yazılım hayatınız boyunca kullanacağınız Regex konusuna giriş olacak. Kaçırmayın derim :)
