---
title: "NestJS Mimarisi: Headers ve Query Parametreleri Okuma (9. Bölüm)"
date: 2025-07-30
draft: false
weight: 11
---


## HTTP İsteği Sadece Body'den İbaret Değil

API’ye gelen her istek 3 temel veri kaynağı taşır:

- Body -> İçerik, payload
- Query -> Filtre, arama, sayfalama gibi şeyler
- Headers -> Kimlik, token, dil bilgisi, içerik türü

Ve bunların her biri farklı ihtiyaçlara hizmet eder. NestJS bu üçüne de ayrı dekoratörler sunar.

Bu bölümde `@Query()` ve `@Headers()` üzerinde duracağız.

@Query() - Sorgu Parametrelerini Okumak

Örnek URL:
```bash
GET /products?page=2&limit=10&sort=price
```
Bu URL'deki `page`, `limit` ve `sort` kısmı query parametresidir.

Bunu almak için:
```ts
@Get()
getProducts(@Query() query: any) {
  console.log(query); // { page: '2', limit: '10', sort: 'price' }
  return query;
}
```

## Tek Tek Erişmek de Mümkün:

```ts
@Get()
getProducts(
  @Query('page') page: string,
  @Query('limit') limit: string
) {
  return `Sayfa: ${page}, Limit: ${limit}`;
}
```

> Query her zaman string gelir. Sayıya çevirmek istiyorsan kendin dönüştür ya da Pipe kullan.


## DTO ile Tip Tanımlamak (Tavsiye Edilen Yol)

```ts
export class ProductQueryDto {
  page: number;
  limit: number;
  sort?: string;
}
```
```ts
@Get()
getProducts(@Query() query: ProductQueryDto) {
  return query;
}
```
> Bu sayede hem tip kontrolü hem autocompletion kazanırsın.


## @Headers() - Header Bilgilerini Okumak

Header'lar genellikle kimlik (JWT), dil, content-type gibi bilgileri taşır.
```ts
@Get()
getHeaderInfo(@Headers() headers: any) {
  console.log(headers['user-agent']);
  return headers;
}
```

## Belirli Header’a Erişmek:
```ts
@Get()
getLang(@Headers('accept-language') lang: string) {
  return `Tercih edilen dil: ${lang}`;
}
```
> Header isimleri küçük harfli string olarak verilir. Büyük harfle yazarsan `undefined` döner.

## @Req() ile de Erişebilirsin Ama...
```ts
@Get()
rawWay(@Req() req: Request) {
  return req.query.limit;  // veya req.headers['authorization']
}
```
Bu da çalışır, ama NestJS felsefesi gereği dekoratörlü yol daha temiz ve test edilebilir kabul edilir.

## Ne Sıklıkla Kullanılır?

| Durum                            | Kullanım      |
| -------------------------------- | ------------- |
| Sayfalama (page, limit)          |  `@Query()`   |
| Filtreleme (status, keyword vs.) |  `@Query()`   |
| JWT Token                        |  `@Headers()` |
| Dil tercihi                      |  `@Headers()` |
| CORS veya Content-Type kontrolü  |  `@Headers()` |


## Dikkat Edilecek Noktalar

- Query parametreleri her zaman string’tir
`page=2` olarak gelir ama `typeof page === 'string'`
- Header adları case-sensitive değildir ama JavaScript objelerinde küçük harfli yazılır (`headers['authorization']`)
- Query parametreleri güvenli değildir, client istediğini gönderebilir. O yüzden tip kontrolü veya validation pipe önerilir
- Header’lar da aynı şekilde spoof edilebilir. Doğrulama backend tarafında yapılmalı

## Parametre Gelmediyse?

```ts
@Get()
test(@Query('page') page?: string) {
  if (!page) return 'Sayfa parametresi gelmemiş';
  return `Sayfa: ${page}`;
}
```
Boş gelirse `undefined` olur. Bu yüzden ya `?` işaretiyle optional yap, ya da default değer ata.


## Özet

- `@Query()` -> URL'deki ? ile gelen verileri okur
- `@Headers()` -> HTTP header bilgilerini okur
- Her ikisi de `@Req()` içinden alınabilir ama dekoratörle almak daha doğru yaklaşımdır
- Gelen değerler string’tir, tip dönüşümüne dikkat
- DTO ile tip güvenliği, Pipe ile doğrulama sağlanabilir
- Parametre gelmemesi durumunu mutlaka kontrol et