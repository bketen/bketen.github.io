---
title:  "Ultrasonik Mesafe Sensörü"
date:   2018-07-25 15:52:16
categories: [c]
tags: [c]
---

Ultrasonik mesafe sensörü çalışma prensibi:

Sensör üzerinde ultrasonik alıcı ve verici bulundurmaktadır.
Vericiden gönderilen ultrasonik dalganın, cisim üzerinden yansıyarak alıcıya geri dönmesi prensibiyle çalışır.

Hc-sr04 ultrasonik mesafe sensörü kullanımı:

Sensörün tetikleme girişine 10us'den büyük bir tetikleme sinyali uygulanır.
Tetiklemeden sonra sensör vericisi 8 tane 40KHz ultrasonik dalga yollar.
Dalgalar yollandıktan sonra Echo girişi "high" seviyeye çekilir.
Bu dalgalar alıcıya ulaştığında Echo girişi tekrar "low" seviyeye düşürülür.
Echo pininin "high" seviyesinde kalma süresi, cisim ile sensör arasındaki mesafenin ölçülmesinde kullanılır.

![image](/images/posts/hc-sr04_timig_chart.png)

Mesafenin Hesaplanması:

`V = 0.0331*(sqrt(1+(T/273))`

V : Sesin hızı

T : Sıcaklık (santigrad)

0.0331: 0°C derece havadaki ses hızı(cm/mikrosaniye)


Yukarıdaki formülden o andaki ortam sıcaklığına göre sesin hızını buluyoruz.(cm/mikrosaniye cinsinden)

`Mesafe = ( Süre * V ) / 2`

Echo pininden ölçtüğümüz süreyi, sesin hızıyla çarparak mesafeyi buluyoruz.
Ancak dalgalar hem cisime gidip hemde cisimden döndüğü için mesafeyi ikiye bölüyoruz.


Örnek:

T = 23°C için V = 0.0345 olur.

Geçen süreyi 160 mikrosaniye kabul edersek mesafemiz 2.7573cm olarak bulunur.


Stm8 için örnek uygulama:

![image](/images/posts/hc-sr04_schematic.png)

{% gist bketen/a54bbcfcf3af08de405a8323983c959d %}

Mikroişlemcimizde geçen süre 58'e bölünerek mesafe bulunmaktadır.
Buradaki 58, 1 santimetreyi kaç mikrosaniyede gittiğidir.
