---
title: "What is Angular"
date: 2025-07-15
draft: false
weight: 1
---

## Angular'ın Felsefesi ve Paradigması

Angular bir "framework"tür, bir "library" değil. Bu şu anlama gelir:

- Ne yapman gerektiğini değil, nasıl yapman gerektiğini söyler.
- Bir yapı ve düzen silsilesi sunar.
- Geliştirici özgürlüğünü azaltır ama büyük ölçekli projelerde tutarlılık, test edilebilirlik ve bakım kolaylığı sağlar.

## Paradigması

- Component-Based Architecture (React ile benzer)
- Declarative Template Syntax (HTML içinde Angular'ın syntax'ı kullanılabilir, bildiğin template engine işte)
- Two-Way Data Binding (Kısaca UI ile JS arasına kablo çekmişler, değişkende bir değişiklik olunca anında UI değişiyor; aynı şekilde UI’da bir değişiklik olunca değişken güncelleniyor gibi bir şey)
- Dependency Injection (Bağımlılık sokuşturma)
- RxJS ile Reactive Programming (Kısaca bir veri kaynağında bir değişiklik olunca bu otomatik bir zincirleme reaksiyonu tetikliyor, bir şeyler yapılıyor. Temelden anlamak istersen JS’de yerleşik olarak bulunan `Proxy` API ile basit reactive programlama örnekleri yapabilirsin)

## Angular Kullanmanın Avantajları

- Kurumsal uygulama yapacaksan bunu kullan diyorlar, ne alaka aq.
- Routing, HTTP, Form Operations, test... her şey tek pakette.
- Google geliştirmiş. Google'ın şak diye support kesmeleri meşhurdur, o yüzden dikkat etmek lazım :) (Google gibi bir devin arkasında olması hem avantaj hem de dezavantaj lol)
- CLI ile component, service, interceptor... oluşturmak çok kolay (gerçi bu her framework'te var ama neyse)


## Angular Kullanmanın Dezavantajları

- Yüksek bir öğrenme eğrisi söyleniyor, ne kadar doğru bilemem ama  bana pek öyle gelmedi. Günün sonunda klasik web app geliştirmenin üzerine bir abstraction geçirmiş adamlar; paradigmik olarak neyi nasıl yapmak istediklerini iyi anlarsanız olayın zor bir yanı yok.
- Boilerplate kod fazla, ufacık şey için zibilyon tane dosya oluşturuyorsun.
- Bundle size büyük deniyor, buna bakmadım; bakınca güncellerim burayı. Eğer öyleyse bu da eksi bir yön.
- Küçük bir app yapacaksan hiç gerek yok, React yapıştır geç.
- Esnek değil; Angular neyi nasıl istiyorsa o şekilde yapman gerek çoğu zaman. Bu aslında yerine göre iyi de olabilir.


`Bugün Angular yazarsın, yarın React. Kullandığın teknolojiler sürekli değişir; mesele Web’in ne olduğunu ve nasıl çalıştığını anlamak.`