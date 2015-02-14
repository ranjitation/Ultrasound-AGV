# UltraAGV
Automated Guided Vehicle with collision prevention system

Program for the Automated Guided Vehicle
Compiler: mikroElektronika C compiler Version 8.2.0.0
Microcontroller: Microchip PIC16F877A

//*********AUTOMATED GUIDED VEHICLE  PROGRAM************************
//This program directs the vehicle in the desired path guides it to the active ultrasonic transmitter's destination.
//For the infrared sensors, it is assumed that they cannot detect black coluored obstacles.
//*************************************************************************

unsigned ch1,ch2,ch3,ch4;
int flag1=0;

//The function that must be followed depending on the receiver values during direction finding

void fn(unsigned,unsigned,unsigned,unsigned)
{
        if (ch1>ch2 && ch1>ch3 && ch1>ch4)         // Output command when 'front' sensor has
maximum input
        {
         PORTB.F0=1;
         PORTB.F1=1;
         PORTB.F2=0;
         PORTB.F3=0;
        }
        if (ch2>ch1 && ch2>ch3 && ch2>ch4)         //Output command when 'right' sensor has 
maximum input
        {
         PORTB.F0=1;
         PORTB.F1=1;
         PORTB.F2=1;
         PORTB.F3=0;
        }
        if (ch3>ch1 && ch3>ch2 && ch3>ch4)         //Output command when 'left' sensor has 
maximum input
        {
         PORTB.F0=0;
         PORTB.F1=1;
         PORTB.F2=0;
         PORTB.F3=0;
        }
        if (ch4>ch1 && ch4>ch2 && ch4>ch3)         //Output command when 'rear' sensor has 
maximum input
        {
         PORTB.F0=0;
         PORTB.F1=1;
         PORTB.F2=0;
         PORTB.F3=1;
        }

        if (ch4<=0x8E && ch3<=0x8E && ch2<=0x8E && ch1<=0x8E)       //Output command 
when vehicle doesn't
receive any input
        {
         PORTB.F0=0;
         PORTB.F1=1;
         PORTB.F2=1;
         PORTB.F3=0;
        }
}

//The function that is followed when the vehicle encounters a collision

void col(int)
{
        if (flag1==1 && PORTC.F0==1)
        {
         PORTB.F0=0;
         PORTB.F1=1;
         PORTB.F2=0;
         PORTB.F3=1;
        }
        if (flag1==1 && PORTC.F0==0)
        {
           Delay_ms(3000);

               PORTB.F0=1;
               PORTB.F1=1;
               PORTB.F2=0;
               PORTB.F3=0;

           Delay_ms(6000);
           flag1=0;
         }

}
void main()
{
    INTCON = 0;                           	//Disables all interrupts

    TRISA=0xFF;
    TRISB=0x00;
    TRISC=0xFF;
    PORTB=0X00;                           //PORTB is the output port for the motors
    PORTA=0X00;                           //PORTA is the input port for the receivers
    ADCON1=0x00;
    ADCON0=0xC5;
    
    while(1)
    {
      ch1=Adc_Read(0);
      Delay_us(17);
      ch2=Adc_Read(1);
      Delay_us(17);
      ch3=Adc_Read(2);
      Delay_us(17);
      ch4=Adc_Read(3);
      Delay_us(17);
      
      if (flag1==0)
      {
       fn(ch1,ch2,ch3,ch4);             //Direction finding is done only when there is no obstruction 
       ahead
      }

      if (PORTC.F0==1)
      {
       flag1=1;                         //Higher priority is given to the collision prevention function
      }
      
      col(flag1);

    }
}//~!
