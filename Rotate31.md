# Rotate 31

```c
 /*
 * rotate_31_int.c
 *
 * Created: 14.09.2017 12:03:40
 * Author : mot
 */
 #define F_CPU 16000000
 #include <avr/io.h>
 #include <avr/interrupt.h>
 #include <util/delay.h>

void initINT0(){
	EICRA |= (1<<ISC01);
	EICRA &= ~(1<<ISC00);
	EIMSK |= (1<<INT0);
	EIFR |= (1<<INTF0);
}

ISR(INT0_vect){
	EIMSK &= ~(1<<INT0);	
	PORTB = (PORTB << 1);
	if (PORTB >= 64 ){
		PORTB &= 0x3F;
		PORTB |= 0x01;
	}
	_delay_ms(500);
	EIMSK |= (1<<INT0);
	EIFR |= (1<<INTF0);
}


 int main(void)
 {
	 DDRB = 0xFF;
	 PORTB = 0x01;		 
	 sei();
	 initINT0();	 
	 while (1){
			 /* in ISR
			 PORTA = (PORTA << 1);
			 if (PORTA >= 64 ){		 
				 PORTA &= 0x1F;
				 PORTA |= 0x01;
			 } */
		}
 }
```

![Rotate 31 Aufzeichnung](./Rotate31.jpg)



### Code mit CTC

```c
#define F_CPU 16000000
#include <avr/io.h>
#include <avr/interrupt.h>

void config(){
	sei();
	OCR0A = 0x1F; //30d
	TCCR0A &= ~(1 << WGM00);
	TCCR0A |= 1 << WGM01;
	TCCR0B &= ~(1 << WGM02);
	TCCR0B &= ~(1 << CS00);
	TCCR0B &= ~(1 << CS01);
	TCCR0B |= 1 << CS02;
	TIMSK0 |= 1 << TOIE0;
	TIMSK0 |= 1 << OCIE0A;
	TIFR0 |= 1 << OCF0A;
}

ISR(TIMER0_COMPA_vect){
	PORTC++;
	if (PORTC >= 0x80){
		PORTC = 0x00;
	}
	
}

int main(void)
{	
	config();
	DDRC = 0xFF;
	PORTC = 0x00;
	while(1){}
}
```
