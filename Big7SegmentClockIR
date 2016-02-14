/*------------------------
   Leds used here are WS2812B, 3 leds by segment
   total = (3*7)* 4numbers  + 2 dots = 86 leds
   Led controlled with FastLeds Lib on Pin 5
   Brightness sensor on A0
   RTC Modul on A4 A5
   I'm using 5V 2A Power supply
*/


#include <DS3232RTC.h>
#include <Time.h>
#include <Wire.h>
#include <FastLED.h>
//#include <IRremote.h>



#define NUM_LEDS 86  //3*7*4 +2  Number of LED controles (remember I have 3 leds / controler
#define LED_TYPE    WS2812
//#define COLOR_ORDER BRG // Define color order for your strip
#define BRIGHTNESS_DEFAULT  180
#define LED_PIN 5 // Data pin for led comunication



char    incoming_command = 'H';
int brightness = 0;
int  auto_brightness = 1;
//brightness
int photocellPin = 0; // the cell and 10K pulldown are connected to a0
int photocellReading; // the analog reading from the analog resis
int led_on = 1;
//LEDS COLORS


volatile boolean animate = true;
volatile long animation_change_timeout;
unsigned long previousMillis = 0;        // will store last time LED was updated


const long interval = 5000;           // interval for printing Temp and date



CRGB leds[NUM_LEDS]; // Define LEDs strip
// 10 digit :0...10, each digit is composed of 7 segments of 3 leds
byte digits[10][21] = {
  {
    0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1
  }
  , // Digit 0

  {
    0, 0, 0, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1
  }
  , // Digit 1

  {
    1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 0
  }
  , // Digit 2

  {
    1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1
  }
  , // Digit 3

  {
    1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, 1
  }
  , // Digit 4

  {
    1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1
  }
  , // Digit 5

  {
    1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1
  }
  , // Digit 6

  {
    0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1
  }
  , // Digit 7

  {
    1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1
  }
  , // Digit 8

  {
    1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 1
  }
}; // Digit 9 | 2D Array for numbers on 7 segment

// Digit 1 first number | 2D Array for numbers on 7 segment

bool Dot = true; //Dot state

bool DST = false; //DST state
int last_digit = 0;
//long ledColor = CRGB::DarkOrchid; // Color used (in hex)
long ledColor = CRGB::MediumVioletRed;
//Random colors i picked up
long ColorTable[16] = {
  CRGB::Amethyst,
  CRGB::Aqua,
  CRGB::Blue,
  CRGB::Chartreuse,
  CRGB::DarkGreen,
  CRGB::DarkMagenta,
  CRGB::DarkOrange,
  CRGB::DeepPink,
  CRGB::Fuchsia,
  CRGB::Gold,
  CRGB::GreenYellow,
  CRGB::LightCoral,
  CRGB::Tomato,
  CRGB::Salmon,
  CRGB::Red,
  CRGB::Orchid
};


void setup() {

  Serial.begin(9600);
  Wire.begin();
  FastLED.addLeds<WS2812B, LED_PIN, GRB>(leds, NUM_LEDS);
  // FastLED.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS).setCorrection( TypicalLEDStrip );
  FastLED.setBrightness(  BRIGHTNESS_DEFAULT );

}

// Check Light sensor and set brightness accordingly

void BrightnessCheck() {

  photocellReading = analogRead(photocellPin);

  const byte brightnessLow = 30; // Low brightness value

  const byte brightnessHigh = 100; // High brightness value
  //you can choose between auto or manual brightness
  if   (auto_brightness ) {
    if (photocellReading > 100) {

      FastLED.setBrightness( brightnessHigh );

    }

    else if (photocellReading < 100) {


      FastLED.setBrightness( brightnessLow);

    }
  }

};

// Get time in a single number
int GetTime() {
  tmElements_t Now;
  RTC.read(Now);
  //time_t Now = RTC.Now();// Getting the current Time and storing it into a DateTime object
  int hour = Now.Hour;
  int minutes = Now.Minute;
  int second = Now.Second;
  //use this to blink your dots every second
  if (second % 2 == 0) {
    Dot = false;
  }
  else {
    Dot = true;
  };
  //it is easy to manipulate 2150 rather than hours = 10 minutes = 50
  return (hour * 100 + minutes);
};

