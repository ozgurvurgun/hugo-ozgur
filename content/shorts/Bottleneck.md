---
title: "Bottleneck"
date: 2025-07-14
draft: false
---

- Türkçesi “şişe ağzı” anlamına geliyor.

Bu kavramı bir analojiyle anlamaya çalışalım. Aslında kelimenin kendi anlamı bile meseleyi güzel özetliyor. Üzerine biraz yürüyelim.

Pet şişeler genelde ağız kısmına doğru daralır. Şişenin içindeki sıvıyı dışarı çıkarmak isterseniz, ister istemez bu dar ağızdan geçirmek zorundasınız. Yani dış etkilerden bağımsız bir senaryoda, sıvının dışarı çıkış hızını şişenin ağzı belirler.

---

- IT dünyasına gelirsek, bu analoji şu şekilde birebir oturur:

Çok taşaklı bir sunucunuz, hayvani bir internet hızınız olabilir. API’niz 20ms’de cevap dönebilir. Ama veritabanınız tırt ise... geçmiş olsun. Sistemdeki diğer tüm servisler ışık hızında çalışsa da, hepsi veritabanının cevap vermesini beklemek zorunda kalır.

Bu senaryoda veritabanı, sistemin zayıf halkası yani **şişenin ağzı** oluyor. Şişenin alt kısmında servisler şıkır şıkır çalışıyor ama dar ağız yüzünden tüm akış yavaşlıyor. İşte bottleneck dediğimiz şey tam olarak bu.