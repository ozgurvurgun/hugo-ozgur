---
title: "Giriş Temel Bİlgiler"
date: 2025-07-29
draft: false
weight: 1
---


## NestJS'e Giriş: Neden Yeni Bir Şeye Daha İhtiyacımız Var?

Node.js harika. JavaScript'in gücünü sunucu tarafına taşıdı, “Aynı dili hem frontend'de hem backend'de kullanma” rüyasını gerçeğe dönüştürdü. Ama ne zaman ki işler büyüdü, bu özgürlük başımıza bela olmaya başladı. Çünkü:


- Herkes her şeyi her yerde yapabiliyordu.
- Proje büyüdükçe dosyalar çöplüğe dönebiliyordu.
- Kod tekrarları, karışık business logic, zor test edilebilir yapılar ortaya çıkıyordu.


İşte NestJS burada sahneye çıkıyor.


## NestJS Nedir?

NestJS, TypeScript ile yazılmış, modüler bir backend framework'üdür. Express ya da Fastify üzerine kuruludur, ama bunları soyutlayarak bize **Angular benzeri bir yapı** sunar.

> ✅ Yani hem OOP (nesne yönelimli), hem fonksiyonel, hem de reaktif programlama desenlerini destekler.


## Bir Analojiden Gidelim

NestJS'i bir şehir olarak hayal et. Node.js kendi başına düzensiz, küçük bir kasaba olabilir:  
- Herkes evini kafasına göre yapmış  
- Su tesisatı kimin nereye bağlandığı belli değil  
- Sokak isimleri yok


Ama NestJS devreye girdiğinde şöyle oluyor:
- Şehir planlaması var (modülerlik)  
- Her binanın görevi belli (Controller, Service, Module)  
- Altyapı merkezi ve kontrol edilebilir (Dependency Injection, Pipe, Guard vs.)  
- Yeni biri gelip kodu rahatça anlayabilir (test edilebilirlik ve yapı)


## Peki Neden TypeScript?

NestJS, TypeScript ile yazılmıştır çünkü:
- Büyük projelerde tip güvenliği hayat kurtarır.
- Refactor işlemleri çok daha güvenlidir.
- Kod kendi kendini belgelemeye başlar (autocompletion, IDE destekleri vs.)
- Hataları çalıştırmadan önce yakalayabilmek büyük avantajdır.


Eğer TypeScript'e yeniysen:

> - Tip tanımları, enumlar, interface'ler, class yapıları seni biraz zorlayabilir ama NestJS'de bu yapılar çok anlaşılır bir şekilde kullanılıyor.  
> - Birkaç pratikle elin alışacak.


## Express Mi, Nest Mi?


| Özellik             | Express              | NestJS                        |
|---------------------|----------------------|-------------------------------|
| Yapılandırma        | Serbest, düzensiz    | Katı ve düzenli               |
| Öğrenme eğrisi      | Düşük                 | Orta                          |
| Büyük projeye uygunluk | Düşük              | Yüksek                        |
| TypeScript uyumu    | Harici eklentiyle    | Doğuştan (built-in)           |
| Modüler yapı        | Yok                  | Var                           |


## NestJS Kullanmak Ne Kazandırır?

-  Daha sürdürülebilir projeler
-  Daha az teknik borç
-  Takımca çalışmaya uygun yapı
-  Daha az "bu fonksiyon nerede tanımlanmıştı ya?" sorusu


## Başlamadan Önce Bilmen Gerekenler

NestJS’e başlamadan önce şu konulara aşina olman işini kolaylaştırır:

- **Node.js nedir ve nasıl çalışır?**
  - Event loop, async yapılar, NPM modülleri<br/><br/>
- **TypeScript'e temel düzeyde hâkimiyet**
  - Interface, type, class, enum
  - Fonksiyonlara tip tanımlamak
  - Modül sistemi (`import`, `export`)<br/><br/>
- **Object-Oriented Programming**
  - `class`, `constructor`, `this`
  - Kalıtım (`extends`), soyutlama, polimorfizm