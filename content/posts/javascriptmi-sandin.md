---
title: "JavaScript mi Sandın?"
date: 2025-07-26
draft: false
---


Şu kodları görmüşsündür:

```js
document.addEventListener("click", () => console.log("clicked"));
```

```js
setTimeout(() => console.log("done"), 1000);
```

```js
localStorage.setItem("user", "ozgur");
```

Bunlar sana "JavaScript yazıyorum" gibi geliyor değil mi?
Ama gerçekte olan şu: Bunların hiçbiri JavaScript'in kendisi değil.

## JavaScript Nedir?

JavaScript, ECMAScript standardına bağlı bir programlama dilidir.
Sana şunları sunar:

- `let` `const` `if` `for` `function` `class`
- `Array` `Object` `String` `Number`
- `Promise` `Symbol` `Map` `Set`

Ama asıl sihir nerede biliyor musun?
JavaScript'e bir vücut kazandıran şey, çalıştığı ortamın sunduğu API’lerdir.


## Web API Nedir?

Tarayıcılar (Chrome, Firefox, Safari...) sana JavaScript üzerinden kullanabileceğin bir sürü hazır "yetki" verir:

```txt
| API            | Açıklama                    |
| -------------- | --------------------------- |
| document       | HTML’e erişmeni sağlar      |
| fetch()        | HTTP isteği atmanı sağlar   |
| setTimeout()   | Zamanlayıcı                 |
| localStorage   | Tarayıcıya veri kaydetme    |
| navigator      | Tarayıcı bilgilerine erişim |
```

Bunlar tarayıcıların içindeki C++ kodlarıyla yazılmış hazır modüllerdir. Sen sadece JavaScript’le onları çağırırsın.


## Restoran Analojisi

- Tarayıcı = Restoran
- JavaScript = Menü
- Web API = Mutfak


Sen menüden Burger seçersin (`localStorage.setItem(...)`)
Ama o burgeri sen yapmazsın, mutfak (C++) hazırlar
Sen sadece “burger istiyorum” demişsindir.


## Nereden Anlarsın?

Node.js’e geçince bazı şeyler çalışmaz:

```js
document.querySelector("body"); // hata
alert("merhaba"); // hata
```
Çünkü Node.js’de tarayıcı yok -> `document` `alert` `localStorage` gibi Web API’ler de yok.

Ama şunlar çalışır:

```js
let a = [1, 2, 3].map(x => x * 2);
```


## Bu API’leri JS ile Yazamaz mıyız?

Yazarsın ama alt seviye erişimin olmaz. Örneğin kendi localStorage’ını yazamazsın çünkü tarayıcıya doğrudan dosya yazamazsın.

## JavaScript İçinde Taklit API
Bu kolay. Örnek:

```js
class FakeStorage {
  #store = {};
  setItem(k, v) { this.#store[k] = v; }
  getItem(k) { return this.#store[k]; }
}
```

Bu tür sınıflarla “kendi localStorage’ını yazdım” diyebilirsin ama bu sadece bellekte çalışır.
Tarayıcıya gidip dosya yazamaz, çerez oluşturamaz, sistem API’lerine dokunamazsın.

> Çünkü JavaScript izole bir dünyada yaşar. Güvenlik gereği, tarayıcı seni dışarı çıkarmaz.

## Gerçek Web API Nasıl Yazılır?

Şimdi iş ciddileşiyor. Tarayıcıya kendi yazdığın C++ kodunu bağlamak istiyorsan:

Web API gibi native bir modül yazmak için:
- Tarayıcıların kaynak kodlarına katkı yapman gereki
- Veya bir tarayıcı plugin/extension geliştirip API etkisi yaratabilirsin
- Ya da WebAssembly (WASM) kullanarak C/C++ kodunu JavaScript’e bağlarsın

##  WebAssembly: “C++ ile JS’te Süper Güçler Yazmak

WebAssembly sayesinde C/C++ kodunu alırsın, tarayıcının anlayacağı bytecode’a çevirirsin. JavaScript bunu çalıştırabilir. Örnek:

```c
// hello.c
int topla(int a, int b) {
  return a + b;
}
```

Bu C kodunu `Emscripten` ile `.wasm` dosyasına çevirirsin:

```bash
emcc hello.c -o hello.js
```

Sonra JS’te çağırırsın:

```js
const sonuc = Module._topla(2, 3);
```

## Web API Gibi Erişim?

Eğer tarayıcının native API’lerine erişmek istiyorsan (dosya sistemi, ağ, bluetooth, kamera...):

- Tarayıcı Plugin / Extension geliştirmen gerekir.
- Ya da Electron / Tauri gibi hibrit ortamlar kullanarak sistem seviyesine ulaşırsın.

Tarayıcıya `localStorageV2` gibi yeni bir API eklemek istiyorsan:
- Tarayıcının C++ kaynak koduna PR açman gerekir (örneğin Chromium gibi açık kaynak projelere).
- Bu çok üst düzey bir iştir, genelde tarayıcı geliştiren ekipler yapar.


## Özetle: “Kendi API’ni Yazmak” 3 Seviyede Olur

```txt
| Seviye | Ne Yaparsın                                  | Ne Zaman?                        |
| ------ | -------------------------------------------- | -------------------------------- |
| 1      | JS ile sınıf yazarsın (`FakeStorage`)        | Eğitim, test, taklit için        |
| 2      | WASM ile C++ kodunu bağlarsın                | Performans gerekiyorsa           |
| 3      | Tarayıcıya native kod (C++) katkısı yaparsın | Çok özel, profesyonel ihtiyaçlar |
```

## Node.js’te API Yazmak Daha Kolay

Tarayıcı yerine Node.js ortamındaysan `native addon` yazmak daha basittir.

- Node.js, `N-API` veya `node-addon-api` modülüyle C++ eklentisi yazmana izin verir.
- Böylece kendi `.node` dosyanı üretir ve JavaScript’ten şu şekilde çağırırsın:

```js
const benimAddon = require('./build/Release/benimaddon.node');
benimAddon.birIslev(); // C++ function
```

## Son
JavaScript sadece vitrindir. Perde arkasında C++ çalışır.
Ama bu katmanlar burada bitmez.

PHP’nin motoru da C ile yazılmıştır.
C’nin kendisi, makine koduna derlenir.
Makine kodu, işlemcinin komut seti mimarisine(ISA) göre çalışır.
O mimari bile transistörlerin davranışına dayanır.

Yani…

Her yazılım katmanı, bir alttaki soyutlamaya yaslanır
ve her soyutlama bize daha az detayla daha fazla güç verir.

Yazılım, atomlara değil, anlamlara hükmetme sanatıdır.
Katmanlıdır, çünkü karmaşayı yönetmenin tek yolu budur
ve bu yüzden soyutlama sadece bir araç değil, bir zorunluluktur.

Gerçek gücün nerede olduğunu anlamak istiyorsan,
işlerin en kolay göründüğü yere değil, en derine bakman gerekir ;)

