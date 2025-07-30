---
title: "NestJS Mimarisi: Controller Nedir? Ne Değildir? (5. Bölüm)"
date: 2025-07-29
draft: false
weight: 7
---


## Controller Nedir?

NestJS'te Controller, gelen HTTP isteklerini karşılayan yapıdır.  
Yani dış dünyadan gelen tüm `GET`, `POST`, `PUT`, `DELETE` istekleri burada karşılanır.

> Kısaca: Controller = API’nin giriş noktasıdır.


## Router mı bu?

Express kullanmış olanlar için şöyle bir benzetme yapalım:

```js
// Express
app.get('/users', handler);
```

```ts
// NestJS
@Controller('users')
export class UserController {
  @Get()
  getAll() {
    return 'user listesi';
  }
}
```
Arka planda aslında Express (veya Fastify) hala çalışıyor.
Ama NestJS bunu sınıflar ve dekoratörlerle daha okunabilir ve organize bir hâle getiriyor.


## Temel Yapı

```ts
@Controller('users')
export class UserController {
  @Get()
  getUsers() {
    return 'Tüm kullanıcılar listelendi';
  }

  @Post()
  createUser() {
    return 'Yeni kullanıcı oluşturuldu';
  }
}
```

Burada:

| Dekoratör       | Anlamı                  |
| --------------- | ----------------------- |
| `@Controller()` | Bu sınıf bir controller |
| `@Get()`        | GET isteğini karşılar   |
| `@Post()`       | POST isteğini karşılar  |


Yani tarayıcıdan `/users` GET isteği gelirse `getUsers()` çalışır.


## Parametre Almak
Controller’ın güzelliği, parametreleri doğrudan fonksiyon içine alabilmendir.

1. Route Parametresi (/users/:id):

```ts
@Get(':id')
getUser(@Param('id') id: string) {
  return `Kullanıcı ID: ${id}`;
}
```

2. Query Parametresi (/users?active=true):
```ts
@Get()
filterUsers(@Query('active') isActive: string) {
  return `Aktif kullanıcılar: ${isActive}`;
}
```

3. Body Parametresi (POST için):
```ts
@Post()
create(@Body() userData: any) {
  return userData;
}
```

> `@Param` `@Query` `@Body` `@Headers` `@Req` `@Res` gibi birçok decorator var. Her biri istekten belirli bir parçayı çeker.


## Controller - Service İlişkisi
Controller, business logic içermez. Sadece gelen isteği alır, ilgili servise yönlendirir.

```ts
@Controller('users')
export class UserController {
  constructor(private userService: UserService) {}

  @Get()
  getAll() {
    return this.userService.findAll();
  }
}
```

Yani Controller sadece aracıdır. Gerçek işi Service yapar.

## Anti-Pattern: Controller içine tüm mantığı yazmak

```ts
@Get()
getAll() {
  const users = readFileSync('db.json'); // olmaz
  return JSON.parse(users);
}
```

Bu kod çalışır ama kötü bir pratik. Çünkü:
- Test edilemez hale gelir
- Diğer modüller bu işlemi tekrar edemez
- Kod tekrarına neden olur

> Bunun yerine: mantığı `UserService` içine yaz, Controller sadece çağırıcı olsun.

## Best Practice Önerileri

| Yapılacak Şey                  | Neden?                                                                       |
| ------------------------------ | ---------------------------------------------------------------------------- |
| Servis çağrısı yap             | Kodun test edilebilirliğini artırır                                          |
| Parametreleri dekoratörle al   | Daha sade ve okunabilir olur                                                 |
| HttpException fırlatmayı öğren | Standart hata yapısı oturur (`NotFoundException`, `BadRequestException` vs.) |
| Controller'ı küçük tut         | Sadece yönlendirme yapsın                                                    |


## Bonus: Custom Route Prefix

```ts
@Controller({ path: 'users', version: '1' })
```
Bu şekilde versiyonlama yapabilir, API’nizi daha profesyonel hale getirebilirsin.


## Eksik/Karmaşık Yönler
- NestJS dıştan çok sihirli görünür: ama Express çalışır, next() mantığı arka planda hala geçerli.
- `@Res()` kullanımı Express bağımlılığı yaratır, bu yüzden return ile response dönmek daha Nest-native bir yaklaşımdır.
- Exception handling’i `Controller` değil `Filter` ve `Pipe` gibi yapılara yaymak gerekir (ileride değineceğiz)


## Özet

- Controller = dış dünyadan gelen HTTP isteklerini karşılayan yapıdır
- `@Controller`, `@Get`, `@Post` gibi decorator’larla yönetilir
Parametreler `@Param`, `@Body`, `@Query` gibi decorator’larla alınır
- Controller logic içermez, sadece yönlendiricidir
- İşin gerçek mantığı Service’e bırakılmalıdır