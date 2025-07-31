---
title: "NestJS Mimarisi: Business Logic Katmanı Olarak Servisler (13. Bölüm)"
date: 2025-07-31
draft: false
weight: 15
---

## "Business Logic" Ne Demek?

Kodda her şey teknik olmak zorunda değil.
Bir de işin iş tarafı (business) var.

> Kullanıcı kayıt olurken e-posta eşsiz olmalı<br />
Bir ürün stoğu sıfırsa sepete eklenememeli<br />
Hafta sonu sipariş geçersiz sayılmalı

Bunlar framework’le değil, iş modeliyle ilgili kurallardır.
Ve işte bunlara business logic denir.

## Peki Bu Kurallar Nereye Yazılır?

- Controller’a yazarsan: Kodun şişer. Test yazmak eziyet olur.
- Entity’ye yazarsan: Veritabanına fazla sorumluluk yüklersin.
- Pipe’a yazarsan: Genellikle sadece doğrulama yapılır.
- Service’e yazarsan: Tam olması gereken yer.

> Service = business logic katmanı<br />
Service -> Domain kurallarının merkezi

## Örnek: Ürün Sepete Eklenmeden Önce Stok Kontrolü
Controller:

```ts
@Post('add-to-cart')
addToCart(@Body() dto: AddToCartDto) {
  return this.cartService.add(dto);
}
```
Service:
```ts
@Injectable()
export class CartService {
  constructor(private productService: ProductService) {}

  async add(dto: AddToCartDto) {
    const product = await this.productService.findById(dto.productId);

    if (product.stock <= 0) {
      throw new BadRequestException('Stokta yok');
    }

    // Sepete ekleme işlemleri...
    return 'Sepete eklendi';
  }
}
```
Bu kontrol "stok yoksa işlem yapılamaz" kuralı - yani business logic.
Bunun yeri controller değil, service’tir.


## Business Logic’te Nelere Dikkat Etmeli?

- Response formatı veya HTTP statüsü burada olmamalı
- Request nesnesi (req, res) burada olmamalı
- Sadece girdiye göre karar verilmeli
- Hatalar mantıklı exception’larla fırlatılmalı
- Domain kuralları soyutlanabilir olmalı


## Domain-Driven Design’a Giriş Niteliğinde

Service katmanını iyi organize edersen:
- Uygulama büyüdüğünde çökmez
- Yeni bir controller yazmadan aynı mantığı kullanabilirsin
- Mikroservise geçişte bu yapıyı doğrudan taşırsın
- Testlerini controller’a dokunmadan yazarsın

> Business logic demek domain logic demektir.
Service, iş kurallarını temsil eder. API’yi değil.

## Kod Yapısı Nasıl Olmalı?

Kötü Örnek:

```ts
@Post()
createUser(@Body() dto) {
  if (!dto.email.includes('@')) {
    throw new BadRequestException('Geçersiz email');
  }

  const user = new User(dto);
  this.repo.save(user);
}
```
İyi Örnek:

```ts
@Post()
createUser(@Body() dto) {
  return this.userService.create(dto);
}
```

```ts
@Injectable()
export class UserService {
  async create(dto: CreateUserDto) {
    if (!this.isValidEmail(dto.email)) {
      throw new BadRequestException('Geçersiz email');
    }

    const user = new User(dto);
    return this.repo.save(user);
  }

  private isValidEmail(email: string) {
    return email.includes('@');
  }
}
```

## Sık Yapılan Hatalar

- Business logic’i controller içinde tutmak
- Her şeyi Pipe içine gömmeye çalışmak
- Veritabanı işlemlerini doğrudan controller’da yapmak
- Service yerine helper fonksiyonlara bulaşmak

Bunların hepsi bir gün seni kod çorbası içinde boğar.

## Unutma

> Service, HTTP ile ilgilenmez.
Sadece "şartlar doğruysa şu işlem yapılır" der.
O yüzden controller gider, router değişir, hatta transport layer değişir... ama business logic kalır.


## Özet

- Service sadece utility sınıfı değil, iş kurallarının merkezidir
- Business logic demek: "Şartlar sağlandığında şu olur" demektir
- Kodun test edilebilir, tekrar kullanılabilir, sürdürülebilir olması buradan başlar
- Domain-Driven Design mantığının temeli buraya oturur
- Service içi logic temiz, bağımsız ve taşınabilir olmalı