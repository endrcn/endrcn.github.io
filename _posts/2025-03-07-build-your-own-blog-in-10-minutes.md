---
layout: post
title: "10 Dakikada Kendi Blok Siteni Ücretsiz Oluştur"
author: endrcn
date: "2025-03-07"
categories: [ Apps ]
image: assets/images/apps/build_your_blog_in_10_minutes.png
video: https://www.youtube.com/embed/MP0X3v4V5A4?si=GYSSB1JkxGBaliRu
---

# GitHub Pages ile Ücretsiz Blog Kurma

Merhaba arkadaşlar! Bugün sizlerle birlikte **GitHub Pages** ve **Jekyll** kullanarak tamamen ücretsiz ve profesyonel bir blog sitesi nasıl kurulur, bunu adım adım inceleyeceğiz. Bu rehberde, hiçbir ücret ödemeden, kendi alan adınızı kullanarak veya GitHub'ın size sağladığı ücretsiz alan adıyla profesyonel bir blog sitesi oluşturmayı öğreneceksiniz.

Bu yöntem özellikle **yazılımcılar, öğrenciler veya sadece düşüncelerini paylaşmak isteyen herkes için** idealdir. Neden mi?

- **Tamamen ücretsiz**
- **Herhangi bir sunucu yönetimi gerektirmiyor**
- **Git ile versiyon kontrolü sağlıyor**
- **Markdown ile kolay içerik oluşturma imkanı sunuyor**
- **Minimal ve hızlı bir web sitesi elde ediyorsunuz**

Videoda oluşturacağımız blog sitesinin teması, **Wowthemes**'in MIT lisansıyla ücretsiz olarak sunduğu **Mediumish** teması olacaktır.

## 🚀 Başlangıç

### 1. Mediumish Temasını Forklayalım
Öncelikle [Mediumish temasının GitHub reposuna](https://github.com/wowthemesnet/mediumish-theme-jekyll) girerek **kendi hesabımıza forkluyoruz**.

### 2. GitHub Pages İçin Repo Adını Ayarlayalım
GitHub Pages kullanabilmek için repo adını **`endrcn.github.io`** olarak tanımlayalım. GitHub Pages, bu formatı otomatik olarak tanır ve sitenizi yayınlamanıza izin verir.

### 3. GitHub Pages Yayın Ayarları
- **Settings > Pages** alanına giriyoruz.
- **Deploy from a branch** seçeneğini işaretleyip,
- **Branch olarak `master` ve root** seçiyoruz.
- Böylece **master'a güncelleme yapıldığında site otomatik güncellenecek**.

### 4. VS Code ile Düzenlemelere Başlayalım
Şimdi repository'yi bilgisayarımıza **klonlayıp** **VS Code**'da açıyoruz.

- Tema ile birlikte gelen **örnek post'ları** bir-iki tanesi hariç siliyoruz.
- Daha önce yazdığım **MongoDB** ile ilgili post'u buraya kopyalıyorum.

### 5. Metadata Alanını Düzenleyelim
Post'un en üstündeki alan **metadata alanı**. Bu alanda **başlık, yazar, kategori** gibi temel bilgileri veriyoruz.

## 🔍 İlk Yayını Test Edelim
İlk yüklemeyi yapıp **test ettiğimizde sitenin açılmadığını gördük**. Demek ki bir hata yaptık. Hemen **post'u ve build loglarını inceliyoruz**.

- `_config.yml` dosyasında eksik veriler olduğunu fark ettik. **Güncelleyip tekrar commit attık**.
- **Hata devam ediyor**, metadata alanında **kategori** kısmının **dizi olarak tanımlanması gerektiğini fark ettik**. Düzeltip tekrar build ettik.
- **Build başarılı oldu, siteyi açalım!** 🎉

Ancak **dosyalar yüklenmemiş**! Sebebi `_config.yml` dosyasındaki `base_url` alanının yanlış verilmiş olması. **Onu düzeltiyoruz.**

### 6. Site Genel Ayarlarını Güncelleyelim
`_config.yml` dosyasında:

- **Sitenin adı, başlığı, açıklaması** gibi alanları düzenliyoruz.
- **Lazy Loading (`lazyimages: true`)** ayarını açarak **site hızını artırıyoruz**.
- **Cache temizliyoruz** ve **resimler düzgün yükleniyor!**

### 7. Menü ve Yazar Bilgilerini Düzenleyelim
- **Menüyü `_layouts/default.html` dosyasından düzenliyoruz**.
  - **Docs, WP Versions** gibi tema linklerini kaldırıyoruz.
  - **GitHub linkini** kendi hesabımızla değiştiriyoruz.
  - **YouTube kanal linkimizi ekliyoruz**.
- **Post ekranında yazar bilgileri görünmüyor**. `_config.yml` dosyasındaki **yazar alanını düzenleyerek** bu sorunu çözüyoruz.

### 8. Kendi Alan Adımızı Tanımlayalım
GitHub’ın ücretsiz verdiği **`endrcn.github.io`** adresi yerine **kendi domainimizi kullanmak için**:

- **Settings > Custom Domain** alanına **`endrcn.dev`** yazıyoruz.
- Alan adını aldığımız firmanın sitesinden **DNS ayarlarında `www CNAME`ini `endrcn.github.io`ya yönlendiriyoruz**.

Böylece artık **endrcn.dev** adresine gelen ziyaretçiler **blog sitemize erişebilecek**! 🎉

---

📢 **İçeriği beğendiyseniz [videoyu](https://www.youtube.com/watch?v=MP0X3v4V5A4) beğenmeyi ve kanala abone olmayı unutmayın!**