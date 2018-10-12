---
title:  "Modbus ve Freemodbus'ın Port Edilmesi"
date:   2018-09-17 17:29:16
categories: [c]
tags: [c]
---

Modbus, Modicon firmasının geliştirdiği haberleşme protokolüdür.
Seri port (Modbus RTU, Modbus ASCII) veya ethernet (Modbus TCP) üzerinden kullanılabilir.
Açık protokol olduğu için çok fazla kullanıcısı bulunmaktadır.
Sistemde bir master ve en fazla 247 slave cihaz bulunabilir.

Mesaj Yapısı

Mesajın en başında hangi slave ile haberleşilecek ise onun adres bilgisi koyulur.
Ardından yapılacak işlemin fonksiyon kodu eklenir.
Devamına veri eklenir.
En sonuna da hata kontrol değeri eklenir.

Mesajımız `Adres + Fonksiyon Kodu + Veri + Hata Kontrol Değeri` şeklinde olur.

Freemodbus kütüphanesinin Stm8 için port edilmesi:

[Stm8l discovery kiti](https://www.st.com/en/evaluation-tools/stm8l-discovery.html){:target="_blank"} (slave) ile bilgisayar (master) arasında, seri port üzerinden ModbusRTU haberleşmesi yapacağız.

İlk olarak [freemodbus](https://sourceforge.net/projects/freemodbus.berlios/){:target="_blank"} kütüphanesini projemize ekliyoruz ve dizine kaydediyoruz.
![image](/images/posts/modbus/modbus-1.jpg)

Daha sonra main.c dosyasının en başına ' #include "mb.h" ' ekleyerek kütüphaneyi projemize dahil ediyoruz.

Artık kütüphaneyi eklediğimize göre gerekli ayarlamaları yapmaya geçebiliriz.

İlk olarak `port.h` dosyası içerisinde;

1) En başta `#include "stm8l15x.h"` ekliyoruz. Böylece kütühane içerisinde stm8 tanımlamalarına erişim sağlıyoruz.
	
2) Kesmeleri aktif etmek için `ENTER_CRITICAL_SECTION()` fonksiyonunu tanımlıyoruz.
Bunun için fonksiyon oluşturabiliriz veya Makro tanımlayabiliriz.
Biz örneğimizde makro tanımlayacağız.

' #define ENTER_CRITICAL_SECTION()        ( disableInterrupts() ) '
	
3)Kesmeleri devre dışı bırakmak için de aynı yukarıdaki gibi makro tanımlıyoruz.

' #define EXIT_CRITICAL_SECTION()         ( enableInterrupts() ) '

İkinci olarak `portserial.c` dosyası içerisinde;

1) Rx ve Tx Kesmelerini etkinleştirmek ve devredışı bırakmak için gerekli kodlarla `vMBPortSerialEnable( BOOL xRxEnable, BOOL xTxEnable )` fonksiyonunu tamamlıyoruz.
	
```c
void vMBPortSerialEnable( BOOL xRxEnable, BOOL xTxEnable )
{
/* If xRXEnable enable serial receive interrupts. If xTxENable enable
* transmitter empty interrupts.
*/
if(xRxEnable == TRUE)
{
	USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);
}
else
{
	USART_ITConfig(USART1, USART_IT_RXNE, DISABLE);
}
 
if(xTxEnable == TRUE)
{
	USART_ITConfig(USART1, USART_IT_TXE, ENABLE);
}
else
{
	USART_ITConfig(USART1, USART_IT_TXE, DISABLE);
}
}
```
	
2) Seri portu açmak için `xMBPortSerialInit( UCHAR ucPORT, ULONG ulBaudRate, UCHAR ucDataBits, eMBParity eParity )` fonksiyonunu tamamlıyoruz.

```c	
BOOL xMBPortSerialInit( UCHAR ucPORT, ULONG ulBaudRate, UCHAR ucDataBits, eMBParity eParity )
{
	USART_Parity_TypeDef parity = USART_Parity_No;
	USART_WordLength_TypeDef wordLength = USART_WordLength_8b; 
	
	switch ( eParity )
	{
	case MB_PAR_NONE:
		parity = USART_Parity_No;
		wordLength = USART_WordLength_8b;
		break;
	case MB_PAR_ODD:
		parity = USART_Parity_Odd;
		wordLength = USART_WordLength_9b;
		break;
    case MB_PAR_EVEN:
		parity = USART_Parity_Even;
		wordLength = USART_WordLength_9b;
		break;
	}
  
CLK_PeripheralClockConfig(CLK_Peripheral_USART1, ENABLE);
GPIO_ExternalPullUpConfig(GPIOC, GPIO_Pin_3, ENABLE);
GPIO_ExternalPullUpConfig(GPIOC, GPIO_Pin_2, ENABLE);
  
/* USART configuration */
USART_Init(USART1, ulBaudRate, wordLength, USART_StopBits_1, parity, USART_Mode_Rx | USART_Mode_Tx);
  
return TRUE;
}
```
	
