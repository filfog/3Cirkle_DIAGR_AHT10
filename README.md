/*
  drawDiagram - Draws a circular diagram with customizable parameters.

  Parameters:
    value:      Value to represent in the diagram (0-100).
    x:          X-coordinate of the diagram's center.
    y:          Y-coordinate of the diagram's center.
    r:          Radius of the diagram.
    lineWidth:  Determines the thickness of the diagram's lines.
    lineColor:  Color of the lines in the diagram.
    bgColor:    Background color of the diagram.

  The function draws a circular diagram using lines to represent the value.
  The thicker the lines (lineWidth), the more saturated the diagram fill appears.
  The diagram is drawn from an initial angle (angleStart) to the value-mapped angle (angleEnd).
  The diagram is drawn step by step with an increment of angleStep.

  After drawing the lines, the function displays the value as text near the center of the diagram.

  Note: Make sure to have the appropriate display library included and configured.
*/

#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Thinary_AHT10.h>
//#include <Fonts/FreeSerifItalic9pt7b.h>
//#include <Fonts/FreeSans9pt7b.h >
#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 64 // OLED display height, in pixels
// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET -1 // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);
AHT10Class AHT10;
float temp = 0;
float hum = 0;
float dP = 0;
int value = 0;
void circleDiagram(); 
void setup() {
Serial.begin(9600);  
Wire.begin();
  // Initialize the OLED display using Adafruit SSD1306 library
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);}
 display.clearDisplay();
  display.display();
}
void loop() {
  // Initialize AHT10
    if(AHT10.begin(eAHT10Address_Low))
    Serial.println("Init AHT10 Sucess.");
  else
    Serial.println("Init AHT10 Failure.");
  // put your main code here, to run repeatedly:
  Serial.println("//Thinary Eletronic AHT10 Module//");

hum = AHT10.GetHumidity();
temp = AHT10.GetTemperature();
dP =  AHT10.GetDewPoint();

circleDiagram();
display.display();

}

void drawDiagram(float value, int x, int y, int r, int lineWidth, uint16_t lineColor, uint16_t bgColor) {
 //lineWidth -изменяя толщину линии изменяется насыщенность заливки круговой диаграммы.
  float angleStart = 0;
 float angleEnd = map(value, 0, 100, 0, 360);
//float angleEnd = map(value, 0, 100, 360, 0);  
  float angleStep = 5;

  for (float angle = angleStart; angle <= angleEnd; angle += angleStep) {
    int x1 = x + (r * cos(angle * PI / 180));
    int y1 = y + (r * sin(angle * PI / 180));
    int x2 = x + ((r - lineWidth) * cos(angle * PI / 180));
    int y2 = y + ((r - lineWidth) * sin(angle * PI / 180));
    
//drawLine(int16_t x0, int16_t y0, int16_t x1, int16_t y1, uint16_t color);
 display.drawLine(x1, y1, x2, y2,lineColor);}

 // Display value as text
  display.setTextSize(1);
  display.setTextColor(lineColor, bgColor);
  display.setCursor(x - 10, y + 5);
  display.print(value, 1);
  delay(100);  
}    
void circleDiagram() {
 display.clearDisplay();
/// Display temperature diagram
drawDiagram(temp, 20, 24, 23, 5, SSD1306_WHITE, SSD1306_BLACK);

display.drawCircle(20, 24, 23, SSD1306_WHITE);
display.setCursor(10, 55);
display.setTextColor(WHITE);
display.setTextSize(1);
display.print("Temp");
 // Display humidity diagram
drawDiagram(hum, 64, 24, 23, 5, SSD1306_WHITE, SSD1306_BLACK);
display.drawCircle(64, 24, 23, SSD1306_WHITE);
display.setCursor(54, 55);
display.setTextColor(SSD1306_WHITE);
display.setTextSize(1);
display.print("Hum");
// Display pressure diagram
  //float data = calculatePressure(temp, hum);
drawDiagram(dP, 108, 24, 23, 5, SSD1306_WHITE, SSD1306_BLACK);  // 108//12
//display.fillCircleHelper(108,22,- 90,  data / 500.0 * 360.0 - 90, 2, WHITE);// 24, 
display.drawCircle(108, 24, 23, SSD1306_WHITE);
display.setCursor(98, 55);
display.setTextColor(SSD1306_WHITE);
display.setTextSize(1);
display.print("devP");

//display.display();
delay(100);
  
}





  
