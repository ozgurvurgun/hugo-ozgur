+++
title = 'Nostr'
date = 2025-04-23T07:22:22-04:00
+++

Nostr - İletişimin Özgürlüğü

Microblogging konusunda uzun zaman önce kullandığımız FriendFeed platformunun hep özlemini çekiyordum. O zamanlardan tanıştığım pek çok arkadaşımla de ara ara facebook'un öldürdüğü FriendFeed'i anar dururuz. Bizim için hem ilk sosyal medya deneyimi, hem de yaklaşımıyla hep farklı bir platform olmuştu. 

Dünya çapında, uzun süredir sosyal medya platformlarının hep benzeri şekilde tek elle yönetilmesi, gücün merkezde olması sebebiyle ekonomik veya siyasal sebeplerle özgürlüğümüzün elimizden kolayca alınabiliyor olması sebebiyle, pek çok yönden çalışmalar yapıldığını takip ediyoruz. Neden bu durumdayız, neden özgür olamıyoruz, hem de internetin varoluşundan beri özgürlükçü olduğunu bilirken. 

Yapısı gereği internetin kendisi tamamen özgürlükçü. Protokol olarak bir ağ oluşturulması ve kullanılması tamamen engellenemez bir yapıda. Intranet üzerinden oynadığımız oyunları düşünün örneğin. Bunun büyük alanlara yayılması internet. Sahibi olduğumuz bir alet olan bilgisayarı kablolarla veya kablosuz olarak birbirine bağlamamızın önüne kim geçebilir? 

Fakat, gelişim düzeyinde, haliyle daha merkezi yapılar oluştu ve internet, herkes tarafından varedilebiliyor olsa da, çoğu noktada merkezileşti. Hatta neredeyse tamamen merkezi olamayacak olan eposta protokolü bile google tarafından merkezileştiriliyordu. Duymuşsunuzdur, bazı insanlar eposta hizmetini gmail üzerinden kullandıkları için, hotmail hesabı olan kişilere eposta gönderemeyeceklerini zannediyorlar. İnternet o kadar merkezileşmiş durumda ki, bu tarz bir algıya şaşıramıyorum. 

Halbuki, eposta protokolünün doğası gereği (merkezi sanan çok az insan hariç), merkeziyetsiz bir yapıda olabileceği düşünülemiyor. Google ne kadar zorlasa da hala merkeziyetsiz olarak kullanıyoruz. 

Bu sayede ise, hiç bir devlet, hiç bir kişi, hiç bir şirket, hiç bir organizasyon, hiç bir kurum, hiç bir yapı, eposta protokolünü engelleyemiyor. Tabi ki gmail sizin hesabınızı silebilir, şirketiniz size ait olan epostayı silebilir, ama bu sizin eposta protokolünü kullanamayacağınız anlamına gelmiyor. Sadece onların verdiği hizmeti kullanamıyorsunuz. Hatta kendi domain adresinize sahipseniz, altyapı değiştirerek yine ulaşılabilir durumda kalabiliyorsunuz. Örneğin z@emre.xyz adresine sahip olduğum için, altyapım ne olursa olsun, her zaman eposta alabiliyor olacağım. Tabi ki devletler domain engelleyebildikleri için web siteme ulaşılamayabilir, ancak her bir eposta sağlayıcısı beni tek tek engellemediği sürece, ben eposta almaya devam edeceğim. Ki bu da mümkün olmayan bir senaryo olduğu için, teoride mümkün gibi görünse de pratikte mümkün olmayan bir engelleme durumu oluşuyor. Kısacası, engellenemez bir sistem diyebiliyoruz. 

Fakat biliyorsunuz ki, pek çok kişi tarafından takip edildiğiniz, bilinirliğiniz bulunan bir hesabınızı herhangi bir sosyal medya platformu, bir anda çat diye kapatabiliyor. Tabi ki yeni bir hesap açabiliyorsunuz, ancak tüm takipçileriniz, arkadaşlarınız, tanıdıklarınız, iş arkadaşlarınız, işverenleriniz, iş başvurularınız, sosyal medya çevreniz, her şeyiniz o hesapla birlikte gidebiliyor. Bu durumda ise, merkezi yapılar tamamen engelleme gücüne sahip oluyor.  

