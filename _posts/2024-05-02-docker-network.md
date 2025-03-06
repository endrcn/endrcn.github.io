---
layout: post
title: Docker'da Farklı YAML Dosyaları Arasında Uygulama Haberleşmesi
author: endrcn
date: "2022-05-07"
categories: [ Apps ]
---

Docker, mikroservis mimarilerinde ve konteyner yönetiminde öncü bir araçtır. Ancak, farklı `docker-compose.yaml` dosyalarında tanımlanmış uygulamaların birbiriyle nasıl haberleşeceği, özellikle yeni başlayanlar için kafa karıştırıcı olabilir. Bu makalede, Docker'da farklı YAML dosyaları arasında uygulama haberleşmesini sağlamanın yöntemlerini adım adım açıklayacağız.


## Docker Ağları: Temel Kavramlar

Docker'da uygulamaların birbiriyle haberleşebilmesi için aynı ağda olmaları gerekir. Docker, bu amaçla üç temel ağ tipi sunar: `bridge`, `host` ve `overlay`. Çoklu konteyner yapılandırmalarında, `bridge` ve `overlay` ağları en yaygın kullanılanlardır.

## Docker Ağları: Temel Kavramlar

Docker'da uygulamaların birbiriyle haberleşebilmesi için aynı ağda olmaları gerekir. Docker, bu amaçla üç temel ağ tipi sunar: `bridge`, `host` ve `overlay`. Çoklu konteyner yapılandırmalarında, `bridge` ve `overlay` ağları en yaygın kullanılanlardır.

### Bridge Ağları

Varsayılan olarak, Docker bir `bridge` ağı oluşturur ve konteynerları bu ağa bağlar. Ancak, farklı `docker-compose.yaml` dosyalarında tanımlanan konteynerler varsayılan `bridge` ağı üzerinden birbirlerini göremezler. Bu sorunu çözmek için özel bir `bridge` ağı oluşturabilir ve her iki YAML dosyasındaki konteynerları bu ağa bağlayabilirsiniz.

#### Özel Bridge Ağı Oluşturma

1. Özel bir `bridge` ağı oluşturun:
```
   docker network create --driver bridge my_custom_network
````

2. Her iki `docker-compose.yaml` dosyasında, konteynerları bu ağa bağlayın:
```
   version: '3'
   services:
     my_service:
       image: my_image
       networks:
         - my_custom_network
   networks:
     my_custom_network:
       external: true
````

### Overlay Ağları

`Overlay` ağları, Docker Swarm kullanılarak dağıtılmış uygulamalar arasında haberleşme sağlar. Eğer uygulamanız birden fazla Docker host'u üzerinde çalışıyorsa, `overlay` ağları kullanarak farklı YAML dosyalarındaki uygulamalar arasında iletişim kurabilirsiniz.

## Docker Compose ile Ağ Yapılandırması

`docker-compose` aracı, birden çok konteynerin yönetimini ve ağ yapılandırmasını kolaylaştırır. Aşağıda, iki farklı `docker-compose.yaml` dosyasında tanımlanan servislerin nasıl aynı özel `bridge` ağında haberleşebileceğine dair bir örnek bulunmaktadır.

### Örnek Yapılandırma

**docker-compose.app1.yaml:**
```
version: '3'
services:
  app1:
    image: app1_image
    networks:
      - my_custom_network
networks:
  my_custom_network:
    external: true
```

**docker-compose.app2.yaml:**
```
version: '3'
services:
  app2:
    image: app2_image
    networks:
      - my_custom_network
networks:
  my_custom_network:
    external: true
```

Bu yapılandırmalar sayesinde, `app1` ve `app2` servisleri `my_custom_network` adlı özel `bridge` ağında birbirleriyle sorunsuz bir şekilde haberleşebilir.


## Sonuç

Docker'da farklı `docker-compose.yaml` dosyaları arasında uygulama haberleşmesini sağlamak, mikroservis mimarilerinde sıklıkla karşılaşılan bir gereksinimdir. Bu rehberde anlatılan adımlar ve örnek yapılandırmalar, farklı `docker-compose.yaml` dosyalarında tanımlı servislerin birbiriyle etkileşimini sağlama konusunda temel bir yol gösterici olacaktır.

Başta zor görünse de, doğru araçlar ve yöntemlerle kolaylıkla gerçekleştirilebilir. Bu makalede sunulan yöntemler, Docker'ın sunduğu ağ yapılandırma özelliklerinden yararlanarak, mikroservisler arası etkileşimi basitleştirmenize yardımcı olacaktır. Uygulamalarınızı Docker üzerinde geliştirirken, bu rehberin, etkili ve verimli bir yapılandırma için ihtiyacınız olan bilgileri sağladığını umuyoruz.

Docker ve konteyner teknolojileri hakkında daha fazla bilgi edinmek, daha karmaşık yapılandırmalar ve gelişmiş kullanım senaryoları için Docker'ın resmi belgelerini ve topluluk kaynaklarını incelemenizi öneririz. Teknoloji dünyasında sürekli bir öğrenme süreci olduğunu unutmayın; her yeni proje, yeni bir keşif fırsatıdır. Docker ile uygulama geliştirmenin keyfini çıkarın!