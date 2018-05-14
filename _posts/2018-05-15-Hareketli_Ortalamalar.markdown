---
title:  "Hareketli Ortalamalar"
date:   2018-05-15 01:13:23
categories: [c]
tags: [c]
---

Giriş sinyalimizi gürültülerden temizlemek için kullanabileceğimiz basit bir yumuşatma filtresidir. Kolayca kullanılabilmesi ve basit yapısıyla anlaşılmasının kolay olmasından dolayı sinyal işlemede sıkça kullanılır. Zaman domeninde örneklenmiş sinyallerde gürültüyü azaltmak için kullanılabilir. Ancak frekans domeninde yetenekleri sınırlıdır.

![image](/images/posts/moving-averages.png)

Adından da anlaşılacağı üzere çıkışın sürekli giriş değerlerinin ortalaması alınarak oluşturulmasıdır.

`q(m) = (d(m)+d(m-1)+d(m-2)+...+d(m-n+1))/n`

Bir örnek vermek gerekirse boyutu 4 olarak seçilmiş filtremizin 10. örnek için cevabı aşağıdaki gibidir.

`q(10) = (d(10)+d(9)+d(8)+d(7))/4`

Filtenin uygulanması için önce filtre boyutu kadar veri saklanır ve bu verilerin aritmetik ortalaması çıkış olarak kullanılır. Her yeni veri geldiğinde, en eski değer çıkarılarak yerine bu yeni verimiz koyulur ve tekrar ortalamanın alınması şeklinde devam eder.

C dili için örnek:

{% gist bketen/b356ff9c29093f193a1cc793233d1446 %}

ADC pinlerinin okunmasında hareketli ortalamalar filtresinin uygulandığı örnek uygulama

[Stm8 Read ADC with moving average filter] (https://github.com/bketen/Stm8l-Moving-Average-ADC)