Zaman içerisinde, sosyal medya platformlarını da bir protokol çerçevesinde toplamak için, pek çok dağıtık yapılı protokol oluşturulmaya çalışıldı. ActivityPub protokolü en gelecek vaadediniydi. Fediverse çevresinde görülen pek çok platform, bu protokolü kullanarak merkeziyetsiz bir yapıda çalışmaya başladı. Ancak, bu yapının da bazı eksiklikleri vardı. Detaylarına burada girmeyeceğim ancak temelinde ölçeklenebilirlik konusunda sıkıntılar yaşamaya devam ediyor. Bununla beraber, twitter bünyesinde başlayıp ayrılan Bluesky tarafından üretilen ATProto protokolü, merkeziyetsizlik vaad etmesine rağmen, twitter bünyesinden çıkmış olması sebebiyle, pek benimsenemedi. Ki, hala atproto kullanan platformlar merkezi gibi ilerliyor. Sunucuların çoğusu aynı şirkete ait. Engellenemezliği zaten bulunmadığı için, merkeziyetsizliği de pek bir anlam ifade etmiyor. Zaten son twitter göçünde görüldüğü gibi, bluesky'da devletlerin taleplerini yerine getirip hesapları engellemeye başladı. 

Nostr ise bir platform değil, sadece bir protokol. Çok basit bir şekilde belirlenmiş olan bu protokol, platformların desteklemesiyle veya nostr tabanlı platformların ortaya çıkmasıyla bizi özgürleştiriyor. Platform ve Protokol arasındaki farklar için [Taylan](https://njump.me/npub166l9t9ckan9yh6j8pku0stszkekt0s8uhqwvddz4qr92r9w0wxcs59u7c3) tarafından çevirisi yapılmış olan [Özgürce Konuşmak](https://www.yirmibir.org/blog/ozgurce-konusmak) başlıklı yazıyı inceleyebilirsiniz. 
 
Eposta sistemlerinde olduğu gibi, takip etmek istediğiniz kişinin adresini bulmanız gerekiyor. Normalde, nostr public anahtarınız ile takipleşme sağlanıyor, ancak internet sitelerine erişmek için IP bilmeniz gerekmediği gibi, kullanıcının handle'ını bilmeniz yetiyor. (yine teknik detaylara girmiyorum) Örneğin, delirehberi@emre.xyz diye aradığınızda beni hangi istemciden olursa olsun takip etmeye başlayabiliyorsunuz. Aynı relay üzerinden olan paylaşımlarımıza erişebiliyoruz. Kafanız hemen karışmasın, birden fazla relay olduğu için ve paylaşımlar bunların hepsinden aktığı için sonuçta birbirimizi takip edebiliyoruz. 


Şimdilik, kafa karıştırmamak için kısa kesiyorum. İlerleyen günlerde kendi relay'imi de paylaşacağım. Ancak, şu an için ek bir relay'e ihtiyacınız yok. Amethyst uygulaması ile birlikte gelen relay'leri kullanarak, iletişiminizi sürdürebiliyorsunuz. 

Web üzerinden ise [Coracle](https://coracle.social/people/nprofile1qythwumn8ghj7emvda3xzmpwwfjkccte9eex2ep0qyf8wumn8ghj7mn0wd68yv339e3k7mf0qy2hwumn8ghj7un9d3shjtn9d4ex2tnc09az7qpqgmeu0wenescpjpymwmwgnkaedc6vy3aamf5tdtvxxf5z0yll3gdq5ead4v) arayüzünü kullanabilirsiniz. 

IOS üzerinde kullanmak isterseniz, [Damus](https://apps.apple.com/us/app/damus/id6445784580) uygulamasını kullanabilirsiniz.

Zaman içerisinde daha fazla nostr içeriği üreteceğim. Takipte kalınız. 

Teknik detaylar için ileri okuma isterseniz, [nostr](https://nostr.com/) protokolünün kendi sitesini inceleyebilirsiniz.


--


Önerileri ve düzeltmeleri için [Taylan](https://njump.me/npub166l9t9ckan9yh6j8pku0stszkekt0s8uhqwvddz4qr92r9w0wxcs59u7c3)'a teşekkür ederim. 
