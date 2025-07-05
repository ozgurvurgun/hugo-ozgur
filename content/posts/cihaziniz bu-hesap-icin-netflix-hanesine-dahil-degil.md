---
title: Cihazınız bu hesap için Netflix Hanesine dahil değil.
date: 2025-07-04 
draft: false
tags:  en #hack
---

Bugün Netflix hesabına hane dışından erişmeye çalışırken ekranıma şu meşhur uyarı düştü:

> *"Cihazınız bu hesap için Netflix Hanesine dahil değil."*

"Tamam" dedim, "ben seni dahil ederim…" 😈
<!--more-->

## 🚫 Engeli nasıl gördüm?

Tam ekran bir engel:

* Arka planda video sayfası var ama oynatılmıyor.
* Üstte `Hesap Oluştur` butonu, `Geçici Olarak İzle` veya `Oturumu Kapat` seçenekleri var.


### 🧙‍♂️ Büyü neydi?

Önce engeli ortadan kaldırmak için klasik `display: none` manevrasını yaptım.
Ardından fark ettim ki video DOM’da duruyor ama kendini oynatmıyor. E tabii `play()` çağrısını da ben yaparım dedim.


### 📜 Kullandığım basit JS sihri:

```js
// Videoları muted aç, autoplay engelini aş
document.querySelectorAll('video').forEach(v => {
  v.muted = true;
  v.play();
});

// Ses açmak için:
document.querySelectorAll('video').forEach(video => {
  video.volume = 1.0; // Tam ses
});

// Tam muted kapalı + tam ses:
document.querySelectorAll('video').forEach(video => {
  video.muted = false; // Muted kapalı
  video.volume = 1.0;  // Ses tam
});
```


### ⚙️ Ne oldu?

* Netflix sayfada `video` elementini yüklüyor.
* Ama otomatik oynatmayı durduruyor.
* Ben de `play()` çağırarak tarayıcıya diyorum ki: **"Kardeşim oynat şunu."**
* Bazı tarayıcılar `muted` olmadıkça otomatik oynatmaya izin vermiyor, o yüzden ilk `play` komutu `muted = true` ile.
* Sonra `muted`’ı kapatıp `volume` %100 yaptım, ses geldi. 🎉
