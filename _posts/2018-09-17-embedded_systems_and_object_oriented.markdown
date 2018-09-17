---
title:  "Gömülü Sistemler ve Nesne Yönelimli Programlama"
date:   2018-09-17 17:29:16
categories: [c++]
tags: [c++]
---

Gömülü sistemlerde kısıtlı bellek kapasitesi ve görece yavaş olmaları sebebiyle nesne yönelimli programlama tercih edilmez. Ancak günümüzde işlemcilerin gelişmesi ve fiyatlarının daha makul seviyelere gelmesi sebebiyle nesne yönelimli programlama yeni projeler için düşünülebilir.


Biz burada örnek olması için Stm8 için Gpio sınıfı oluşturacağız ve birkaç fonksiyon ekleyeceğiz. Önce boş Gpio sınıfı oluşturuyoruz.

```cpp
class Gpio {

	private:

	public:
  
};	// Boş sınıfımız
```

Boş sınıfımızı oluşturduktan sonra ilk olarak pinimizin port ve pin numaralarını saklamak için birer tane "data member" ekliyoruz. Port için "GPIO_TypeDef*" tipinde, pin için ise "GPIO_Pin_TypeDef" tipinde üye tanımlıyoruz. Bu tanımlamalar stm8 için hazırlanmış standart kütüphaneler içerisinde tanımlı.

```cpp
class Gpio {

	private:
		GPIO_TypeDef* port;
		GPIO_Pin_TypeDef pin;
	public:
  
};
```

Daha sonra bu pinimizin Giriş/Çıkış, Pull-Up/Pull-Down gibi durumlarını belitmek için kullanacağımız kendi tanımlamalarımızı ekliyoruz. Bu tanımlamalar için enum yapısını kullanacağız.

```cpp
class Gpio {

	private:
		GPIO_TypeDef* port;
		GPIO_Pin_TypeDef pin;
	public:
		enum GpioModeType {           /* Possible mode types */
			OUTPUT,	//Cikis
			INPUT	//Giris
		};

		enum GpioIOType {             /* Possible input or output types */    
			OPEN_DRAIN,
			PUSH_PULL,
			FLOAT,
			PULL_UP
		};

		enum GpioInterruptSpeedType { /* Possible interrupt(input) or speed(output) types */
			NO_INTERRUPT,	//External kesme aktif
			SET_INTERRUPT,	//External kesme pasif
			MHZ_2,
			MHZ_10
		};
};
```

Böylece pinin bilgilerini girmek için gerekli değişkenleri hazırlamış olduk.


Artık pinimiz için gerekli olan modların seçilmesi için üye fonksionlarımızı yazabiliriz. Sırasıyla Giriş/Çıkış modu seçimini, bu modların kullanım şeklini (Open_Drain/Push_Pull veya Float/Pull_Up) ve kesme/hız ayarlarının yapılmasını ayarlayacağız.


Giriş/Çıkış mod seçimi için sınıf tanımlamamıza 'void setMode(Gpio::GpioModeType Mode);' ekliyoruz. Sınıf dışarısında 'setMode' fonsiyonumuzu tamamlıyoruz. Bu fonksiyonumuzda eğer Mod seçimi Output(Çıkış) seçilirse DDR registerının ilgili pini set ediliyor veya Input(Giriş) seçilmesi durumunda resetleniyor.

```cpp
void Gpio::setMode(Gpio::GpioModeType Mode){  
  if (Mode == OUTPUT) { port->DDR |= pin; }
  else if (Mode == INPUT) { port->DDR &= (uint8_t)(~(pin)); }
}
```

Daha sonra aynı işlemleri Giriş/Çıkış mod kullanım şekli ve kesme/hız durumları içinde yazıyoruz.

Bu kısma kadar geldikten sonra artık bu fonksiyonlarımızın kullanılması için ilklendirme fonksiyonu oluşturuyoruz. Aynı önceki işlemlerdeki gibi önce sınıfımızın içerisinde tanımlamamızı yapıyoruz.


'void Init( Gpio::GpioModeType Mode, Gpio::GpioIOType IOtype, Gpio::GpioInterruptSpeedType IStype);'


Artık fonksiyonumuzu tamamlayabiliriz.

```cpp
void Gpio::Init( Gpio::GpioModeType Mode, Gpio::GpioIOType IOtype, Gpio::GpioInterruptSpeedType IStype)
{
	setMode(Mode);
	setIOtype(IOtype);
	setIStype(IStype);
}
```

Artık kurucu fonksiyonumuzu ekleyebiliriz.

```cpp
Gpio::Gpio(GPIO_TypeDef* Port,
                GPIO_Pin_TypeDef Pin,
                Gpio::GpioModeType Mode,
                Gpio::GpioIOType IOtype,
                Gpio::GpioInterruptSpeedType IStype)
{
	port = Port;
	pin = Pin;
	setMode(Mode);
	setIOtype(IOtype);
	setIStype(IStype);
}
```

Yukarıdaki gibi kurucu fonksiyonumuzuda ekledikten sonra sınıfımızı kullanmaya başlayabiliriz.


Main dosyasında Gpio(GPIOB, GPIO_Pin_6, Gpio::OUTPUT, Gpio::PUSH_PULL, Gpio::MHZ_10) şeklinde pinimizi ayarlayabiliriz.


Daha kapsamlı [Gpio sınıfı](https://github.com/bketen/stm8plus/tree/master/library/gpio){:target="_blank"} ve [örnek uygulaması](https://github.com/bketen/stm8plus/tree/master/examples/button){:target="_blank"} için tıklayabilirsiniz.