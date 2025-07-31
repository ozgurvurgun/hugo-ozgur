---
title: "NestJS Mimarisi: Route Wildcard ve Prefixler (10. Bölüm)"
date: 2025-07-30
draft: false
weight: 12
---


## Rota Yönetiminde Bir Seviye Yukarı: Wildcard ve Prefix Kullanımı

Normalde rotaları şöyle tanımlarız:

```ts
@Controller('users')
export class UserController {
  @Get()
  getAll() { ... }

  @Get(':id')
  getById(@Param('id') id: string) { ... }
}
```

Ama bazı durumlarda daha esnek tanımlamalar gerekebilir:
- Tüm istekleri karşılayan bir “catch-all” route
- Belirli bir grup route’a ön ek tanımlama
- Statik dosyalar, client-side SPA fallback gibi durumlar

İşte burada wildcard ve prefix kavramları devreye giriyor.


## 1. Route Prefix Nedir?

Prefix, bir grup route’un başına otomatik olarak eklenen ortak path’tir.

### Global Prefix:

Tüm uygulamanın başına bir ön ek eklemek için `main.ts` içinde:

```ts
const app = await NestFactory.create(AppModule);
app.setGlobalPrefix('api');
```
> Artık tüm route'lar `/api` ile başlar:
Örneğin `GET /users` -> `GET /api/users`


## Versiyonlama için Prefix Kullanımı:
```ts
app.setGlobalPrefix('v1');
```
veya dinamik:
```ts
app.setGlobalPrefix('api/v1');
```
Bu yöntem, API sürümlerini yönetmenin basit ama etkili bir yoludur.


## 2. Controller Bazlı Prefix (Decorator Üzerinden)

```ts
@Controller('auth')
export class AuthController {
  @Get('login')
  login() {} // /auth/login
}
```

Yani prefix sadece `setGlobalPrefix` ile değil,
`@Controller('prefix')` ile de tanımlanabilir.

Bunlar birbirinin üzerine bindirilebilir.


```ts
app.setGlobalPrefix('api');
@Controller('users')
// -> /api/users
```


## 3. Route Wildcard Nedir?

Wildcard = “joker” karakterdir. Belirli bir pattern’e uymayan istekleri karşılamak için kullanılır.

```ts
@Controller('*')
export class FallbackController {
  @Get()
  handleAll(@Req() req: Request) {
    return `Böyle bir route yok: ${req.url}`;
  }
}
```

Bu yapı, tanımlanmamış tüm GET isteklerini yakalar.
Özellikle Single Page Application (SPA) projelerinde, frontend'e fallback yapılmak isteniyorsa ideal çözümdür.


## Route İçinde Wildcard Kullanımı:
```ts
@Get('docs/*')
handleDocs(@Req() req: Request) {
  return `Docs yolu: ${req.url}`;
}
```

> Bu, tüm `docs/...` yollarını karşılar.
Yani `/docs/intro`, `/docs/setup`, `/docs/anything` hepsi bu method’a gelir.


## 4. @All() - Tüm HTTP Metodlarını Yakalamak

```ts
@All('*')
handleAnything(@Req() req: Request) {
  return `Method: ${req.method}, Path: ${req.url}`;
}
```

Bu, sadece path'i değil aynı zamanda `GET`, `POST`, `PUT`, `DELETE` gibi tüm HTTP metodlarını kapsar.
Genelde fallback API handler'larda, loglamalarda veya yetkisiz erişim yakalayıcılarında kullanılır.


## 5. Wildcard Kullanırken Nelere Dikkat Etmeli?

- Sıralama önemli: NestJS route tanımlarını sırayla işler.
`@Get('*')` gibi wildcard route'lar en sona bırakılmalıdır, aksi takdirde diğer route’lar çalışmaz.
- Performance: Çok geniş wildcard tanımları (örneğin `*`) response time’ı etkileyebilir. Özellikle log veya proxy gibi yapılarda dikkatli ol.
- Test yazmayı zorlaştırır: Her şeyi yakalayan controller, hata ayıklarken nereye düştüğünü anlamayı zorlaştırabilir.


## Ne Zaman Kullanılır, Ne Zaman Kullanılmaz?

| Senaryo                                            | Kullanım Uygun mu?   |
| -------------------------------------------------- | -------------------- |
| SPA fallback (Angular, React, Vue frontend)        | Evet                 |
| Tüm bilinmeyen route'ları loglamak                 | Evet                 |
| Admin panel prefix yapısı                          | Evet                 |
| API sürümleme                                      | Evet                 |
| Tüm rotaları tek `*` ile karşılamak                | Hayır, kötü pratik   |
| “Kodu az olsun” diye tüm path’leri wildcard yapmak | Çok kötü fikir       |


## Özet

- `setGlobalPrefix()` -> tüm uygulamanın path başına ön ek ekler
- `@Controller('prefix')` -> controller bazında ön ek tanımlar
- `@Get('*')`, `@All('*')` gibi wildcard tanımlarla bilinmeyen path’ler yakalanabilir
- SPA uygulamalarında client-side route’lara düşmek için kullanılır
- Dikkatsiz wildcard kullanımı diğer rotaların çalışmamasına yol açar
- Wildcard route’lar en sona yazılmalı