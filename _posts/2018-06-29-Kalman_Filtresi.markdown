---
title:  "Kalman Filtresi"
date:   2018-06-29 23:48:23
categories: [c]
tags: [c]
---

Kalman filtresi, önceki ve şu anki verileri kullanarak sonraki verilerin tahmin edilmesidir.

![image](/images/posts/kalman-filter-1.jpg)

Filtre iki kısımdan oluşur. Tahmin etme ve düzeltme.

Tahmin Etme:
Çıkışın ve Hata Kovaryasyonunun tahmini yapılır.

`x(k) = x(k-1) + u(k)`

`e(k) = e(k-1) + Q`

x(k): çıkış tahmini değeri
x(k-1): önceki durumun çıkış değeri
u(k): kontrol sinyali
e(k): hata kovaryasyon tahmini değeri
e(k-1): önceki durumun hata kovaryasyonu
Q: sistemden kaynaklı hata

Düzeltme:
Düzeltme adımında, önceki durumun verilerine göre kalman kazancı hesaplanır.
Kalman kazancına göre, Çıkış ve Hata kovaryasyon değerinde düzeltme yapılır.

`Kk = e(k) / ( e(k) + R )`

`x(k) = x(k) + Kk * ( z(k) - x(k) )`

`e(k) = ( 1 - Kk ) * e(k)`

Kk: Kalman Kazancı
R: Ölçümden kaynaklı hata
z(k): Sensörden ölçülen değer
x(k): Hesaplanan kalman çıkışı
e(k): Hesaplanan hata kovaryasyonu

![image](/images/posts/kalman-filter-2.png)

Sistem modelinin doğruluğunun arttılması ve ölçüm hatalarının azaltılması ile kalman filtresinin sonuçları iyileşir.

C dili için örnek:

{% gist bketen/dc708bd8ff85b306efe29ba21dc7125e %}