3) Tx kesmesi içerisinden `pxMBFrameCBTransmitterEmpty()` fonksiyonunu, Rx kesmesi içerisinden `pxMBFrameCBByteReceived()` fonksiyonunu çağırıyoruz.
	
```c
INTERRUPT_HANDLER(USART1_TX_TIM5_UPD_OVF_TRG_BRK_IRQHandler,27)
{
	pxMBFrameCBTransmitterEmpty();
}
```

```c
INTERRUPT_HANDLER(USART1_RX_TIM5_CC_IRQHandler,28)
{  
	USART_ClearITPendingBit(USART1, USART_IT_RXNE);
	pxMBFrameCBByteReceived();
}
```
	
4)Veri alımı için `BOOL xMBPortSerialGetByte(CHAR* pucByte)` fonksiyonunu, Veri gönderimi için `BOOL xMBPortSerialPutByte(CHAR ucByte)` fonksiyonunu tamamlıyoruz.
	
```c
BOOL xMBPortSerialGetByte( CHAR * pucByte )
{
	*pucByte = USART_ReceiveData8(USART1);
	return TRUE;
}
```

```c
BOOL xMBPortSerialPutByte( CHAR ucByte )
{
	USART_SendData8(USART1, ucByte);
	return TRUE;
}
```
	
Paket başlangıcı olduğunu belirten 3.5 karakterlik süre ile paketin bozulduğu anlaşılan 1,5 karakterlik süreyi anlamak için Zamanlayıcı kullanılır. Bunun için `porttimer.c` dosyası içerisinde;

1) Zamanlayıcıyı ayarlamak için `BOOL xMBPortTimersInit( USHORT usTim1Timerout50us )` fonksiyonu tamamlanır.
	
```c
BOOL xMBPortTimersInit( USHORT usTim1Timerout50us )
{
	/* Enable TIM1 CLK */
	CLK_PeripheralClockConfig(CLK_Peripheral_TIM1, ENABLE);

	//50uS * usTim1Timerout50us saniyede bir kesme
  
	// Time base configuration 
	TIM1_TimeBaseInit(800, TIM1_CounterMode_Up, usTim1Timerout50us, 0);
	TIM1_ClearFlag(TIM1_FLAG_Update);
	TIM1_ITConfig(TIM1_IT_Update, ENABLE);
 
	return TRUE;
}
```
	
2) Zamanlayıcıyı etkinleştirmek `void vMBPortTimersEnable()` fonksiyonu, devredışı bırakmak için `void vMBPortTimersDisable()` fonksiyonu tamamlanır.
	
```c
void vMBPortTimersEnable()
{
	TIM1_SetCounter(0);
	TIM1_Cmd(ENABLE);
}
```

```c
void vMBPortTimersDisable()
{
	TIM1_Cmd(DISABLE);
}
```
	
3) Zamanlayıcı kesmesi içerisinde `pxMBPortCBTimerExpired()` fonksiyonu çağırılarak taşma durumları kontrol edilir.
	
```c
INTERRUPT_HANDLER(TIM1_UPD_OVF_TRG_COM_IRQHandler,23)
{ 
	TIM1_ClearITPendingBit(TIM1_IT_Update);
	( void )pxMBPortCBTimerExpired();
}
```

Kit üzerinde yapmamız gereken ayarlar tamamlandı.
Bilgisayarı master olarak kullanmak için [ModbusPoll](https://www.modbustools.com/modbus_poll.html){:target="_blank"} programını kullanacağız.
Programın kullanımı oldukça basit.
"Setup" sekmesinin altında "Read/Write Definition" kısmına girerek hangi slave olduğunu, hangi fonksiyonu kullanacağımızı, hangi adresten başlayacağımızı seçiyoruz.
"Connection" sekmesinin altında "Connect" kısmına girerek seri port ile ilgili ayarları yapıyoruz.
Artık slave kitimiz ile bilgisayar arasında haberleşme başlamıştır.