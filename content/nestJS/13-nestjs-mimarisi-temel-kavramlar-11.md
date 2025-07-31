---
title: "NestJS Mimarisi: Provider Nedir? Ne Sağlar? (11. Bölüm)"
date: 2025-07-31
draft: false
weight: 13
---

## NestJS’te Her Şey Bir Provider’dır
NestJS'te controller, service, guard, pipe, hatta resolver... Bunların hepsi özünde birer provider'dır.

> Provider = Nest container’ına tanıtılan ve başka yerlerden inject edilebilen bir “şey”dir.

Sen bir service yazarsın, onu `@Injectable()` yaparsın - bu, Nest’e şunu demektir:

> "Bunu bir yerde kullanmak isteyeceğim, lütfen enjekte edilebilir hale getir."



## En Temel Kullanım: Servis Olarak Provider

```ts
@Injectable()
export class UserService {
  findAll() {
    return ['Ali', 'Ayşe'];
  }
}
```

Bu sınıf bir provider olur.
Bir yerde kullanmak için constructor'a inject edersin:

```ts
@Controller('users')
export class UserController {
  constructor(private userService: UserService) {}

  @Get()
  getUsers() {
    return this.userService.findAll();
  }
}
```

Nest bu yapıyı tanır ve `UserService`'i otomatik olarak örnekler (instantiate eder).


## Provider Tanımlamanın 4 Yolu

Nest sana provider tanımlamak için birkaç farklı yol sunar. Her biri özel durumlar için vardır.

## 1. Class Provider (En yaygın)

```ts
providers: [UserService]
```
Bu, şu anlama gelir:
> `UserService` adında bir sınıfı instance olarak oluştur ve her yerde aynı nesneyi kullan.


## 2. Value Provider
Statik bir veri ya da config objesi vermek istiyorsan:

```ts
{
  provide: 'APP_CONFIG',
  useValue: { port: 3000, env: 'dev' },
}
```
Ve kullanırken:

```ts
constructor(@Inject('APP_CONFIG') config) {}
```

## 3. Factory Provider
Bağımlılıklara göre dinamik üretim gerekiyorsa:

```ts
{
  provide: 'UUID',
  useFactory: () => randomUUID(),
}
```

## 4. Existing Provider
Mevcut bir provider’ı başka bir isimle kullanmak istersen:

```ts
{
  provide: 'IUserService',
  useExisting: UserService,
}
```

## NestJS Container ve Bağımlılık Çözümleme

Nest uygulaması başlarken tüm provider’ları memory’ye alır.
Bu yapıya IoC Container denir. (Inversion of Control)

```txt
UserController depends on UserService
UserService depends on DatabaseService
```

Nest bu bağımlılıkları constructor üzerinden çözer.
Ve enjekte edilecek her şeyi `@Injectable()` olarak tanıman gerekir.

## Manual @Inject() Kullanımı

Normalde Nest tipi baz alarak provider’ı bulur.
Ama bazı özel durumlarda string token kullanman gerekebilir:

```ts
constructor(@Inject('UUID') uuid: string) {}
```

Bu durum genelde `useValue` veya `useFactory` kullanıldığında gerekir.


## Global Provider Nedir?

Bir provider’ı her modülde tek tek tanıtmak yerine global yapabilirsin:

```ts
@Module({
  providers: [
    {
      provide: APP_INTERCEPTOR,
      useClass: LoggingInterceptor,
    },
  ],
})
export class AppModule {}
```
> `APP_` ile başlayan token’lar global tanımlamalarda kullanılır. `@nestjs/core` içinde gelir.


## Provider vs Service
Bazen “provider” ve “service” karıştırılır. Açık net fark şu:

| Terim    | Açıklama                                                             |
| -------- | -------------------------------------------------------------------- |
| Service  | Yazdığın sınıf                                                       |
| Provider | Nest’e tanıttığın şey (bir service olabilir, ama sadece bu değildir) |


Yani her service bir provider olabilir ama her provider service değildir.
Pipe, Guard, Interceptor... hepsi provider’dır.


## Provider Kullanmadan da İş Görür mü?

Evet. Ama ne kaybedersin?

- Test edilebilirlik (mock zorlaşır)
- Reusability (servisi tekrar kullanamazsın)
- Configurability (dinamik bağımlılıklar tanımlayamazsın)

## Kafaya Takılan: @Injectable() Olmadan Çalışır mı?
Eğer o sınıf DI yoluyla bir yere enjekte edilmeyecekse evet, çalışır.
Ama constructor injection kullanacaksan @Injectable() zorunludur. Aksi takdirde Nest hata fırlatır:
> Nest can't resolve dependencies of the ...


## Özet

- Provider = Enjekte edilebilir her türlü yapı
- `@Injectable()` -> Nest'e bu sınıfı provider olarak tanıtır
- Class, value, factory, existing provider türleri vardır
- Nest tüm provider’ları IoC container içinde yönetir
- Servis, pipe, guard, interceptor... hepsi birer provider’dır
- Sağlam bir DI mimarisi için provider sistemi kaçınılmazdır