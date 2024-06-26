---
title: "Regex - Bayraklar (Flags/Options)"
date: "2022-01-20"
categories: 
  - "regex"
tags: 
  - "regex"
  - "regex-flags"
  - "regex-modifiers"
  - "regex-options"
---

[Regex - Giriş](https://endrcn.dev/regex/introduction-to-regex/) makalemizde düzenli ifadelere hızlı bir giriş yapmıştık. Bu makalemizde konuyu bir adım daha ileri götürüp bayraklardan(flags) bahsedeceğim. Başlamadan önce söylemeliyim ki bir ortama girip Regex bayrakları dediğinizde bunu muhtemelen kimse anlamayacaktır. Bu nedenle ben de Türkçe'ye çevirmeyip direkt flags ya da options ifadelerini kullanacağım.

Regex Flags ya da Regex Options, bir düzenli ifadeyi yazarken, yazdığımız ifadenin çalışma biçimini belirler. Bu cümle ile kafalar iyice karıştı. Bu nedenle direkt örnekler üzerinden adım adım gitmeyi daha uygun buluyorum.

## Nasıl Kullanılır?

Yazdığımız bir regex'e flag tanımlamak için regex'in sınırlarını oluşturan son / karakterinden sonra kullanmak istediğimiz flag'leri yerleştiririz.

```javascript
/[a-z]/g
```

Yukarıdaki örnekte **g** bir flag olarak eklenmiştir ve Global anlamını taşır. Tanımlayabileceğimiz options değerlerini bir tabloda görelim.

<table><tbody><tr><td>Flag Adı</td><td>Flag</td><td>Açıklama</td></tr><tr><td><strong><span style="text-decoration: underline;">g</span></strong>lobal</td><td>g</td><td>Tanımlanan regex ile tüm eşleşmeleri yakala.</td></tr><tr><td><strong><span style="text-decoration: underline;">i</span></strong>nsensitive</td><td>i</td><td>Büyük/küçük harf duyarsız hale getirir.</td></tr><tr><td><strong><span style="text-decoration: underline;">m</span></strong>ultiline</td><td>m</td><td>Birden fazla satırda regex taraması yapılmasını sağlar.</td></tr><tr><td><span style="text-decoration: underline;"><strong>u</strong></span>nicode</td><td>u</td><td>Unicode tanımlarının regex içinde kullanımına izin verir.</td></tr></tbody></table>

Regex Flags

Yukarıdakilerin dışında indices(d), sticky(y), single-line(s) gibi flag'ler olsa da şimdiye kadar hiç kullanmadığım için onlardan bahsetmeyeceğim.

## Global Flag (g)

Bir regex tanımladığımızda, eğer bir flag belirtmezsek tanımladığımız regex varsayılan olarak sadece bir eşleşme yapar. Regex'e Global flag eklediğimizdeyse tüm eşleşmeleri yakalamasını söylemiş oluruz. Örneğin sayıları yakalayan bir regex yazalım ve test edelim.

```javascript
/[0-9]+/
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-01.32.44.png)

Tanımladığımız regex ilk eşleşmeyi yakaladı ancak diğerlerine bakmadı. Bir de Global flag tanımlayarak bakalım.

/[0-9]+/g

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-01.38.16.png)

Bu kez tüm sayıları yakalayabildik.

## Insensitive Flag (i)

Tanımladığımız düzenli ifadeler, büyük-küçük harf duyarlıdır. Bu durumu tersine çevirmek için **i** flag'ini kullanırız.

```javascript
/fsm/g
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-01.56.58.png)

Küçük harflerle tanımlanmış **_fsm_** ifadesi, metindeki FSM ifadesiyle eşleşmedi. Eşleşmesi için i flag'ini ekleyelim.

```javascript
/fsm/gi
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-01.58.33.png)

Burada dikkat etmemiz gereken bir konu da insensitive flag'i Türkçe karakterleri kapsamadığıdır. Bu nedenle Türkçe karakterleri regex'te tanımlamak gerekir.

```javascript
/istanbul/gi
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-02.03.49.png)

