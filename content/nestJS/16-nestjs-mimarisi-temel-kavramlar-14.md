---
title: "NestJS Mimarisi: Value, Class ve Factory Providers (14. Bölüm)"
date: 2025-07-31
draft: false
weight: 16
---


## Provider Ne Demekti?

NestJS’de bir class veya değer, "provider" olarak tanımlanırsa
constructor() içinde @Inject() ile enjekte edilebilir.

Yani dependency injection sistemine kayıtlı olur.
Ama bu provider illa bir class olmak zorunda değil.


İşte tam burada 3 farklı yol devreye giriyor:

| Tür                  | Ne Sağlar?                          | Ne Zaman Kullanılır?                        |
| -------------------- | ----------------------------------- | ------------------------------------------- |
| **Value Provider**   | Sabit bir değer (obj, string, sayı) | Config, sabit ayarlar, env verileri         |
| **Class Provider**   | Alternatif class tanımı             | Mock servis, farklı implementasyonlar       |
| **Factory Provider** | Fonksiyonla üretilen provider       | Dinamik yapı, config’e bağlı servis üretimi |



## 1. Value Provider
Bir sabit değer sistemin bir parçası olarak kullanılır.

```ts
const config = {
  apiKey: 'abc123',
  baseUrl: 'https://api.example.com',
};
```

```ts
@Module({
  providers: [
    {
      provide: 'CONFIG',
      useValue: config,
    },
  ],
})
export class AppModule {}
```
> Sabit verileri DI sistemine sokmanın en basit yolu


## 2. Class Provider

Normalde şöyle tanımlarız:
```ts
@Module({
  providers: [EmailService],
})
```
Ama eğer `EmailService` yerine farklı bir class kullanılacaksa:

```ts
@Module({
  providers: [
    {
      provide: EmailService,
      useClass: MockEmailService,
    },
  ],
})
```

Ya da string tokenla:

```ts
@Module({
  providers: [
    {
      provide: 'IEmailService',
      useClass: EmailService,
    },
  ],
})
```

Kullanımı:

```ts
constructor(@Inject('IEmailService') private emailService: EmailService) {}
```

> Mocklama, test senaryosu, veya farklı implemantasyonlar için süper


## 3. Factory Provider

En güçlü ve esnek yöntem.

Provider’ı bir fonksiyonla oluşturursun. Yani runtime’da veriye göre ne döneceğine sen karar verirsin.

```ts
@Module({
  providers: [
    {
      provide: 'DATABASE_URL',
      useFactory: () => {
        return process.env.NODE_ENV === 'prod'
          ? 'mongodb://prod-db'
          : 'mongodb://localhost/dev';
      },
    },
  ],
})
```

Ve:
```ts
constructor(@Inject('DATABASE_URL') private dbUrl: string) {}
```

Dilersen başka dependency’ler de inject edebilirsin:

```ts
useFactory: (configService: ConfigService) => {
  return configService.get('databaseUrl');
},
inject: [ConfigService],
```
> Dinamik yapı isteyenler için biçilmiş kaftan

## Neden Bu Kadar Çeşit Var?

Çünkü NestJS sadece class değil, her şeyi DI container’a dahil edebilsin istiyor.

Bu sistemin sana sundukları:

- Modülerlik
- Mock edilebilirlik
- Test kolaylığı
- Ortam bazlı konfigürasyon desteği

## TL;DR - Ne Ne İçin Kullanılır?

| Provider Türü | Kullanım Amacı                                    |
| ------------- | ------------------------------------------------- |
| `useValue`    | Sabit bir değer sağlamak (env, config, sabit obj) |
| `useClass`    | Alternatif implementasyonla inject etmek          |
| `useFactory`  | Runtime’da logic çalıştırarak provider oluşturmak |



## Gerçek Hayattan Senaryolar

- `useValue` -> `app.config.ts` içinde `ConfigService`’e sabitler vermek
- `useClass` -> Development ortamında `MockMailService`, production’da `SendGridMailService`
- `useFactory` -> Request başına değişen servisler üretmek (örneğin tenant bazlı database bağlantısı)


## Dikkat Edilecek Noktalar

- `provide`: kısmı string ise `@Inject('TOKEN')` ile kullanılmalı
- `useClass`, `useValue`, `useFactory` birbirinin yerine geçemez
- Factory provider içinde başka servisler kullanılacaksa `inject` mutlaka tanımlanmalı
- Provider’lar `Module` içinde tanımlanmazsa inject edilemez

## Sonuç

- NestJS provider sistemi sadece class’larla sınırlı değil
- 3 farklı yol (value, class, factory) ihtiyaca göre çözüm sunar
- Uygulamanın esnekliği ve sürdürülebilirliği bu yapılarla kurulur
- Test yazmak, ortam yönetimi, configurasyon gibi konularda vazgeçilmezdir