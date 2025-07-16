---
title: Reaktif Programlama
date: 2025-07-15
draft: false
tags:  #tr #hack
---

## Nedir Bu Reaktif Programlama

Reaktif programlama, zaman içinde değişen verilerle çalışan sistemler için geliştirilmiş bir programlama paradigmasıdır. Temel fikir, değişen verilere otomatik tepki veren sistemler oluşturmaktır. Geleneksel yöntemde veriyi çeker, kontrol eder ve ona göre reaksiyon alırız. Reaktif programlamadaysa veri otomatize bir şekilde akar.


## Neden Ortaya Çıktı?

Modern uygulamalar artık çok fazla gerçek zamanlı veriyle çalışıyor, çok sayıda kullanıcıya hizmet veriyor. Yani aynı anda birden fazla işlemi (asenkron akışı) iyi yönetmek gerekiyor.

Geleneksel imperative programlama yaklaşımıyla bu tür eş zamanlı ve asenkron sistemlerde kodun okunabilirliğini ve sürdürülebilirliğini yönetmek zor olabiliyor.


## Callback Hell ve Akış Kontrolü

Özellikle JavaScript dünyasında callback hell (iç içe geçmiş fonksiyon cehennemi) ortaya çıkıyor. Olaylar zincirleme halde akıyor ama bu olaylar kodun düzensiz ve okunması zor bir hal almasına sebep oluyor.


## Reactive UI Gereksinimleri

UI tarafında bir butona basılması, bir formun içeriğinin değişmesi veya bir WebSocket mesajının gelmesi gibi olaylara anında tepki verme ihtiyacı doğdu. Bunları yönetmenin daha doğal, daha basit bir yolu gerekiyordu.


## Ne Tür Problemleri Çözüyor?

- Veri ile senkron kalma: UI ile state'in her zaman birbirine uyumlu kalmasını sağlar.
- Asenkron veri akışını yönetme: Timer, kullanıcı etkileşimi, AJAX, WebSocket gibi kaynaklardan gelen verileri yönetmeyi kolaylaştırır.

## Gerçek Hayattan Basit Bir Örnek

Hadi işi daha iyi anlayabilmek için web üzerinden örnekleyelim.  
Bir input alanımız olsun. Input değeri değişince bir değişken güncellensin, değişken değeri değişince de input’un içeriği otomatik güncellensin.  

Bu gayet basit bir görev, değil mi?

JavaScript'te bulunan reaktif programlama destekleyici yapıları kullanmadan bile UI ve değişken değerlerini sürekli kontrol eden ve değişiklikleri algılayıp gerekli atamaları yapan bir sistem yazabiliriz. Hatta daha temiz iş yapmak için Proxy API’sini kullanarak aşağıdaki gibi reaktif bir sistem yazabiliriz:


```HTML
<input id="myInput" />

<script>
  const input = document.getElementById("myInput");

  const state = new Proxy({ name: "" }, {
    set(target, key, value) {
      target[key] = value;

      if (key === "name" && input.value !== value) {
        input.value = value;
      }

      return true;
    }
  });

  input.addEventListener("input", (e) => {
    if (state.name !== e.target.value) {
      state.name = e.target.value;
    }
  });

  state.name = "hello world";
</script>
```

Bu haliyle oldukça statik bir yapı, ama kodu daha dinamik hale getirip farklı veri kaynaklarıyla çalışacak şekilde de yazabiliriz.
Ama şu soruyu da sormak lazım: "Ne gerek var?"

## Kütüphane Kullanmak: Kolaylık mı, Karmaşa mı?

Bu kodu RxJS gibi reaktif programlama destekleyici kütüphanelerle yazarsak evet, daha okunaklı ve sürdürülebilir olabilir. Ama proje büyüdükçe bu da yönetmesi zor bir hale gelebilir. Bu, şuna benzer:

> Bir oyunu sıfırdan, herhangi bir library kullanmadan yazmak çok zor. Bir dünya spesifikasyonu var. “Uğraşmayayım, THREE.js kullanayım” dersin, okey işleri kolaylaştırır.
Ama anasını satayım onu kullanmak da zor.
En sonunda “en temizi Unity kullanayım, deli gibi abstraction olsun, ben sadece işimi gerçeklemeye bakayım” dersin.

Yani araçlar işini kolaylaştırır ama onların da öğrenme eğrisi, kuralları ve mimarisi vardır.

## Aynı Mantığın React Versiyonu

Aşağıda, yukarıdaki reaktif yaklaşımın birebir React kodunu veriyorum:

```jsx
import { useState } from "react";

function App() {
  const [name, setName] = useState("Merhaba dünya!");

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <p>{name}</p>
    </div>
  );
}

export default App;
```

Nasıl ama? Mis gibi okunaklı ve anlaması çok kolay değil mi?

## Son

Günün sonunda reaktif programlama paradigması oldukça basittir:
Bazı kaynaklarda veri değişimleri olur, biz de bu değişimlere tepki veririz. Bu kadar.
Ama sistemi büyütüp karmaşıklaştırdıkça bu tepki sistemini sağlıklı ve sürdürülebilir şekilde yönetmek istersen, işte orada RxJS gibi kütüphaneler, Vue, React gibi framework’ler devreye girer.

