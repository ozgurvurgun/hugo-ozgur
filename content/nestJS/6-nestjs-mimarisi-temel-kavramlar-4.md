---
title: "NestJS Mimarisi: Global Modüller ve Kullanım Senaryoları (4. Bölüm)"
date: 2025-07-29
draft: false
weight: 6
---

## Global Modül Nedir?


NestJS'te bir modül normalde sadece import edildiği yerde kullanılır.  
Yani `UserModule` içinde tanımlı bir `UserService`, sadece onu import eden modüller tarafından erişilebilir.


Ama bazı modüller vardır ki:
> “Beni her yerden erişilebilir yap” demek ister.


İşte bu tür modüller için **global modül** kavramı vardır.


## Nasıl Tanımlanır?
Bir modülü global yapmak için `@Global()` dekoratörünü kullanırız.


```ts
// logger.module.ts
@Global()
@Module({
  providers: [LoggerService],
  exports: [LoggerService],
})
export class LoggerModule {}
```

Artık bu modülü `AppModule`'de bir kez import etmek yeterlidir:
```ts
@Module({
  imports: [LoggerModule],
})
export class AppModule {}
```
> Sonuç: LoggerService, başka hiçbir yerde LoggerModule'ü import etmene gerek kalmadan her yerden erişilebilir hâle gelir.

##  Nasıl Çalışır?

- Global olarak işaretlenen modüllerin provider’ları uygulama seviyesinde tek bir kez oluşturulur.
- Bu nedenle bunlar singleton davranır.
- NestJS iç dependency injection container’ında bu servisleri global scope’a koyar.


## Analoji

Global modülleri bir “ofis içi anons sistemi” gibi düşünebilirsin.

- Normalde her departman kendi asistanını kullanır.
- Ama bazı duyurular merkezi sistemden yapılır: yangın alarmı, tatil bildirimi, vs.


İşte global servisler böyle bir ortak bilgi kanalı gibi davranır.


## Peki Ne Zaman Kullanılır?

| Kullanım Durumu                 | Global Olmalı mı? |
| ------------------------------- | ----------------- |
| Logging servisi                 | Evet            |
| Config servisi (ortak ayarlar)  | Evet            |
| Cache servisi                   | Duruma göre    |
| Database bağlantısı (singleton) | Dikkatle       |
| UserService                     | Hayır           |
| Domain'e özgü özel servisler    | Hayır           |


> Özet: Cross-cutting concerns dediğimiz sistem geneli servislerde kullanılır: log, auth, config gibi.


## Neden Her Şey Global Yapılmamalı?

Bu çok kritik bir nokta.

1. Test edilebilirlik düşer.
Test sırasında modüller izole edilemez hâle gelir.

2. Proje büyüdükçe bağımlılıklar belirsizleşir.
Hangi modül neye bağlı, neyi nerede override etmişsin belli olmaz.

3. Yanlışlıkla override edersen sistemin her yeri bozulabilir.
Bir modülü global tanımlayıp, başka yerde onun provider’ını tekrar tanımlarsan farkında olmadan sistemi etkileyebilirsin.


## Bir Anti-Pattern: Her Modülü Global Yapmak

Şunu yapanlar var:
```ts
@Global()
@Module({ providers: [UserService] })
export class UserModule {}
```

> "Kolay erişilsin diye her şeyi global yaptım."
Hayır. Bu, dependency injection’ın ruhuna aykırıdır.

Her modül, sadece kullanacağı servislere bağımlı olmalı.
Tersine, her şeyi her yerden erişilebilir yapmak proje mimarisinin içinden geçer.


## Alternatif: SharedModule (Yarı-global kullanım)

Eğer bazı servisleri birçok modül kullanacak ama global yapmak istemiyorsan `SharedModule` kalıbı iş görür.

```ts
@Module({
  providers: [CommonService],
  exports: [CommonService],
})
export class SharedModule {}
```

Sonra hangi modülde gerekiyorsa oraya import edersin:
```ts
@Module({
  imports: [SharedModule],
})
export class OrdersModule {}
```

Bu sayede:
- Global kaos yaşamazsın
- Hangi modül neyi kullandı şeffaf olur
- Test yazmak daha kolay olur

## Özet

- `@Global()` -> Bir modülü tüm projeye tek seferde erişilebilir kılar
Singleton olarak davranır
- Config, Logger gibi sistem düzeyi servisler için mantıklıdır
- Ama domain-specific servislerde kullanmak mimariyi zayıflatır
- Alternatif olarak `SharedModule` gibi çözümler önerilir