---
title: "NestJS Mimarisi: Global Exception Handle (8. Bölüm)"
date: 2025-07-30
draft: false
weight: 10
---

## Hata Fırlatmak Yetmez, Yakalamak da Gerekir

Hatalar kaçınılmaz.
Ama kontrolsüz hata = üretimde patlayan sistem = sinirli müşteri = uykusuz geliştirici.


NestJS bu durumu çözmek için merkezî bir hata yakalama mekanizması sunar:
**Global Exception Filter**


## Şimdiye Kadar Ne Yaptık?

Şöyle kodlar yazdık:

```ts
if (!userExists(id)) {
  throw new NotFoundException('Kullanıcı bulunamadı');
}
```
Ve Nest bunu alıp güzelce 404 yanıtıyla döndü. Harika.

Ama ya özel bir hata sınıfı yazmak istiyorsan? Ya da her hatayı `{ status, errorCode, message, timestamp }` gibi özel bir formatta döndürmek istiyorsan?


O zaman devreye **Global Exception Filter** giriyor.

## @Catch() ile Özel Hataları Yakalamak

NestJS’te hata yakalamak için Exception Filter kullanılır.
Kendi filter’ını yazmak için `ExceptionFilter` arayüzünü uygularsın.

```ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
} from '@nestjs/common';

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : 500;

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: ctx.getRequest().url,
      message: exception instanceof HttpException
        ? exception.getResponse()
        : 'Internal Server Error',
    });
  }
}
```

## Uygulamaya Globally Tanıtmak

Bu filter’ı tüm uygulama için aktif etmek istiyorsan `main.ts` dosyasına ekle:

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new AllExceptionsFilter());
  await app.listen(3000);
}
bootstrap();
```
Ve artık uygulama içinde hangi exception fırlatılırsa fırlatılsın bu yapıdan geçer.


## Kendi Error Sınıflarını da Yakalamak Mümkün

Mesela:
```ts
export class CustomException extends Error {
  constructor(message: string) {
    super(message);
  }
}
```
Ve bunu yakalamak için sadece bu sınıfı hedef alan bir `@Catch(CustomException)` filter'ı da yazabilirsin.

Bu da sana hataları ayrıştırma ve farklı yapılarla ele alma şansı verir.


## Ne Kazanırsın?

- Hata mesajları tek bir yerden kontrol edilir
- Tüm hatalar aynı formatta döner
- Üretim ortamı için loglama sistemlerine entegre edilebilir
- API istemcileri tutarlı response yapısı sayesinde kolay yönetilir


## Dürüst Gerçekler

- Nest varsayılan Exception’ları zaten güzel işler - her şeyi özelleştirmen gerekmez
- Eğer merkezi bir loglama (Sentry, Datadog vs.) kullanıyorsan bu sistemle kolay entegre olursun
- Hataları her yere fırlatmak yerine, mantıklı yerlerde fırlatıp centralized yakalamak iyi bir mimaridir

## Özetle

- NestJS, `ExceptionFilter` yapısıyla hataları merkezi olarak ele almanı sağlar
- `@Catch()` dekoratörüyle özelleştirme yapabilirsin
- Global olarak `main.ts` içinde `app.useGlobalFilters()` ile tanıtılır
- Format birliği ve kontrol tek noktadan sağlanır
- Loglama ve production-ready sistemler için vazgeçilmezdir