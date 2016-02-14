/*---------------- HARDWARE -----------------
 * Arduino Nano with IR Receiver TSOP 1738 connected to PIN2
 * TX connected to RX of another Arduino Nano
 * RX connected to TX of the other Arduino Nano
 * The IR remote control is the one you can buy with RGB controller
 * It has 44 buttons
 * You have first to decode your buttons using Serial.println(results.value);
 * then using switch case, affect a command for every button
 * dont use number, you can use Capital letters too
 */


#include <IRremote.h>

int RECV_PIN = 2;
IRrecv irrecv(RECV_PIN);
decode_results results;
char command;
boolean understood = false;

void setup()
{
  Serial.begin(9600);
  irrecv.enableIRIn(); // Start the receiver
}

void loop() {

  //check for data
  if (irrecv.decode(&results)) {
    understood = true;
    Serial.println(results.value);
    /*depending on the button pressed send the appropriate commande
    this par is up to you to choose the 'command' to send */
    switch (results.value) {
      case 16726725:  //brightness++
        command = '+';
        break;
      case 16759365:  //brightness--
        command = '-';
        break;
      case 16745085:  //TEMP
        command = 'T';
        break;
      case 16712445: //ON/OFF
        command = 'O';
        break;
      //COLORS
      case 16718565:
        command = 'R';
        break;
      case 16751205:
        command = 'G';
        break;
      case 16753245:
        command = 'B';
        break;
      case 16720605:
        command = 'W';
        break;

      case 16722645:
        command = 'I';
        break;
      case 16755285:
        command = 'J';
        break;
      case 16749165:
        command = 'K';
        break;
      case 16716525:
        command = 'L';
        break;
      case 16714485:
        command = 'M';
        break;
      case 16747125:
        command = 'N';
        break;
      case 16757325:
        command = 'P';
        break;
      case 16724685:
        command = 'Q';
        break;
      case 16726215:
        command = 'S';
        break;
      case 16758855:
        command = 'U';
        break;
      case 16775175:
        command = 'W';
        break;

      case 16718055:
        command = 'X';
        break;

      case 16750695:
        command = 'Y';
        break;


      case 16734375:
        command = 'Z';
        break;

      case 16767015:
        command = '*';
        break;


      case 16722135:
        command = 'h';
        break;


      case 16754775:
        command = 'm';
        break;


      case 16738455:
        command = 'n';
        break;

      case 16771095:
        command = 'a';
        break;

      case 16713975:
        command = 'r';
        break;

      case 16746615:
        command = 'g';
        break;


      case 16730295:
        command = 'b';
        break;

      case 16762935:
        command = 'h';
        break;

      case 16724175:
        command = 'i';
        break;

      case 16756815:
        command = 'j';
        break;

      case 16740495:
        command = 'k';
        break;

      case 16773135:
        command = 'A';
        break;

      case 16716015:
        command = 'x';
        break;

      case 16748655:
        command = 'y';
        break;

      case 16732335:
        command = 'z';
        break;

      case 16764975:
        command = 'H';
        break;


      case 16720095:
        command = 'F';
        break;

      case 16752735:
        command = 'C';
        break;

      case 16736415:
        command = 'D';
        break;

      case 16769055:
        command = 'E';
        break;

      default:
        understood = false;
    }
    if (understood) Serial.println(command);
    irrecv.resume(); // Receive the next value
  }
}
