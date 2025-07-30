---
title: "NestJS Mimarisi: Status Code Yönetimi (7. Bölüm)"
date: 2025-07-29
draft: false
weight: 9
---



## HTTP Status Code Nedir? Neden Önemlidir?

Bir API sadece veri döndürmez.  
Her cevabın bir anlamı olmalıdır. Bu anlamı taşıyan şey de **status code**'tur.

```ts
return { message: 'Başarılı!' };
```

Bu cevap 200 OK ile gelir mi? 201 Created mi? Yoksa 400 Bad Request mi?
İşte bu çok kritik. Çünkü:

> Status code = İstemciye ne olduğunu anlatan işaret lambasıdır.

## NestJS’te Status Code Nasıl Yönetilir?

En sade yol: @HttpCode() dekoratörüdür.

```ts
@Post()
@HttpCode(201)
createUser() {
  return { message: 'Kullanıcı oluşturuldu' };
}
```
> Bu örnekte `POST /users` isteğine dönüş `201 Created` olacaktır.

Eğer yazmazsan, NestJS default olarak şu kuralları kullanır:

| HTTP Metodu | Varsayılan Status |
| ----------- | ----------------- |
| GET         | 200 OK            |
| POST        | 201 Created       |
| DELETE      | 200 OK            |
| PUT         | 200 OK            |


## @Res() Kullanıyorsan Ne Olur?

```ts
@Post()
create(@Res() res: Response) {
  res.status(202).json({ message: 'Talep alındı' });
}
```

Bu durumda tüm kontrol sende. Nest artık otomatik hiçbir şey yapmaz.
Yani bu durumda `@HttpCode()` çalışmaz.

## Başka Ne Var? @Header(), @Redirect()

`@Header()`: Header eklemek için

```ts
@Get()
@Header('Cache-Control', 'none')
noCache() {
  return 'Cache yok';
}
```

`@Redirect()`: 3xx cevaplar için

```ts
@Get('docs')
@Redirect('https://docs.site.com', 302)
goToDocs() {}
```
> `@Redirect()` olmadan da `res.redirect()` yapılabilir ama bu Nest-style değildir.


## NestJS’te Hatalarda Status Code

NestJS’te hata fırlatmak için exception sınıfları vardır.

```ts
@Get(':id')
getUser(@Param('id') id: string) {
  if (!userExists(id)) {
    throw new NotFoundException(`Kullanıcı bulunamadı: ${id}`);
  }
  return user;
}
```
Bu durumda otomatik olarak:

```txt
Status: 404 Not Found
{
  "statusCode": 404,
  "message": "Kullanıcı bulunamadı: 5",
  "error": "Not Found"
}
```

> NestJS içindeki `HttpException` sınıfı ile birlikte gelen `BadRequestException`, `UnauthorizedException`, `ForbiddenException`, `NotFoundException` gibi hazır sınıflar sayesinde status code + mesaj standardı korunur.

## Response Formatı Sabitlemek İçin İpucu

Eğer her cevabın belli bir yapıda olmasını istiyorsan (`{ data, message, statusCode }` gibi), interceptor kullanabilirsin.
Ama bu ileri seviye konuya `Interceptor` başlığında değineceğiz.


## Yaygın Hatalar

- `@HttpCode()` ile `@Res()` aynı anda kullanmak (çalışmaz)
- GET metodundan `201` döndürmek (yanlış anlam yaratır)
- Hata fırlatmak yerine `return { error: '...' }` yapmak (status code 200 döner - yanıltıcı)
- 204 No Content döndürüp JSON body göndermek (204’te body olmamalı)


## Status Code’lar, Bir API'nin "beden dili" Gibidir.

- 200 -> her şey yolunda
- 201 -> yeni bir şey doğdu
- 400 -> sen bir hata yaptın
- 401 -> kimliğini tanımıyorum
- 403 -> seni tanıyorum ama yetkin yok
- 404 -> böyle biri yok
- 500 -> ben de ne olduğunu bilmiyorum :d

## Özet

- `@HttpCode()` ile manuel status belirlenebilir
- `@Res()` kullanıyorsan, kodu da sen yazmalısın
- `@Redirect()` ve `@Header()` özel durumlar için idealdir
- Hatalar `HttpException` sınıflarıyla fırlatılmalı
- Status code, client tarafında doğru davranış için kritik bilgi taşıyıcısıdı