![CORE_PHOTO](https://github.com/AlexGyver/GyverCore/blob/master/gyverCoreLogo.jpg)
# GyverCore for ATmega328
 **Version 2.0 from 01.02.2020**   
 Fast and light core for Arduino IDE with advanced configuration. **Only for ATmega328 boards!**  
 Based on original Arduino 1.8.9 core, all functions are replaced by faster versions, a bunch of interesting staff is added.  
 Made by **AlexGyver** and Egor 'Nich1con' Zaharov from mother Russia.  

## Install
### Automatic
- Open the Arduino IDE.
- Open the **File > Preferences** menu item.
- Enter the following URL in **Additional Boards Manager URLs**:
    ```
    https://alexgyver.github.io/package_GyverCore_index.json
    ``` 
- Open the **Tools > Board > Boards Manager...** menu item.
- Wait for the platform indexes to finish downloading.
- Scroll down until you see the **GyverCore** entry and click on it.
- Click **Install**.
- After installation is complete close the Boards Manager window.

### Manual
- Place all files from GyverCore to C:\Users\Username\AppData\Local\Arduino15\packages\GyverCore\hardware\avr\1.10.0\

## Changes
### Faster and lighter
Time of execution, us

Function        | Arduino   | GyverCore | Times faster
----------------|-----------|-----------|----------
millis			| 0.69 us	| 0.69 us	| -
micros			| 0.81 us	| 0.81 us	| -
pinMode         | 2.56 us   | 0.125 us  | 20.5     
digitalWrite    | 2.40 us   | 0.125 us  | 19     
digitalRead     | 2.06 us   | 0.063 us  | 33    
analogWrite     | 5.9 us    | 0.63 us   | 9.3   
analogRead      | 107.2 us  | 5.94 us   | 18 
analogReference | 0.19 us   | 0.19 us   | -    
attachInterrupt | 1.06 us   | 1.0 us    | -     
detachInterrupt | 0.5 us    | 0.13 us   | 3.8   
tone			| 9.0 us    | 2.25 us   | 4

Flash, byte

Function        | Arduino | GyverCore | Difference
----------------|---------|-----------|---------------
millis			| 26      | 24		  | 2
micros			| 24	  | 20		  | 4
pinMode         | 114     | 24        | 90             
digitalWrite    | 200     | 24        | 176            
digitalRead     | 190     | 24        | 166            
analogWrite     | 406     | 48        | 358            
analogRead      | 32      | 72        | -40            
analogReference | 0       | 22        | -22            
attachInterrupt | 212     | 180       | 32             
detachInterrupt | 198     | 150       | 48         
tone      		| 1410    | 740       | 670       
Serial begin    | 1028    | 166       | 862            
print long      | 1094    | 326       | 768            
print string    | 2100    | 1484      | 616            
print float     | 2021    | 446       | 1575           
parseInt        | 1030    | 214       | 816            
readString      | 2334    | 1594      | 740            
parseFloat      | 1070    | 246       | 824         

Note: **analogRead** and **analogReference** has advanced configuration  
Sketch with all of functions:
- Arduino Core: 3446 byte (11%) Flash / 217 byte (10%) SRAM
- GyverCore: 1436 byte (4%) Flash / 94 byte (4%) SRAM  

Empty sketch
- Arduino Core: 444 byte (1%) Flash / 9 byte (0%) SRAM
- GyverCore: 202 byte (0%) Flash / 0 byte (0%) SRAM  

All libraries, based on Wiring functions, works much faster. Even SPI (more than 2 times faster!)
	
### Added
- Wide Syntax Coloring Map (include names of registers and bits)
- Macros **bitToggle**(value, bit), for fast bit inversion
- Function **digitalToggle**(pin), for fast pin inversion
- Advanced ADC
	- ~~**analogStartConvert**(byte pin) - start convert from choosed pin~~
	- ~~**analogGet()** - get converted value (you can make some actions between start and get)~~
	- **analogPrescaler**(uint8_t prescl) - set prescaler for ADC. Prescaler:
		- **2**: 3.04 us per conversion
		- **4**: 4.72 us per conversion
		- **8**: 8.04 us per conversion
		- **16**: 15.12 us per conversion
		- **32**: 28.04 us per conversion
		- **64**: 56.04 us per conversion							
		- **128**: 112 us per conversion
	- In function **analogRead(pin)** you can use constants instead of pin number: **INTERNAL** (get reference voltage) and **THERMOMETR** (get core temperature). *Note: set ADC prescaler to 128*
- **uart** class is an alternative to Serial, but much faster and lighter.
- Advanced board menu
	- Bootloader select
	- Clock source and frequency select
	- Save EEPROM when erase, or not
	- Clock out
	- Selectable "system timer" - you can disable it and take control on timer 0 OVF vector
	- Replace Serial by faster uart
	- Disable or configure B.O.D.
	- You can disable default initialization of timers and ADC
	- Compiler version
		
## Board settings
**Bootloader** - bootloader selection **(flash bootloader needed)**:
- **old bootloader** - old stock bootloader
- **optiBoot** - actual stock bootloader for all new Arduino boards
- **optiBoot v8** - actual version of [optiboot](https://github.com/Optiboot/optiboot) with correct fuses
- **without bootloader** with fuses for upload sketch in all of 32 kBytes ща Flash!
---
**Clock** - clock source and frequency selection **(flash bootloader needed)**:
- **External 16 MHz**
- **External 8 MHz**
- **Internal 8 MHz** for standalone ATmega chip progects =)
- **Internal 1 MHz**
- **Internal 128 kHz** - use *without bootloader* for this clock
- Note:
	- Time function (delay/millis) are corrected for chosen clock
	- After flash 128 kHz you need to slow down your peogrammer to flash again!
---
**Save EEPROM** - save EEPROM after flashing with programmer **(flash bootloader needed)**:
- **enable**
- **disable**
---
**Clock Out** - system clock out on pin **D8** **(flash bootloader needed)**:
- **disable**
- **enable**
---
**System timer** - timer0 setting:
- **enable** - default settings delay/millis is working as usual
- **disable** - timer 0 OVF vector is free! But millis/micros is not working. delay/delayMicroseconds are replaced by _delay_ms и _delay_us
---
**Serial** - Serial behavior:
- **default Serial** - when you use Serial object, it behave as usual
- **GyverUART** - all calls of **Serial** will be replaced by fast and light **uart** class
---
**B.O.D.** (Brown-out detector) - reset when voltage drop **(flash bootloader needed)**:
- **disable**
- **1.8V** - reset when VCC is below 1.7-2.0V
- **2.7V (default)** - reset when VCC is below 2.5-2.9V
- **4.3V** - reset when VCC is below 4.1-4.5V
---
**Initialization** - initialization of chip periphery (timers, ADC, etc.)
- **enable** - default initialization
- **disable** - no initialization
---
**Compiler version**
- **default v5.4.0** - stock Arduino IDE compiler (avr-gcc v5.4.0)
- **avr-gcc v8.3.0** - new compiler version: faster compilation and lighter code (more free Flash). But could be unstable in some case