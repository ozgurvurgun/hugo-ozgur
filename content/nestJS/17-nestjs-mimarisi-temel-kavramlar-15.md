---
title: "NestJS Mimarisi: Provider Scope (15. Bölüm)"
date: 2025-07-31
draft: false
weight: 17
---


## Bir Servis, Herkes İçin Aynı mıdır?

NestJS’te `@Injectable()` bir sınıf tanımladığında, varsayılan olarak bu servis singleton olur:

> Tüm uygulama boyunca sadece 1 kez oluşturulur ve herkes aynı örneği kullanır.

Ama bu her zaman ideal değildir.


Bazı durumlarda:

- Her HTTP isteği için yeni bir örnek gerekebilir (örneğin kullanıcıya özel veri taşımak)
- Her dependency injection noktası farklı bir örnek almalıdır

İşte burada `scope` kavramı devreye girer.


## 3 Temel Scope Türü

| Scope       | Yaşam Süresi                 | Kullanım Senaryosu                           |
| ----------- | ---------------------------- | -------------------------------------------- |
| `SINGLETON` | Tüm uygulama boyunca 1 örnek | Varsayılan, çoğu servis bu olur              |
| `REQUEST`   | Her HTTP isteği için yeni    | Kullanıcıya özel veri tutan servisler        |
| `TRANSIENT` | Her kullanımda yeni örnek    | Paylaşılmaması gereken servisler (stateless) |


## 1. Singleton (Varsayılan)

```ts
@Injectable()
export class UserService {}
```

Yukarıdaki gibi hiçbir şey yazmazsan `SINGLETON` olur.

- App ayağa kalkarken oluşturulur.
- Her yerde aynı örnek kullanılır.

Avantajları:

- Performanslı
- Bellek dostu

Ama:

- Durum (state) tutuyorsa, herkes bu durumu paylaşır = tehlikeli


## 2. Request Scoped

```ts
@Injectable({ scope: Scope.REQUEST })
export class UserContextService {
  constructor(@Inject(REQUEST) private req: Request) {}

  getUserId() {
    return this.req.user?.id;
  }
}
```
Bu servis her HTTP isteği için yeniden oluşturulur.
Yani `UserContextService` artık o isteğe özel veriyle çalışır.

Kullanım alanları:

- Kullanıcı kimliği
- İstek başına değişen veriler
- Loglama, trace id, tenant ID gibi metadata

Dikkat:

- Performansı düşürebilir (her istek için yeni nesne)
- Tüm bağımlılıkları da request-scoped olmalı ya da dikkatlice paylaşılmalı



## 3. Transient Scoped

```ts
@Injectable({ scope: Scope.TRANSIENT })
export class UniqueIdGenerator {}
```

Bu scope, her `constructor()` çağrısında yeni bir örnek oluşturur.


Örnek:
```ts
@Injectable()
export class OrderService {
  constructor(private generator: UniqueIdGenerator) {}
}
```
`OrderService` her çağrıldığında yeni bir `UniqueIdGenerator` örneği alır.
Yani aynı servis bile olsa, her injection noktası farklı nesne alır.


Kullanım senaryosu:

- Kısa ömürlü, durum tutan servisler
- Her iş parçası kendi verisini üretmeli
- Paralel işlerde paylaşım riskliyse

## Felsefi Açıdan...

Scope aslında şunu sorar:
> Bu servis ne kadar yaşamalı, kimlerle paylaşılmalı?

Bu da yazılımda yaşam süresi yönetimi (lifetime management) ve bağımlılık izolasyonu gibi kavramlara dokunur.

- Singleton: Herkesin ortak kullanacağı şeyler (config, sabit servisler)
- Request: Kullanıcıya göre değişen veriler
- Transient: Herkesin kendine özel anlık ihtiyaçları



## @Injectable() ile Tanımlamak

```ts
@Injectable({ scope: Scope.REQUEST })
```
veya
```ts
@Injectable({ scope: Scope.TRANSIENT })
```
`Scope` enum'u şuradan gelir:
```ts
import { Injectable, Scope } from '@nestjs/common';
```


## REQUEST Sabitine Ulaşmak

`@Inject(REQUEST)` ile Express isteğini yakalayabilirsin:
```ts
import { REQUEST } from '@nestjs/core';

constructor(@Inject(REQUEST) private req: Request) {}
```
Ama bu sadece `Scope.REQUEST` ile çalışan servislerde işe yarar.


## Örnek Uygulama: Kullanıcıya Özel Servis
```ts
@Injectable({ scope: Scope.REQUEST })
export class CurrentUserService {
  constructor(@Inject(REQUEST) private req: Request) {}

  getUser() {
    return this.req.user;
  }
}
```
Ve bu servis tüm controller’larda `@Inject()` edilerek kullanılabilir.

## Dikkat Etmen Gerekenler

- Request-scoped bir servis, singleton bir servise inject edilemez.
Tersine ise sorun yok.
- Transient kullanımında dikkat: Performans maliyeti var ama paylaşım riski sıfır.
- Tüm sistemini request-scoped yapmak “temiz” görünebilir ama ölçeklenebilir değildir.
- Gerekmedikçe singleton dışına çıkma.

## Ne Nerede Kullanılır?

| Durum                                             | Scope Seçimi |
| ------------------------------------------------- | ------------ |
| Config, Logger, MailService                       | `SINGLETON`  |
| Authenticated User, Request Context               | `REQUEST`    |
| İşlem bazlı stateful servis (örneğin PDF Builder) | `TRANSIENT`  |
| Her injection’da yeni ID, yeni veri               | `TRANSIENT`  |

## Sonuç

- NestJS’te servislerin yaşam süresi `scope` ile kontrol edilir.
- Varsayılan `singleton`, ama ihtiyaç varsa `request` veya `transient` seçebilirsin.
- Doğru yerde doğru scope kullanmak sisteminin güvenliğini ve performansını doğrudan etkiler.
- Gereksiz yere farklı scope kullanmak hem performansı düşürür hem sistemi karmaşıklaştırır.


