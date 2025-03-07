---
layout: post
title: "Youtube Videosuna Yapay Zeka ile Dublaj Nasıl Yapılır?"
author: endrcn
date: "2025-02-07"
categories: [ AI ]
image: assets/images/ai/how_to_dub_your_video_with_ai.png
video: https://www.youtube.com/embed/VNTYwWi8qAU?si=sNMfTIT9YhZ8teu5
--- 

Herkese merhaba, bugün YouTube videolarınıza yapay zekâ kullanarak nasıl ücretsiz dublaj yapabileceğimizi konuşacağız.  

## **SoniTranslate ile Dublaj İşlemi**  

Github'ta tamamen açık kaynaklı olarak paylaşılan **SoniTranslate** Reposu, **Apache 2.0** lisansıyla hem kişisel hem de ticari olarak kullanılabilir.  

Bir web arayüzü de sağlayan **SoniTranslate**, sadece YouTube videonuzun URL'ini vererek dublaj yapmanıza olanak tanıyor.  

Çeviri işlemi için **Türkçe’nin de içinde bulunduğu toplam 84 dili destekliyor!**  

Hadi gelin bu web uygulamasının arayüzünü kullanarak, tamamen **ücretsiz** bir şekilde bir videoyu nasıl dublajlayacağımızı görelim.  

## **Google Colab ile Kurulum**  

Tüm işlemlerimizi **Google Colab** üzerinden yapacağız. Eğer üyeliğiniz yoksa, **Gmail hesabınız** ile kolayca kayıt olabilirsiniz.  

İlk komutları çalıştırmak için kod bloğunun **Run** butonuna basıyoruz.  

Bize **"Bu notebook, Google tarafından oluşturulmadı"** şeklinde bir uyarı gelecektir, **"Çalıştır"** butonuna basarak devam ediyoruz.  

Gerekli **repo ve kütüphaneler** indirilip kuruluyor. Bu işlem yaklaşık **7 dakika** sürecektir.  

Kurulum tamamlandıktan sonra, uygulamanın kullandığı **modeller için HuggingFace üzerinden model lisanslarını kabul etmemiz gerekiyor**.  

Lisans kabul işlemi için:  
1. HuggingFace’e giderek model lisanslarını kabul ediyoruz.  
2. HuggingFace üzerinden bir **token üretiyoruz**.  
   - Burada dikkat edilmesi gereken en önemli nokta: **"Read Access to contents of all public gated repos you can Access"** izninin verilmesi.  

Oluşturduğumuz **token**'i uygulamaya giriyoruz ve komutları çalıştırıyoruz.  

İsterseniz **arayüz dilini ve temasını** da değiştirebilirsiniz.  

## **Dublaj İşlemi Adım Adım**  

Tüm işlemler tamamlandıktan sonra, bize **Gradio tabanlı bir arayüz linki** veriliyor.  

Bu link ile uygulamaya erişebiliriz.  

### **Videoyu Yükleme**  
- **YouTube linki** girerek veya **video yükleyerek** dublaj işlemi yapılabilir.  
- Ben **YouTube URL’sini kullanıyorum**.  

### **Dublaj Ayarları**  
- **Videonun dilini belirtiyoruz** → Türkçe  
- **Çeviri dilini seçiyoruz** → İngilizce  
- **Videoda kaç kişinin konuştuğunu belirliyoruz**  
- **Seslendirme için bir erkek sesi seçiyoruz**  
- **Ses tonlamalarına uygun dublaj için "Active Voice Imitation" özelliğini aktifleştiriyoruz**  
- **openvoice_v2 modelini kullanarak çalıştırmasını istiyoruz**  

### **Gelişmiş Ayarlar (Advanced Settings)**  
- **Max audio acceleration**: **2.4** (Sesin videoya göre hızlanıp yavaşlamasını kontrol eder)  
- **Volume original audio**: **0** (Orijinal videonun sesini kapatır)  
- **Dublaj sesi yüksekliği**: **2.4**  
- **Soft subtitles (Altyazı aç/kapat özelliği)** → Aktif  
- **Whisper ASR Modeli**: **OpenAI large-v3**  

Son olarak oluşturulacak **dosyanın formatını** belirliyoruz:  
- **Ses dosyası almak için MP3 formatını seçiyorum**.  

### **Dublaj İşlemi ve Çıktılar**  

Arayüzde en üste çıkıp **Translate** butonuna basarak işlemi başlatıyoruz.  

**Çıktı:**  
- **Ses dosyası**  
- **Altyazı dosyası**  

### **Altyazı Kontrolü ve Düzeltme**  

Altyazıyı incelediğimizde **6:10 - 6:17** arasında modelin hata yaptığını görüyoruz.  

Bu yüzden:  
1. **Hatalı kısmı altyazı dosyasından siliyoruz ve kaydediyoruz**.  
2. **Düzeltilmiş altyazı dosyasını uygulamaya tekrar yükleyip dublajı yeniden oluşturuyoruz**.  

Son olarak **yeni ses dosyamızı kontrol ediyoruz**.  

## **Dublaj Sesini Videoya Ekleme**  

Şimdi elde ettiğimiz ses dosyasını **iMovie** kullanarak videomuza ekleyelim:  
1. **Orijinal videonun sesini "Detach audio" özelliği ile ayırıp siliyoruz**.  
2. **Yeni dublaj sesini videoya hizalıyoruz**.  
3. **Export işlemiyle videoyu tamamlıyoruz**.  

Sonuç olarak **Türkçe olan videomuzu İngilizce olarak dublajlamış olduk!** 🎉  

## **Sonuç**  

**SoniTranslate ile tamamen ücretsiz bir şekilde YouTube videolarınıza dublaj yapabilirsiniz!**  

İçeriği beğendiyseniz **videoyu beğenmeyi ve kanala abone olmayı unutmayın!** 🚀