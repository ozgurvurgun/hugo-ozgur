---
title: "NestJS Mimarisi: Service Oluşturma ve Kullanma (12. Bölüm)"
date: 2025-07-31
draft: false
weight: 14
---

## Controller İş Yapar, Service İşletir

NestJS mimarisinde `Controller`, dış dünyayla konuşan yerdir.
Ama işin mantığı, hesaplama, veri işleme gibi şeyler Service içinde olmalıdır.

> Controller = sekreter. Çağrıyı alır, ilgili departmana yönlendirir.
Service = departman. Gerçek işi yapar.

Eğer her şeyi controller içinde yaparsan:

- Test yazmak zorlaşır
- Kod tekrar eder
- Modülerlik kaybolur
- API karmaşıklaşır


## Service Tanımlamak: En Temel Hali
```ts
@Injectable()
export class UserService {
  private users = ['Ayşe', 'Fatma', 'Hayriye'];

  findAll() {
    return this.users;
  }

  findById(id: number) {
    return this.users[id];
  }
}
```

> `@Injectable()` diyerek bu sınıfı Nest’in DI (dependency injection) sistemine tanıtıyoruz.
Artık başka yerlerde constructor üzerinden inject edilebilir hale gelir.


## Controller İçinde Kullanmak
```ts
@Controller('users')
export class UserController {
  constructor(private userService: UserService) {}

  @Get()
  getAllUsers() {
    return this.userService.findAll();
  }

  @Get(':id')
  getOne(@Param('id') id: string) {
    return this.userService.findById(+id);
  }
}
```
> `private userService: UserService` yazınca Nest otomatik olarak bu servisi inject eder.

## CLI ile Service Oluşturmak
Elle uğraşmak istemiyorsan Nest CLI seni mutlu eder:

```bash
nest g service user
```

Sonuç:
```txt
src/user/
├── user.service.ts
├── user.service.spec.ts
```
İlk dosya service’in kendisi, ikincisi test dosyasıdır.


## Service Ne Tür İşleri Üstlenmeli?

- Veritabanı sorguları
- İş kuralları (business logic)
- Diğer servislerle haberleşme
- Veri dönüştürme, hazırlama
- Hesaplama, filtreleme


## Nest Neden Bu Ayrımı Zorunlu Kılar?
Bu mimari şu faydaları getirir:

- Kod okunabilirliği artar
- Test yazmak kolaylaşır (servis bağımsız test edilebilir)
- Tekrar kullanılabilirlik artar (aynı servis başka controller’dan da çağrılabilir)
- Separation of concerns sağlanır: controller “iletişimci”, service “işçi” olur


## Servisler Arası Bağımlılık?

Service içinde başka bir service’i de inject edebilirsin:

```ts
@Injectable()
export class OrderService {
  constructor(private paymentService: PaymentService) {}

  placeOrder() {
    this.paymentService.charge();
  }
}
```
Nest bu bağımlılığı container üzerinden çözer.

## async/await Kullanımı
Servis method’larının çoğu veritabanı gibi async kaynaklara bağlıdır:

```ts
async findAll() {
  return await this.userRepository.find();
}
```

Controller da buna göre async olur:

```ts
@Get()
async getAll() {
  return await this.userService.findAll();
}
```

> Best practice: async method’ları `await` ile çağırmak yerine doğrudan döndürmek bile mümkündür. Ama readability için `await` tercih edilebilir.


## Service İçinde Ne Olmamalı?

- HTTP request-response nesneleri (req, res)
- Pipe, guard, interceptor gibi yapıların logic’i
- Kullanıcının yetkisini doğrudan kontrol etmek (bu guard’ın işidir)

Service “bağımsızdır”. İstersen API olmadan da çağırabilirsin.


## Test Edilebilirlik Neden Service ile Başlar?

Çünkü service:

- Hiçbir framework’e bağlı değildir
- Sadece input alır, output döner
- DI sayesinde mock’lanabilir

Bu da test yazmayı basitleştirir:

```ts
describe('UserService', () => {
  let service: UserService;

  beforeEach(() => {
    service = new UserService();
  });

  it('should return all users', () => {
    expect(service.findAll()).toEqual(['Ece', 'Ozgur', 'Ela']);
  });
});
```

## Özetle

- Service, işin mantıksal kısmını içerir
- `@Injectable()` ile provider olarak tanımlanır
- Controller, sadece yönlendirici olur
- Servisler modüler, test edilebilir, DI uyumludur
- NestJS’in clean architecture anlayışı bu ayrımı temel alır