İstanbul ifadesinin yakalanması için aşağıdaki gibi bir tanım yapmalıyız.

```javascript
/[iİ]stanbul/gi
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-02.04.39.png)

## Multiline Flag (m)

Multiline Flag, tüm satır sonlarının metnin sonu olarak görülmesini sağlar. Regex'te kullandığımız [$ karakterinin](https://endrcn.dev/regex/introduction-to-regex/#_karakteri-6) sadece metnin sonuyla değil tüm satırların sonuyla eşleşmeyi sağlar.

```javascript
/köprüsü/gi
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-02.15.49.png)

$ karakteri görevini yaptı ve sadece metnin sonundaki "köprüsü" ifadesiyle eşleşti. Ancak bizim istediğimiz tüm satırların sonundaki ifadelerle de eşleşmesi olursa multiline flag'ini kullanırız.

```javascript
/köprüsü/gim
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-02.17.26.png)

## Unicode Flag (u)

Unicode Flag, regex içinde unicode kullanmamızı sağlar. Unicode kullanarak; para birimleri, Çince harfler, Arapça sayılar, emojiler gibi karakterleri daha kolay yakalayabiliriz. Regex'te unicode karakterler kategorilere ayrılır. Bu kategorileri **_property_** kullanarak yakalayabiliriz. Property tanımlama işlemi için **\p{...}** yapısı kullanılır. Örneğin süslü parantezler arasında kullanacağımız L karakteri, herhangi bir dildeki bir harfi belirtir.

```javascript
/\p{L}/giu
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-21.13.54.png)

L karakteri Letter _property_'sinin kısaltmasıdır. Yukarıdaki ifadeyi \p{Letter} olarak da tanımlayabilirdik. Neredeyse tüm _property_'ler bir kısaltmaya sahiptir.

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-21.16.14.png)

Ana karakter kategorileri ve onların alt kategorileri:

- Letter `L`:
    - lowercase `Ll`
    - modifier `Lm`,
    - titlecase `Lt`,
    - uppercase `Lu`,
    - other `Lo`.
- Number `N`:
    - decimal digit `Nd`,
    - letter number `Nl`,
    - other `No`.
- Punctuation `P`:
    - connector `Pc`,
    - dash `Pd`,
    - initial quote `Pi`,
    - final quote `Pf`,
    - open `Ps`,
    - close `Pe`,
    - other `Po`.
- Mark `M` (accents etc):
    - spacing combining `Mc`,
    - enclosing `Me`,
    - non-spacing `Mn`.
- Symbol `S`:
    - currency `Sc`,
    - modifier `Sk`,
    - math `Sm`,
    - other `So`.
- Separator `Z`:
    - line `Zl`,
    - paragraph `Zp`,
    - space `Zs`.
- Other `C`:
    - control `Cc`,
    - format `Cf`,
    - not assigned `Cn`,
    - private use `Co`,
    - surrogate `Cs`.

Bir de alt kategorilerden _currency_ örneği yapalım.

```javascript
\p{Currency_Symbol}\d
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-21.35.07.png)

Bu ifadeyi kısaltma kullanarak daha kolay tanımlayabiliriz.

```javascript
\p{Sc}\d
```

![_config.yml]({{ site.baseurl }}/images/Screen-Shot-2022-01-20-at-21.36.40.png)

Regex'te önemli bir yere sahip olan _flag_'lerin sonuna geldik. Hangi programlama dilini kullanıyorsanız kullanın mutlaka Regex'e ihtiyaç duyacaksınız. Bu nedenle Regex öğrenmeye başlamak yazılım hayatınızda vereceğiniz en güzel kararlardan biri ve bu güzel kararın arkasından önemli bir konuyu bitirdiniz bile. _Tebrikler_ :)

## Kaynaklar

- [https://javascript.info/regexp-unicode](https://javascript.info/regexp-unicode)
- [https://melvingeorge.me/blog/check-if-string-contain-emojis-javascript](https://melvingeorge.me/blog/check-if-string-contain-emojis-javascript)
