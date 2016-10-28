# arduino

int buzzerPin = 6;  // Buzzer is connected to digital pin 6
int lightPin = 0; // sensor connected to analog pin 0

int lightValue; // a variable to keep track of light
int iniLight;    // a var to hold the initial light reading

int melody[] = {0, 0, 0, 0};   // an array to hold
int melodyCount = 0;
   
void setup() {   
     pinMode(buzzerPin, OUTPUT);  // sets the buzzerPin to be an output
     Serial.begin(9600);            // open the serial port:
     
     pinMode(lightPin, INPUT);  // sets the sensorPin to be an input
     analogWrite(lightPin, LOW);  // sets the default (unpressed) state of switchPin to HIGH

     iniLight = analogRead(lightPin);
}  

// runs on a loop  
void loop() {
  
    // read the value from the sensor
    lightValue = analogRead(lightPin);  
    Serial.println(lightValue);
     
    // if someone types a character, add it to melody[]
    if (Serial.available() > 0 ) { 
         int note = Serial.read();
         // if a letter a thru g is pressed, add it to the list of notes
         if (note >= 'a' && note <= 'g') {
             // Serial.print("char is a note: ");  
             // Serial.println(note); 
             melody[melodyCount] = note;
             melodyCount++;
          }
          // if an 'x' is pressed, reset the tune and start over
          else if (note == 'x') {
              clearMelody();
              melodyCount = 0;
              iniLight = analogRead(lightPin);
          }
     } 
     
     //determine the speed of the beat based on light sensor input by user
     int lightSpeed = 400*lightValue/iniLight;
     lightSpeed += 100; // add base speed to ensure beeps aren't running too fast
     playMelody(lightSpeed); 
  
     // Serial.print("LightSpeed: ");
     // Serial.println(lightSpeed);   
   
}   

// for each letter input, play a certain note at speed s
void playMelody(int s) {   
  
    for (int i=0; i<sizeof(melody); i++) {
         switch (melody[i]) {
            case 'a': 
              beep(buzzerPin,3520,s);
              break;
            case 'b':
              beep(buzzerPin,3951,s);
              break;
            case 'c':
              beep(buzzerPin,2093,s);
              break;
            case 'd':
              beep(buzzerPin,2349,s);
              break;
            case 'e':
              beep(buzzerPin,2637,s);
              break;
            case 'f':
              beep(buzzerPin,2793,s);
              break;
            case 'g':
              beep(buzzerPin,3136,s);
              break;            
            default:
              Serial.println("press a key, a thru g, to add a note to your beat. press x to start over.");

          }
      } 
}

// clear the Melody array of all notes, start over
void clearMelody() {
    for(int i=0; i<sizeof(melody); i++) {
        melody[i] = (char)0;
        Serial.print(melody[i]);
    }
}

// the sound producing function taken from tutorial at http://web.media.mit.edu/~leah/LilyPad/07_sound_code.html
void beep (unsigned char speakerPin, int frequencyInHertz, long timeInMilliseconds) {        
    int x;   
    long delayAmount = (long)(1000000/frequencyInHertz);
    long loopTime = (long)((timeInMilliseconds*1000)/(delayAmount*2));
    for (x=0;x<loopTime;x++)   
    {  
        digitalWrite(speakerPin,HIGH);
        delayMicroseconds(delayAmount);
        digitalWrite(speakerPin,LOW);
        delayMicroseconds(delayAmount);
    }  
}
