# Mini-Project
Two-Wheeled vehicle with LCD interfacing Using ARM
#include <lpc214x.h>
#define RS (1<<10); // Register select pin at 10
#define RW (1<<11); // Read/write pin at 11
#define EN (1<<12); // Enable pin at 12
unsigned int i;
#define delay for(i=0; i<10000; i++); // Delay of few microseconds
void lcd_init(); // Initializes LCD
void data(unsigned char); // Displays data to LCD
void commd(unsigned char); // Sends data to LCD
void strings(unsigned char*); // Sends string in LCD
void robo_direction(); // Identifies the direction of motor
char *robo_state;
int main()
{
IO0DIR = 0x00001C0F;
IO1DIR = 0x00FF0000;
IOPIN0 = 0;
IOPIN1 = 0;
VPBDIV = 0x01;
lcd_init(); // Initializes LCD
commd(0x00);
strings("Robot direction:"); // String
delay;
while(1){
commd(0xC0);
robo_direction();
delay;
delay;
}
return 0;
}
void robo_direction(){
int sel = 0;
if ((IO1PIN & (1<<25)) == 0){ // Switch to move forward
IO0SET = (1<<1) | (1<<3);
IO0CLR = (1<<0) | (1<<2);
robo_state = "Forward ";
sel += 1;
}
else{}
if ((IO1PIN & (1<<27)) == 0){ // Switch to move backward
IO0SET = (1<<0) | (1<<2);
IO0CLR = (1<<1) | (1<<3);
robo_state = "Backward ";
sel += 1;
}
else{}
if ((IO1PIN & (1<<26)) == 0){ // Switch to turn left
IO0SET = (1<<0) | (1<<3);
IO0CLR = (1<<1) | (1<<2);
robo_state = "Left ";
sel += 1;
}
else{}
if ((IO1PIN & (1<<24)) == 0){ // Switch to turn right
IO0SET = (1<<1) | (1<<2);
IO0CLR = (1<<0) | (1<<3);
robo_state = "Right ";
sel += 1;
}
else{}
if (sel > 1){
IO0CLR = (1<<0) | (1<<1) | (1<<2) | (1<<3);
robo_state = "I/O error";
}
else if (sel < 1){ // Stops if all switches are closed
IO0CLR = (1<<0) | (1<<1) | (1<<2) | (1<<3);
robo_state = "Idle ";
}
strings(robo_state);
}
void lcd_init(){
commd(0x38); // LCD in 8-bit, 2-line, 5x7 dots
commd(0x0e); // Display ON cursor ON
commd(0x06); // Entry mode
commd(0x01); // Clear display
}
void commd(unsigned char a){
IO1PIN = IO1PIN & 0x00000000;
IO0SET = EN; // Set enable pin
IO0CLR = RS; // Clears register select pin
IO1PIN |= (a<<16); // Loads 8-bit data
IO0CLR = EN; // Clear enable pin
delay;
}
void data(unsigned char d){
IO1PIN = IO1PIN & 0x00000000;
IO0SET = EN; // Set enable pin
IO0SET = RS; // Set register select pin
IO1PIN |= (d<<16); // Loads 8-bit data
IO0CLR = EN; // Clear enable pin
delay;
}
void strings(unsigned char* p){
while(*p != '\0'){ //Loops until end os string
data(*p ++); // Increments string pointer
}
}
