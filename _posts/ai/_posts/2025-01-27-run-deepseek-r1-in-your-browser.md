---
layout: post
title: "🚀 Deepseek R1’i Tarayıcınızda Çalıştırın!"
author: endrcn
date: "2025-02-07"
categories: [ AI ]
image: assets/images/ai/run_deepseek_r1_in_your_browser.png
video: https://www.youtube.com/embed/3pbSAWleLVo?si=oXdS-NQVzz46VmYL
---

# Deepseek R1: Yapay Zekada Yeni Bir Çağ

Bugün yapay zeka dünyasına yeni bir soluk getiren **Deepseek R1** modelini konuşacağız! Bu model, özellikle kodlama, metin üretimi ve analitik yetenekleriyle dikkat çekiyor. Peki, onu diğer modellerden ayıran özellikler neler?

## Deepseek R1 Nedir?

**Deepseek R1**, açık kaynaklı ve büyük ölçekli bir dil modeli. Doğal dil işleme konusunda oldukça güçlü ve aynı zamanda programlama dillerinde de iddialı. **OpenAI**’nin modellerine benzer bir performans sergilerken, açık kaynak olmasıyla geliştiricilere büyük bir özgürlük sağlıyor.

### Deepseek R1’in Öne Çıkan Özellikleri:
✅ **Geniş Dil Anlayışı** – Birçok dili destekliyor ve doğal diyalog kurabiliyor.
✅ **Kodlama Yeteneği** – Python, JavaScript ve diğer dillerde güçlü.
✅ **Açık Kaynak** – İsteyen herkes inceleyebilir ve geliştirebilir.
✅ **Hızlı ve Optimize** – Hafif yapısıyla düşük donanımlarda bile verimli çalışabiliyor.

Yani bu model, sadece bir sohbet botu değil! **Hem yazılım geliştirenler için bir asistan hem de büyük veri işleyebilen bir analiz aracı.**

---

## Deepseek R1 7B Modelini WebLLM ile Kullanmak

Şimdi **Deepseek R1’in 7B modelini WebLLM ile kendi bilgisayarımızda nasıl çalıştırabileceğimize** bakalım.

### 1. Dosyalarımızı Oluşturalım

Öncelikle, HTML ve JavaScript dosyalarımızı oluşturuyoruz. Aşağıda verdiğim kodlar, WebLLM kullanarak Deepseek R1 modelini çalıştırmak için ihtiyacınız olan temel yapıyı oluşturacaktır.

### 2. HTML Yapısı

Aşağıdaki HTML kodu, **Bootstrap kullanarak bir mesajlaşma ekranı** oluşturur. Kullanıcı mesajları sağda, asistan mesajları solda görünecek şekilde tasarlanmıştır.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Deepseek R1</title>
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="d-flex flex-column vh-100">
    <div class="progress" style="height: 5px;">
        <div id="progress" class="progress-bar" role="progressbar" style="width:0%;" aria-valuemax="100" aria-valuemin="0"></div>
    </div>
    <div id="message-area" class="container-fluid d-flex flex-column-reverse flex-grow-1 overflow-auto"></div>
    <div class="input-group mt-auto">
        <input type="text" class="form-control" placeholder="Mesajınızı yazın..." id="query">
    </div>
    <script type="module" src="./script.js"></script>
</body>
</html>
```

### 3. JavaScript ile Modeli Kullanma

Şimdi de WebLLM ile **Deepseek R1 modelini entegre eden** JavaScript dosyamıza göz atalım.

```javascript
import * as webllm from './web-llm.js';

const queryInput = document.getElementById('query');
const progressbar = document.getElementById("progress");

const selectedModel = "DeepSeek-R1-Distill-Qwen-7B-q4f16_1-MLC";
const engine = new webllm.MLCEngine();

engine.setInitProgressCallback((report) => {
    progressbar.style.width = (report.progress * 100) + '%';
    queryInput.value = report.text;
});

async function initializeWebLLMEngine() {
    await engine.reload(selectedModel, { temperature: 1.0, top_p: 1 });
}

document.addEventListener("DOMContentLoaded", async function () {
    queryInput.disabled = true;
    await initializeWebLLMEngine();
    queryInput.disabled = false;
    queryInput.value = "";
});

queryInput.addEventListener('keyup', function (event) {
    if (event.key === "Enter") {
        sendMessage();
    }
});

async function sendMessage() {
    const query = queryInput.value;
    addMessageToMessageArea(query, "user");
    
    try {
        let id = addMessageToMessageArea("", "ai");
        await askToLLM(query, document.getElementById(id));
    } catch (err) {
        console.error(err);
    }
}

function addMessageToMessageArea(message, type) {
    const messageArea = document.getElementById('message-area');
    const div = document.createElement('div');
    div.className = `alert ${type === "user" ? "alert-secondary text-right" : "alert-primary text-left"}`;
    div.textContent = message;
    messageArea.appendChild(div);
}

async function askToLLM(query, outputElement) {
    queryInput.value = "";
    queryInput.disabled = true;
    outputElement.textContent = "Thinking...";
    
    const completion = await engine.chat.completions.create({ stream: true, messages: [{ role: "user", content: query }] });
    
    let responseText = "";
    for await (const chunk of completion) {
        responseText += chunk.choices[0].delta.content;
        outputElement.textContent = responseText;
    }
    
    queryInput.disabled = false;
}
```

### 4. Küçük Arayüz Güncellemeleri

- Model indirme sürecini göstermek için bir **progress bar** ekledik.
- Model yüklenirken giriş kutusunu devre dışı bıraktık.
- **Thinking...** ifadesi ekleyerek, modelin düşündüğünü kullanıcıya gösteriyoruz.
- Gelen mesajlardaki `\n` karakterlerini `<br>` etiketiyle değiştirerek, **daha okunaklı bir çıktı** elde ettik.

---

## Sonuç

Deepseek R1, **hem kodlama hem de doğal dil işleme yetenekleri** ile dikkat çeken, açık kaynaklı bir model. WebLLM entegrasyonu sayesinde, **kendi bilgisayarınızda hızlıca çalıştırabilir ve test edebilirsiniz**.

Bu rehberi takip ederek, siz de **kendi yapay zeka sohbet botunuzu** oluşturabilirsiniz. Deepseek R1 ile neler yapabileceğinizi keşfetmek için hemen denemeye başlayın!

