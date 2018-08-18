---
title:  "SDCC ve Code::Blocks"
date:   2018-08-18 16:16:16
categories: [c]
tags: [c]
---

Bu yazımızda Sdcc derleyicisini kullanarak Code::Blocks Ide'si üzerinde stm8 için uygulama geliştireceğiz.

Sdcc : 8-bit mikroişlemciler için geliştirilmiş derleyicidir. Ansi-C standartlarını destekler. Intel 8051 tabanlı mikrodenetleyiciler, Zilog Z80 tabanlı mikrodenetleyiciler, Microchip Pic16 ve Pic18 tabanlı mikrodenetleyiciler dahil birçok mikrodenetleyici ailesi için kod üretebilir.

Code::Blocks : Açık kaynaklı, farklı platformları destekleyen, ücretsiz bir C/C++ tümleşik geliştirme ortamıdır.

ST Visual Develop : ST firmasısın mikroişlemcilerini programlamak için kullanılan programdır. Mikroişlemcinin hafızasını ve opsiyon baytlarını okumak, yazmak veya kontrol etmek için kullanılır.

İlk olarak sırasıyla Sdcc, Code::Blocks ve STVP programlarını yüklüyoruz.

Daha sonra Code:Blocks çalıştırıyoruz.
En üstte bulunan "Settings" sekmesinden "Compiler" kısmını açıyoruz.
![image](/images/posts/codeblocks/code-blocks-1.jpg)

Burada Selected compiler kısmından "Small Device C Compiler" seçiyoruz.
"Set as default" tıklayarak varsayılan derleyicimiz olmasını sağlıyoruz.
![image](/images/posts/codeblocks/code-blocks-2.jpg)


Artık örnek proje oluşturup, Stm8 için gerekli ayarları yapabiliriz.

Proje oluşturmak için en üstte "File" sekmesinden "New" ==> "Project" açıyoruz.
![image](/images/posts/codeblocks/code-blocks-3.jpg)

Açılan sayfadan "Empty project" seçiyoruz ve devam ediyoruz.
![image](/images/posts/codeblocks/code-blocks-4.jpg)

Gelen sayfadan, Projenin ismini, dosya ismini, kaydedilecek dizini ayarlıyoruz ve devam ediyoruz.
![image](/images/posts/codeblocks/code-blocks-5.jpg)

Gelen sayfada Complier seçimini yapıyoruz. Varsayılan olarak "Small Device C Compiler" seçili olduğu için değiştirmemize gerek yok.
Bu projemizde Debug kullanmayacağımız için "Create Debug configuration" yanındaki seçimi işaretlemeyi kaldırıyoruz.
Sadece "Create Release configuration" yanındaki seçimi işaretliyoruz ve bitiriyoruz.
![image](/images/posts/codeblocks/code-blocks-6.jpg)

Artık projemizi oluşturduk. Derleyici ayarlarını değiştirerek, Stm8 için çalışmasını ayarlıyoruz.

Proje isminin üzerine sağ tıklıyoruz ve en alttaki "Properties" açıyoruz.
![image](/images/posts/codeblocks/code-blocks-7.jpg)

Açılan sayfadan "Build Targets" sekmesine geliyoruz.
Type kısmında "Console application" seçiyoruz.
Output filename kısmına "bin\Release\${PROJECT_NAME}.hex" yazıyoruz.
Bu şekilde proje ismiyle hex üretilmesini sağlıyoruz.
Daha sonra alt tarafta "Auto-generate filename extension" yanındaki seçimi kaldırıyoruz.

Sol alt kısmında bulunan "Build option" butonuna basarak derleyici ayarlarına giriyoruz.
![image](/images/posts/codeblocks/code-blocks-8.jpg)

Açılan pencerede "Compiler settings" sekmesinden "Compiler Flags" kısmına geliyoruz ve Stm8 için gerekli bayrakları işaretliyoruz.

	a) ISO C99 with SDCC extension [--std-sdcc99]
	
	b) Optimize for code size rather then speed [--opt-code-size]
	
	c) STMicroelectronics STM8 [-mstm8]
	
	d) Large model programs [--model-large]
	
	e) Intel Hex [--out-fmt-ihx]

Bu bayrakları işaretledikten sonra çıkıyoruz.
![image](/images/posts/codeblocks/code-blocks-9.jpg)

Projemiz için gerekli bütün ayarları yaptık.
Artık test etmek için "main.c" dosyası ekleyeceğiz ve led blinking uygulaması yapacağız.
Main dosyası eklemek için en üstte "File" sekmesinden "New" ==> "Empty file" açıyoruz.
![image](/images/posts/codeblocks/code-blocks-10.jpg)

Gelen uyarıda evete tıklıyoruz.
Böylelikle oluşturacağımız dosya, projemize eklenmiş olacak.
![image](/images/posts/codeblocks/code-blocks-11.jpg)

Gelen pencerede "Dosya Adı" kısmını "main.c" şeklinde dolduruyouz ve kaydediyoruz.
![image](/images/posts/codeblocks/code-blocks-12.jpg)

main.c dosyasını aşağıdaki örneğimizdeki gibi dolduruyoruz.

[Uyguluma örneğimiz](https://github.com/bketen/Stm8-Sdcc-Example){:target="_blank"}

Artık projemizi "Build" ettiğimiz zaman "\bin\Release" klasörü altında .hex dosyamız oluşmuş olacak.