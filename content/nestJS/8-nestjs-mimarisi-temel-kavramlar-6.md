---
title: "NestJS Mimarisi: Controller’da Request ve Response Nesnelerinin Kullanımı (6. Bölüm)"
date: 2025-07-29
draft: false
weight: 8
---


## NestJS’te Request ve Response Nesneleri


NestJS, altında Express (veya Fastify) kullanır.  
Yani istersen alışılagelmiş `req`, `res` nesnelerine erişebilirsin.

Ama NestJS bunun yerine decorator temelli bir yaklaşım sunar.


> Yani doğrudan `req.body` yerine `@Body()` kullanırsın.  
> `res.send()` yerine doğrudan `return` yaparsın.


Peki nasıl ve neden?


## 🧱 `@Req()` ve `@Res()` Nedir?

### `@Req()`:
HTTP isteğini temsil eden `Request` objesini verir. (Express tipindedir)

```ts
@Get()
handleRequest(@Req() req: Request) {
  console.log(req.headers['user-agent']);
  return 'Gelen istek işlendi';
}
```
`@Res()`: Manuel olarak yanıt oluşturmak istediğinde kullanılır.

```ts
@Get()
handleResponse(@Res() res: Response) {
  res.status(200).json({ message: 'Manuel yanıt' });
}
```

> Bu yaklaşım seni Express'e bağımlı hâle getirir. NestJS bunu pek önermiyor. Nedenine birazdan geleceğiz.


## NestJS’in Tavsiye Ettiği Yol

Controller metodlarında mümkünse `@Res()` kullanmak yerine doğrudan `return` et.

```ts
@Get()
getAll() {
  return { message: 'Otomatik cevap!' };
}
```

Nest, bu cevabı otomatik olarak JSON’a çevirir ve status code verir (200 OK).
> Yani `@Res()` yerine `return` -> Nest-native
`res.send()` -> Express-native


## Ne Zaman @Res() Kullanılır?

Bazı özel durumlarda `@Res()` kullanmak mantıklıdır:

| Durum                            | `@Res()` Kullanımı Gerekli mi?         |
| -------------------------------- | -------------------------------------- |
| Dosya indirme (`res.download()`) | Gerekli                                |
| Response stream açma             | Gerekli                                |
| Özel header veya cookie yazma    | Gerekli (ama `@Res()` zorunlu değil)   |
| Redirection (`res.redirect()`)   | Gerekli                                |
| Normal JSON cevabı dönmek        | `return` kullan yeter                  |


## Kafa Karışıklığı: @Res() Kullanırsan Return Çalışmaz

Eğer `@Res()` kullanırsan, Nest artık otomatik olarak response dönmez.
Sen dönmezsen cevap `timeout` olur.

```ts
@Get()
manual(@Res() res: Response) {
  // return yazsan da çalışmaz
  res.status(201).send('Tamamdır');
}
```

> Bu yüzden `@Res()` kullandığında her zaman cevabı sen vermelisin.

## `@Body()`, `@Param()`, `@Query()` ile Farkı Ne?

| Dekoratör    | Açıklama                             |
| ------------ | ------------------------------------ |
| `@Body()`    | Request body (POST içerikleri)       |
| `@Param()`   | URL path parametresi (`/user/:id`)   |
| `@Query()`   | Query string (`?page=2`)             |
| `@Headers()` | İstek başlıkları (`req.headers`)     |
| `@Req()`     | Tüm request objesi (Express tipinde) |
| `@Res()`     | Response objesi (manuel kontrol)     |


> Yani `@Req()` ve `@Res()` -> ham kontrol
Diğerleri (`@Body`, `@Param`) -> değiştirilebilir parça


## Analoji

`@Req()` ve `@Res()` kullanmak = manuel vites araba
`@Body()`, `@Param()`, `return` kullanmak = otomatik vites

Her ikisi de seni bir yere götürür ama biri daha zahmetsiz ve güvenlidir.


## Objektif Gerçekler

- `@Res()` performanslıdır ama Nest felsefesine aykırıdır.
- Test yazmak zorlaşır (çünkü response manuel kontrol ister)
- `return` ise daha sade, test edilebilir, okunaklıdır.
- `@Res()` ile çalışınca response akışını kaçırma ihtimalin artar (özellikle async fonksiyonlarda)


## Özet
- `@Req()` ve `@Res()` ile istek/cevaba direkt erişebilirsin
- Ama NestJS `return` temelli response yönetimini tercih eder
- `@Body()`, `@Param()`, `@Query()` gibi dekoratörlerle daha sade yazılır
- `@Res()` kullanıyorsan, cevabı sen döndürmek zorundasın
- Özel durumlar dışında Nest-native kalmak uzun vadede daha sağlıklıdır
