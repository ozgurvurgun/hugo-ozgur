---
title: "NestJS Mimarisi: Dinamik Modüller ve Yapılandırma (3. Bölüm)"
date: 2025-07-29
draft: false
weight: 5
---

## Dinamik Modül Nedir?
Normal modüller sabittir. İçine neyi yazdıysan, sistem onu kullanır.


Ama bazen bir modülün kurulum aşamasında parametre alması gerekir.  
Yani modülün davranışı dışarıdan gelen bir yapılandırmaya göre değişmelidir.


İşte burada **dinamik modül** devreye girer.


## Gerçek Hayat Senaryosu

Diyelim ki bir `MailerModule` yazıyorsun.

- Geliştirme ortamında log’a yazmasını,  
- Gerçek ortamda ise SMTP sunucusuna mail atmasını istiyorsun.

Yani modül şunu demeli:
> “Ben dışarıdan bana nasıl davranacağım söylensin istiyorum.”


## Nasıl Tanımlanır?

Dinamik modül, `static register()` veya `static registerAsync()` metotlarıyla tanımlanır. Ve dönüş olarak bir `DynamicModule` objesi verir.


## Basit `register()` Örneği:


```ts
// mailer.module.ts
@Module({})
export class MailerModule {
  static register(config: MailerConfig): DynamicModule {
    return {
      module: MailerModule,
      providers: [
        {
          provide: 'MAILER_CONFIG',
          useValue: config,
        },
        MailerService,
      ],
      exports: [MailerService],
    };
  }
}
```

Kullanımı:

```ts
// app.module.ts
@Module({
  imports: [
    MailerModule.register({
      host: 'smtp.mailtrap.io',
      port: 587,
      secure: false,
    }),
  ],
})
export class AppModule {}
```

## registerAsync() Neden Var?
Bazen yapılandırma bilgisi async olarak gelir.

Örneğin:

- Yapılandırmayı bir veritabanından çekiyorsundur
- `ConfigService` gibi başka bir servise bağımlıdır


İşte bu durumda `registerAsync()` devreye girer.

`registerAsync()` Örneği:

```ts
// mailer.module.ts
@Module({})
export class MailerModule {
  static registerAsync(): DynamicModule {
    return {
      module: MailerModule,
      imports: [ConfigModule],
      providers: [
        {
          provide: 'MAILER_CONFIG',
          useFactory: async (configService: ConfigService) => {
            return {
              host: configService.get('MAIL_HOST'),
              port: configService.get('MAIL_PORT'),
              secure: configService.get('MAIL_SECURE'),
            };
          },
          inject: [ConfigService],
        },
        MailerService,
      ],
      exports: [MailerService],
    };
  }
}
```


## Analoji

Bunu bir kahve makinesi gibi düşün:

- `register()` -> “şekerli mi şekersiz mi” bilgisini direkt söylersin.
- `registerAsync()` -> “şeker miktarını başka bir sistemden alacağım” dersin.

Her iki durumda da makine farklı davranır. İşte dinamik modül de bu.

## NestJS’te Bu Nerede Kullanılıyor?


Bazı örnekler:

| Modül            | register kullanımı         |
| ---------------- | -------------------------- |
| `ConfigModule`   | `ConfigModule.forRoot()`   |
| `JwtModule`      | `JwtModule.register()`     |
| `TypeOrmModule`  | `TypeOrmModule.forRoot()`  |
| `MongooseModule` | `MongooseModule.forRoot()` |


Hepsi aslında dinamik modüldür.


## Eksik/Karmaşık Yönler

- Kendi dinamik modülünü yazmak ilk başta kafa karıştırabilir
- Type güvenliği zayıfsa (provide isimleri vs) refactor zorlaşır
- registerAsync içindeki useFactory'ler dependency'lere bağlıysa dikkatli yönetilmelidir
- NestJS’in official dökümantasyonunda detaylar dağınık verildiği için deneme-yanılma gerekebilir

> Bu yüzden bu yazıyı sadece okumak değil, birebir denemek çok önemli.



## Bonus: DynamicModule Nedir?

```ts
export interface DynamicModule {
  module: Type<any>;
  imports?: any[];
  controllers?: Type<any>[];
  providers?: Provider[];
  exports?: any[];
  global?: boolean;
}
```
Aslında @Module() içine yazdığın şeylerin aynısını döndürüyorsun.
Sadece bunu runtime'da inşa etme yetkisini kendin alıyorsun.


## Özetle

- Dinamik modül, esnek ve yapılandırılabilir bir modüldür
- register() -> sabit yapılandırmalar
- registerAsync() -> async yapılandırmalar
- Modül içindeki sağlayıcılar DynamicModule üzerinden oluşturulur
- NestJS’in birçok core modülü zaten bu sistemi kullanır

