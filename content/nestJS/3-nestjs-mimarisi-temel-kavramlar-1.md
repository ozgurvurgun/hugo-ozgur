---
title: "NestJS Mimarisi: Modüller ve Temel Kavramlar (1. Bölüm)"
date: 2025-07-29
draft: false
weight: 3
---


## Neden Modül?

Küçük projelerde her şeyi tek dosyada yazmak kolaydır. Ama işler büyüyünce işler sarpa sarar. Kod karmaşıklaşır, neyin nerede olduğunu bulmak zorlaşır. İşte burada **modüller** devreye girer.

NestJS, Angular'dan aldığı ilhamla “her şey bir modülün içinde” prensibiyle çalışır.

> Bir modül, belirli bir işlevi yerine getiren yapıları gruplar: controller'lar, service'ler, provider'lar...


## 1. Modül Nedir?

NestJS’te her yapı bir modülün içinde yaşar.  
Modül; bağımsız, izole, bir iş parçasını temsil eden bir yapıdır.


## Analoji

Bir otelin her katını bir “modül” gibi düşün:  

1. kat -> rezervasyon modülü  
2. kat -> ödeme modülü  
3. kat -> oda servisi modülü

Her kat kendi işini yapar ama gerektiğinde birbiriyle iletişim kurabilir.


## 2. Root Module ve Feature Module Farkı

NestJS projeleri her zaman bir **Root Modül (kök modül)** ile başlar: `AppModule`. Bu modül uygulamanın giriş noktasıdır. Ana çatı gibi düşünebilirsin.


Örnek:

```ts
@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Peki Feature Module nedir?

Feature Module, belirli bir işlevi izole etmek için oluşturulan ek modüllerdir.
Mesela bir `UserModule`, sadece kullanıcı işlerini halleder.


```txt
src/
├── app.module.ts       # Root modül
├── user/
│   ├── user.module.ts  # Feature modül
│   ├── user.service.ts
│   └── user.controller.ts
```

Bu sayede:
- Kod okunabilirliği artar
- Ekip çalışması kolaylaşır
- Modüller bağımsız test edilebilir hâle gelir


## 3. @Module Dekoratörü

Her modül bir sınıfla temsil edilir ve bu sınıfa `@Module` dekoratörü eklenir.

```ts
@Module({
  imports: [],          // Başka modüller buraya eklenir
  controllers: [],      // HTTP isteklerini yöneten sınıflar
  providers: [],        // Servisler, helper'lar
  exports: []           // Dışa açmak istediklerin
})
export class UserModule {}
```

Bu yapı aslında NestJS’in dependency injection sistemine de temel oluşturur. Her şey bu dekoratörle tanıtılır.


## 4. Modül Oluşturma ve Yapılandırma

Nest CLI üzerinden modül oluşturmak çocuk oyuncağı:

```bash
nest generate module user
# veya kısa hali:
nest g mo user
```

Bu komut:
```txt
src/user/user.module.ts
```
dosyasını oluşturur.

Ardından bir controller ve service ekleyelim:
```bash
nest g controller user
nest g service user
```

Şu yapı oluşur:
```txt
src/user/
├── user.controller.ts
├── user.module.ts
└── user.service.ts
```

UserModule içeriği:
```ts
@Module({
  controllers: [UserController],
  providers: [UserService],
})
export class UserModule {}
```

Şimdi bunu `AppModule` içine dahil etmeliyiz(cli otomatik yapıyor zaten):

```ts
import { UserModule } from './user/user.module';

@Module({
  imports: [UserModule], // Artık UserModule kullanılabilir
})
export class AppModule {}
```

> Böylece modüller arasında “görünürlük” sağlanır. AppModule -> UserModule'u kullanabilir hâle gelir.