//function to print day and month
int DayMonth() {
  tmElements_t Now;
  RTC.read(Now);
  int day = Now.Day;
  int month = Now.Month;
  return (day * 100 + month);
}

// not used yet
void DSTcheck() {

  int buttonDST = digitalRead(2);

  Serial.print("DST is: ");
  Serial.println(DST);

  if (buttonDST == LOW) {

    if (DST) {

      DST = false;

      Serial.print("Switching DST to: ");
      Serial.println(DST);

    }

    else if (!DST) {

      DST = true;

      Serial.print("Switching DST to: ");
      Serial.println(DST);

    };

    delay(500);

  };

}

// Convert time to array needet for display
void TimeToArray() {

  int Now = GetTime(); // Get time

  int cursor = 86; //

  //Serial.print("Time is: ");
  //Serial.println(Now);

  if (Dot) {
    leds[42] = ledColor;
    leds[43] = ledColor;
  }

  else {

    leds[42] = 0x000000;
    leds[43] = 0x000000;

  };

  for (int i = 1; i <= 4; i++) {

    int digit = Now % 10; // get last digit in time

    if (i == 1) {

      cursor = 65;

      //Serial.print("Digit 4 is : ");
      //Serial.print(digit);
      //Serial.print(", the array is : ");

      for (int k = 0; k <= 20; k++) {

        //Serial.print(digits[digit][k]);

        if (digits[digit][k] == 1) {
          leds[cursor] = ledColor;
        }

        else if (digits[digit][k] == 0) {
          leds[cursor] = 0x000000;
        };

        cursor ++;

      }; // fin for

      //   Serial.println();
      if (digit != last_digit)
      {
        fadefonction();
        ledColor =  ColorTable[random(16)];
      }
      last_digit = digit;

    }// fin if

    else if (i == 2) {

      cursor = 44;

      //Serial.print("Digit 3 is : ");
      //Serial.print(digit);
      //Serial.print(", the array is : ");

      for (int k = 0; k <= 20; k++) {

        //Serial.print(digits[digit][k]);

        if (digits[digit][k] == 1) {
          leds[cursor] = ledColor;
        }

        else if (digits[digit][k] == 0) {
          leds[cursor] = 0x000000;
        };

        cursor ++;

      };

      // Serial.println();

    }

    else if (i == 3) {

      cursor = 21;

      //Serial.print("Digit 2 is : ");
      //Serial.print(digit);
      //Serial.print(", the array is : ");

      for (int k = 0; k <= 20; k++) {

        //Serial.print(digits[digit][k]);

        if (digits[digit][k] == 1) {
          leds[cursor] = ledColor;
        }

        else if (digits[digit][k] == 0) {
          leds[cursor] = 0x000000;
        };

        cursor ++;

      };

      //Serial.println();

    }

    else if (i == 4) {

      cursor = 0;

      //Serial.print("Digit 1 is : ");
      //Serial.print(digit);
      //Serial.print(", the array is : ");

      for (int k = 0; k <= 20; k++) {
        //Serial.print(digits[digit][k]);
        if (digits[digit][k] == 1) {
          leds[cursor] = ledColor;
        }
        else if (digits[digit][k] == 0) {
          leds[cursor] = 0x000000;
        };
        cursor ++;
      };
      //      Serial.println();

    };

    Now /= 10;

  };

};

// Convert date to array needet for display

void DateToArray() {
  FastLED.clear();
  unsigned long currentMillis = millis();
  while (millis() - currentMillis < interval) {
    FastLED.show();
    int Now = DayMonth(); // Get time

    int cursor = 86; //116

    //Serial.print("Time is: ");
    //Serial.println(Now);


    for (int i = 1; i <= 4; i++) {

      int digit = Now % 10; // get last digit in time

      if (i == 1) {

        cursor = 65; //82

        //Serial.print("Digit 4 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {

          //Serial.print(digits[digit][k]);

          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }

          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };

          cursor ++;

        }; // fin for

        //   Serial.println();


      }// fin if

      else if (i == 2) {

        cursor = 44;

        //Serial.print("Digit 3 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {

          //Serial.print(digits[digit][k]);

          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }

          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };

          cursor ++;

        };

        // Serial.println();

      }

      else if (i == 3) {

        cursor = 21;

        //Serial.print("Digit 2 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {

          //Serial.print(digits[digit][k]);

          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }

          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };

          cursor ++;

        };

        //Serial.println();

      }

      else if (i == 4) {

        cursor = 0;

        //Serial.print("Digit 1 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {
          //Serial.print(digits[digit][k]);
          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }
          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };
          cursor ++;
        };
        //      Serial.println();

      };

      Now /= 10;

    };
  }
};

//not used yet
void TimeAdjust() {

  int buttonH = digitalRead(5);

  int buttonM = digitalRead(4);

  if (buttonH == LOW || buttonM == LOW) {

    delay(500);

    tmElements_t Now;

    RTC.read(Now);

    int hour = Now.Hour;

    int minutes = Now.Minute;

    if (buttonH == LOW) {

      if (Now.Hour == 24) {
        Now.Hour = 1;
      }

      else {
        Now.Hour += 1;
      };

    }
    else {

      if (Now.Minute == 59) {
        Now.Minute = 0;
      }

      else {
        Now.Minute += 1;
      };

    };

    RTC.write(Now);

  }

}

/* coool effect function*/
void fadeall() {
  for (int m = 0; m < NUM_LEDS; m++) {
    leds[m].nscale8(250);
  }
}

void fadefonction () {
  static uint8_t hue = 0;
  // First slide the led in one direction
  for (int i = 0; i < NUM_LEDS; i++) {
    // Set the i'th led to red
    leds[i] = CHSV(hue++, 255, 255);
    // Show the leds
    FastLED.show();
    // now that we've shown the leds, reset the i'th led to black
    // leds[i] = CRGB::Black;
    fadeall();
    // Wait a little bit before we loop around and do it again
    delay(10);
  }


  // Now go in the other direction.
  for (int i = (NUM_LEDS) - 1; i >= 0; i--) {
    // Set the i'th led to red
    leds[i] = CHSV(hue++, 255, 255);
    // Show the leds
    FastLED.show();
    // now that we've shown the leds, reset the i'th led to black
    // leds[i] = CRGB::Black;
    fadeall();
    // Wait a little bit before we loop around and do it again
    delay(10);
  }

}

/******IR check****
   Check the command on the serial port sent by
   the other arduino depending on the button pressed
*/
void IR_Check() {

  if (Serial.available() > 0) {
    // read the incoming byte:
    incoming_command = Serial.read();
    unsigned long startTime = 0;
    switch (incoming_command) {
      
      //colors
      case 'R' :

        //Serial.println("RED");
        ledColor = CRGB::Red;
        break;

      case 'B' :

        ledColor = CRGB::Blue;
        break;

      case 'G' :

       
        ledColor = CRGB::Green;
        break;

      case 'W' :

       
        ledColor = CRGB::White;
        break;

      case 'I' :

        
        ledColor = CRGB::OrangeRed;
        break;

      case 'J' :

       
        ledColor = CRGB::GreenYellow;
        break;

      case 'K' :

        
        ledColor = CRGB::MediumSlateBlue;
        break;

      case 'L' :

       
        ledColor = CRGB::Pink;
        break;

      case 'M' :

       
        ledColor = CRGB::DarkOrange;
        break;

      case 'N' :

        
        ledColor = CRGB::Aqua;
        break;

      case 'P' :

        
        ledColor = CRGB::DarkSlateBlue;
        break;

      case 'Q' :

      
        ledColor = CRGB::LightPink;
        break;

      case 'S' :

       
        ledColor = CRGB::LightSalmon;
        break;

      case 'U' :

      
        ledColor = CRGB::LightSeaGreen;
        break;

      case 'V' :

       
        ledColor = CRGB::Purple;
        break;



      case 'X' :

       
        ledColor = CRGB::Yellow;
        break;

      case 'Y' :

       
        ledColor = CRGB::Teal;
        break;

      case 'Z' :

       
        ledColor = CRGB::PaleVioletRed;
        break;

      case '*' :

        
        ledColor = CRGB::PaleTurquoise;
        break;

      case 'F' :

        //pick a random color but not working yet
        ledColor = CRGB( random8(), 100, 50);
        break;

      case '+' : //brighter
        brightness += 10;
        if (brightness > 255) brightness = 255;
        FastLED.setBrightness( brightness );
        auto_brightness = 0;
        break;
      case '-' :

        brightness -= 10;
        if (brightness < 10) brightness = 10;
        FastLED.setBrightness( brightness );
        auto_brightness = 0;
        break;

      case 'O' : //auto brightness

        if (led_on)
        {
          brightness = 0;
          led_on = 0;
        }
        else
        {
          brightness = BRIGHTNESS_DEFAULT ;
          led_on = 1;

        }

        FastLED.setBrightness( brightness );
        break;


      case 'T' : //Temp

        Temp();


        break;


      case 'a' : //Date


        DateToArray();


        break;

      case 'A' :

        auto_brightness = 1;
        break;

      default:
        break;
        //Serial.println("error");
    }//switch

    // say what you got:
    //Serial.print("I received: ");
    //Serial.println(incoming_command);

  }
  /*  if (irrecv.decode(&results)) {
      Serial.println(results.value, HEX);
      switch(results.value) {
      case 0xFF1AE5 :

        Serial.println("RED");
        ledColor = CRGB::Red;
        break;
      default:
        Serial.println("error");
      }
      irrecv.resume(); // Receive the next value
    }
  */

};

//print the internal temperature
void Temp()
{
  FastLED.clear();
  int cursor = 21; //65
  float t = RTC.temperature();
  int celsius = t / 4.0;
  Serial.print(celsius);


  unsigned long currentMillis = millis();
  while (millis() - currentMillis < interval) {
    FastLED.show();
    int cursor = 21; //65
    float t = RTC.temperature();
    int celsius = t / 4.0;
    for (int i = 1; i <= 2; i++) {

      int digit = celsius % 10; // get last digit in time
      Serial.print("digit");
      Serial.println(digit);
      if (i == 1) {



        //Serial.print("Digit 4 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {

          //Serial.print(digits[digit][k]);

          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }

          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };

          cursor ++;

        }; // fin for

        FastLED.show();

      }// fin if

      else if (i == 2) {

        cursor = 0;

        //Serial.print("Digit 3 is : ");
        //Serial.print(digit);
        //Serial.print(", the array is : ");

        for (int k = 0; k <= 20; k++) {

          //Serial.print(digits[digit][k]);

          if (digits[digit][k] == 1) {
            leds[cursor] = ledColor;
          }

          else if (digits[digit][k] == 0) {
            leds[cursor] = 0x000000;
          };

          cursor ++;

        };

        // Serial.println();
      }

      celsius /= 10;
      FastLED.show();
    }
    for (int m = 44; m < 56; m++) {
      leds[m] = ledColor;
    }
    for (int m = 72; m < 83; m++) {
      leds[m] = ledColor;
    }
    FastLED.show();
    //check_for_input();
  }//fin while animate

};


//not used yet
void check_for_input() {
  if (animation_change_timeout > 100) {
    if (Serial.available() > 0) {
      // read the incoming byte:
      incoming_command = Serial.read();

      // say what you got:
      Serial.print("I received: ");
      Serial.println(incoming_command);
      animate = false;
    }
  }
}


void loop() // Main loop

{

  BrightnessCheck(); // Check brightness
  TimeToArray(); // Get leds array with required configuration
  IR_Check();
  FastLED.show(); // Display leds array
 
}













