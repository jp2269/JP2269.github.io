---
title: "Autonomous Robot Project"
layout: posts
---

# Code to move the rover

```
#define MotorLA 9 // B1 on H-Bridge
#define MotorLB 10 // B2
#define MotorRA 6 // B1 on H-Bridge
#define MotorRB 5  // B2

// All the sensor Pins for Trigger and Echoing
#define frontRightTrigPin 2
#define frontRightEchoPin 24
#define frontLeftTrigPin 30
#define frontLeftEchoPin 28
#define rightTrigPin 48 
#define rightEchoPin 52 
#define backRightTrig 53
#define backRightEcho 36
#define foot 600


//Encoder values
#define encoderLeftA 3
#define encoderLeftB 7
#define encoderRightA 8
#define encoderRightB 11

#define LEDFront 41 
#define LEDBright 42
#define LEDRight 47
#define distCount 13

#define LEDLSB 22
#define LED2bit 23
#define LEDMSB 25

void CheckSensors(void);

bool state = false;

uint8_t frontRDistanceGB = 5;
uint8_t frontLDistanceGB = 5;
int DistanceGB = 0;

char function;
char modMF = 10;
char encoderArray[4];
char turnArray[4];
char CSRturnLoopcnt = 0;
char CornerCount = 0;
char CheckCorner = 0;
char InsideCounterLoop = 0;
bool testcodeB = true;

void setup() {
  // put your setup code here, to run once:
  pinMode(6, OUTPUT);
  Serial.begin(9600);  //Begin serial communcation
  pinMode(5, OUTPUT);
  pinMode(9,OUTPUT);   
  pinMode(10, OUTPUT);
  pinMode(encoderLeftA, INPUT);
  pinMode(encoderLeftB, INPUT);
  pinMode(encoderRightA, INPUT);
  pinMode(encoderRightB, INPUT);

  pinMode(frontRightTrigPin, OUTPUT);
  pinMode(frontRightEchoPin, INPUT); 
  pinMode(rightTrigPin, OUTPUT);
  pinMode(rightEchoPin, INPUT);

  pinMode(frontLeftTrigPin, OUTPUT);
  pinMode(frontLeftEchoPin, INPUT);
  pinMode(backRightTrig, OUTPUT);
  pinMode(backRightEcho, INPUT);

  pinMode(LEDFront, OUTPUT);
  
  pinMode(LEDLSB, OUTPUT);
  pinMode(LED2bit, OUTPUT);
  pinMode(LEDMSB, OUTPUT);

  pinMode(distCount, INPUT);

  encoderArray[0] = digitalRead(encoderLeftA);
  encoderArray[1] = digitalRead(encoderLeftB);
  encoderArray[2] = digitalRead(encoderRightA);
  encoderArray[3] = digitalRead(encoderRightB);

  turnArray[0] = digitalRead(encoderLeftA);
  turnArray[1] = digitalRead(encoderLeftB);
  turnArray[2] = digitalRead(encoderRightA);
  turnArray[3] = digitalRead(encoderRightB);
  
  delay(3000);
  }
  
void loop() {
  if(Serial.available()){
    delay(100);
    function = Serial.read();
    if(function == '1'){
      //turnRight();
      state = true;
    }
    else if( function == '2'){
      state = false;
      Stop();
    }
  }
 if(state == true){
    /*if (CornerCount >= 8){
    innerCheckSensors(55, 55, 80, 80);
    frontRDistanceGB = 40;
    frontLDistanceGB = 40;   
    modMF = 2;
  }
  else{
    CheckSensors(10, 10, 25, 25); 
  }
  */
  bool onetimetest = false;
  if (testcodeB == true){  
    turnLeft();
    moveForward(400); 
    Stop(); 
    testcodeB = false; 
    turnLeft();
    onetimetest = true;
  }
  if (onetimetest == true){
    swirl();
  }
  
  
  if (CheckCorner % 2 == 0){
    if(DistanceGB/foot == 0 || DistanceGB/foot == 8){
      digitalWrite(LEDMSB, LOW);    
      digitalWrite(LED2bit, LOW); 
      digitalWrite(LEDLSB, LOW); 
    }
    else if(DistanceGB/foot == 1 || DistanceGB/foot == 9){
      digitalWrite(LEDMSB, LOW);    
      digitalWrite(LED2bit, LOW); 
      digitalWrite(LEDLSB, HIGH); 
    }
    else if(DistanceGB/foot == 2 || DistanceGB/foot == 10){
      digitalWrite(LEDMSB, LOW);    
      digitalWrite(LED2bit, HIGH); 
      digitalWrite(LEDLSB, LOW); 
    }
    else if(DistanceGB/foot == 3 || DistanceGB/foot == 11){
      digitalWrite(LEDMSB, LOW);    
      digitalWrite(LED2bit, HIGH); 
      digitalWrite(LEDLSB, HIGH); 
    }
    else if(DistanceGB/foot == 4 || DistanceGB/foot == 12){
      digitalWrite(LEDMSB, HIGH);    
      digitalWrite(LED2bit, LOW); 
      digitalWrite(LEDLSB, LOW); 
    }
    else if(DistanceGB/foot == 5 || DistanceGB/foot == 13){
      digitalWrite(LEDMSB, HIGH);    
      digitalWrite(LED2bit, LOW); 
      digitalWrite(LEDLSB, HIGH); 
    }
    else if(DistanceGB/foot == 6 || DistanceGB/foot == 14){
      digitalWrite(LEDMSB, HIGH);    
      digitalWrite(LED2bit, HIGH); 
      digitalWrite(LEDLSB, LOW); 
    }
    else if(DistanceGB/foot == 7 || DistanceGB/foot == 15){
      digitalWrite(LEDMSB, HIGH);    
      digitalWrite(LED2bit, HIGH); 
      digitalWrite(LEDLSB, HIGH); 
    }
  }
 }
}

void CheckSensors(int CSfrontRDist, int CSfrontLDist, int CSrightDist, int CSbackRightDist){
  int CSrightDistance = 0;
  int CSBrightDistance = 0;
  int RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
  while(RightfrontdistMF == 0){
    Stop();
    RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
  while(LeftfrontdistMF == 0){
    Stop();
    LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  if (RightfrontdistMF <= CSfrontRDist || LeftfrontdistMF <= CSfrontLDist){  //Checks if there is a wall forward and goes into cond if there is a wall
    Stop();
    CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
    while(CSrightDistance == 0){
      Stop();
      CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
      digitalWrite(LEDRight, HIGH);     
    }
    CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
    while(CSBrightDistance == 0){
        Stop();
        CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
        digitalWrite(LEDBright, HIGH);
      }
    if (CSrightDistance <= CSrightDist || CSbackRightDist <= CSBrightDistance){ //Checks if there is a wall forward and if it is next to a wall
      turnLeft();
      CheckCorner++;
      CornerCount++;
      if (CornerCount == 8){
        return;
      }
      int CSBackcounter = 0;
       while(CSBrightDistance <= CSbackRightDist){
         CSBackcounter++;
         moveForward(70);
         if (CSBackcounter % 4 == 0){
           RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
           while(RightfrontdistMF == 0){
             Stop();
             RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
             digitalWrite(LEDFront, HIGH);
           }
           int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
           while(LeftfrontdistMF == 0){
             Stop();
             LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
             digitalWrite(LEDFront, HIGH);
           }
           if (RightfrontdistMF <= 10 || LeftfrontdistMF <= 10){
             return;        
           }
           CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
           while(CSBrightDistance == 0){
             Stop();
             CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
             digitalWrite(LEDBright, HIGH);
           }  
           CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
           while(CSrightDistance == 0){
             Stop();
             CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
             digitalWrite(LEDRight, HIGH);   
           }   
           int CSAutoCorrectcnt = 0;
           AutoCorrect();
         }
         if(CSBackcounter % 2 == 0){
           CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
           while(CSBrightDistance == 0){
             Stop();
             CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
             digitalWrite(LEDBright, HIGH);
           }  
         }
      }  
      Stop();
      moveForward(55);  //was 55
    }
    else{ //There is a wall forward and nothing on its right
      turnRight();
      Stop();
      moveForward(250);
    }
  }
  else{ //There is no wall in front of the rover
    CSrightDistance = getDistance(rightTrigPin, rightEchoPin);
    while(CSrightDistance == 0){
      Stop();
      CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
      digitalWrite(LEDRight, HIGH);   
    }
    CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
    while(CSBrightDistance == 0){
        Stop();
        CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
        digitalWrite(LEDBright, HIGH);
    }
    if (CSrightDistance <= CSrightDist || CSBrightDistance <= CSbackRightDist){ //There is no wall in front and wall/object to the right
       int CSBackcounter = 0;
       while(CSBrightDistance <= CSbackRightDist){
         CSBackcounter++;
         moveForward(140);
         if (CSBackcounter % 3 == 0){
           Stop(); 
           RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
           while(RightfrontdistMF == 0){
             Stop();
             RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
             digitalWrite(LEDFront, HIGH);
           }
           int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
           while(LeftfrontdistMF == 0){
             Stop();
             LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
             digitalWrite(LEDFront, HIGH);
           }
           if (RightfrontdistMF <= 10 || LeftfrontdistMF <= 10){
             return;        
           }
           AutoCorrect();
           CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
           while(CSBrightDistance == 0){
             Stop();
             CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
             digitalWrite(LEDBright, HIGH);
           }  
           CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
           while(CSrightDistance == 0){
             Stop();
             CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
             digitalWrite(LEDRight, HIGH);   
           }   
         }  
         if(CSBackcounter % 2 == 0){
           CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
           while(CSBrightDistance == 0){
             Stop();
             CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
             digitalWrite(LEDBright, HIGH);
           }       
         }
      }  
      Stop();
      moveForward(50); //was 100
    }
    else{ //There is no wall in front and there is no wall to the right -> so, turn left and move forward
      CSRturnLoopcnt++;
      turnRight();
      CheckCorner++;
      moveForward(250);
      Stop();
      if (CSRturnLoopcnt == 3){
        moveForward(10000);
        turnRight();
        CSRturnLoopcnt = 0;
      }
      if (CSRturnLoopcnt == 2){
        moveForward(600);
        CSRturnLoopcnt = 0;
      }
    }
  }
} 

void innerCheckSensors(int CSfrontRDist, int CSfrontLDist, int CSrightDist, int CSbackRightDist){
  int CSrightDistance = 0;
  int CSBrightDistance = 0;
  int RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
  while(RightfrontdistMF == 0){
    Stop();
    RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
  while(LeftfrontdistMF == 0){
    Stop();
    LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  
  if (RightfrontdistMF <= CSfrontRDist || LeftfrontdistMF <= CSfrontLDist){  //Checks if there is a wall forward and goes into cond if there is a wall
    Stop();
    CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
    while(CSrightDistance == 0){
      Stop();
      CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
      digitalWrite(LEDRight, HIGH);     
    }
    CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
    while(CSBrightDistance == 0){
        Stop();
        CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
        digitalWrite(LEDBright, HIGH);
      }
    if (CSrightDistance <= CSrightDist || CSbackRightDist <= CSBrightDistance){ //Checks if there is a wall forward and if it is next to a wall
      turnLeft();
      AutoCorrect();
      Stop();
      CornerCount++;
      moveForward(1200);
    }
    else{ //There is a wall forward and nothing on its right
      turnRight();
      AutoCorrect();
      Stop();
      moveForward(1200);
    }
  }
  else{ //There is no wall in front of the rover
    CSrightDistance = getDistance(rightTrigPin, rightEchoPin);
    while(CSrightDistance == 0){
      Stop();
      CSrightDistance = getDistance(rightTrigPin, rightEchoPin); 
      digitalWrite(LEDRight, HIGH);   
    }
    CSBrightDistance = getDistance(backRightTrig,backRightEcho); 
    while(CSBrightDistance == 0){
        Stop();
        CSBrightDistance = getDistance(backRightTrig,backRightEcho);  
        digitalWrite(LEDBright, HIGH);
      }
    if (CSrightDistance <= 120 || CSBrightDistance <= 120){ //There is no wall in front and wall/object to the right
       moveForward(1000);
    }
    else{ //There is no wall in front and there is no wall to the right -> so, turn left and move forward
      turnRight();
      moveForward(2000);
      turnLeft();
      AutoCorrect();
      CheckCorner++;
      delay(200);
      moveForward(1000);
    }
  }
}


void moveForward(int ForwardDistanceMF)  {
  Stop();
  digitalWrite(MotorRA, LOW);
  analogWrite(MotorRB, 232); 
  analogWrite(MotorLA, 198);
  digitalWrite(MotorLB, LOW);
  int forwardcheckMF = 0;
  int RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
  while(RightfrontdistMF == 0){
    Stop();
    RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
  while(LeftfrontdistMF == 0){
    Stop();
    LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  while ((forwardcheckMF < ForwardDistanceMF) && (RightfrontdistMF > frontRDistanceGB) && (LeftfrontdistMF > frontLDistanceGB)){
    digitalWrite(MotorRA, LOW);
    analogWrite(MotorRB, 232); 
    analogWrite(MotorLA, 198);
    digitalWrite(MotorLB, LOW);
    if ((encoderArray[0] != digitalRead(encoderLeftA)) || (encoderArray[1] != digitalRead(encoderLeftB))){
      forwardcheckMF++;
      DistanceGB++;
      encoderArray[0] = digitalRead(encoderLeftA);
      encoderArray[1] = digitalRead(encoderLeftB); 
    }
    if(forwardcheckMF % modMF == 0) {
      RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
      while(RightfrontdistMF == 0){
        Stop();
        RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
        digitalWrite(LEDFront, HIGH);
      }
      LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
      while(LeftfrontdistMF == 0){
        Stop();
        LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
        digitalWrite(LEDFront, HIGH);
      }
    }
  }
}

int getDistance(int trigger, int echo){
  int duration;
  digitalWrite(trigger, LOW);  // Added this line
  delayMicroseconds(10); // Added this line
  digitalWrite(trigger, HIGH);
  delayMicroseconds(20); // Added this line
  digitalWrite(trigger, LOW);
  duration = pulseIn(echo, HIGH, 60000);
  return (duration/2) / 29.1;
}

void Stop() {
  analogWrite(MotorRB, 255);
  digitalWrite(MotorRA, HIGH);
  digitalWrite(MotorLB,HIGH);
  analogWrite(MotorLA, 255);
  delay(325);
}

void turnRight(){
  Stop();
  int turncountL = 0; 
  int turncountR = 0;
  while(turncountL < 440 || turncountR < 440){
    if ((turnArray[0] != digitalRead(encoderLeftA)) || (turnArray[1] != digitalRead(encoderLeftB))){
      turncountL++;
      turnArray[0] = digitalRead(encoderLeftA);
      turnArray[1] = digitalRead(encoderLeftB); 
    }
    if ((turnArray[2] != digitalRead(encoderRightA)) || (turnArray[3] != digitalRead(encoderRightB))){
      turncountR++;
      DistanceGB++;
      turnArray[2] = digitalRead(encoderRightA);
      turnArray[3] = digitalRead(encoderRightB);
    }
    analogWrite(MotorRB, 100);
    digitalWrite(MotorRA, HIGH);
    digitalWrite(MotorLB, LOW);
    analogWrite(MotorLA, 200);
  }
}

void turnLeft(){
  Stop();
  int turncountL = 0;
  int turncountR = 0;
  while(turncountL < 440 || turncountR < 440) {
    if ((turnArray[0] != digitalRead(encoderLeftA)) || (turnArray[1] != digitalRead(encoderLeftB))){
      turncountL++;
      DistanceGB++;
      turnArray[0] = digitalRead(encoderLeftA);
      turnArray[1] = digitalRead(encoderLeftB); 
    }
    if ((turnArray[2] != digitalRead(encoderRightA)) || (turnArray[3] != digitalRead(encoderRightB))){
      turncountR++;
      turnArray[2] = digitalRead(encoderRightA);
      turnArray[3] = digitalRead(encoderRightB);
    }
    analogWrite(MotorRB, 200);
    digitalWrite(MotorRA, LOW);
    digitalWrite(MotorLB, HIGH);
    analogWrite(MotorLA, 100);
  }
}

void AutoCorrect(){
  int AutoCorrectcnt = 0;
  int ACrightDistance = getDistance(rightTrigPin, rightEchoPin); 
  int ACBrightDistance = getDistance(backRightTrig,backRightEcho); 
  while (ACrightDistance != ACBrightDistance){
    if (ACrightDistance > ACBrightDistance) {
      analogWrite(MotorRB, 255);
      digitalWrite(MotorRA, HIGH);
      digitalWrite(MotorLB, LOW);
      analogWrite(MotorLA, 180);
    }
    else{
      analogWrite(MotorRB, 180);
      digitalWrite(MotorRA, LOW);
      digitalWrite(MotorLB,HIGH);
      analogWrite(MotorLA, 255);               
    }
      AutoCorrectcnt++;
      if (AutoCorrectcnt % 4){
        ACBrightDistance = getDistance(backRightTrig,backRightEcho); 
        while(ACBrightDistance == 0){
          Stop();
          ACBrightDistance = getDistance(backRightTrig,backRightEcho);  
          digitalWrite(LEDBright, HIGH);
        }  
        ACrightDistance = getDistance(rightTrigPin, rightEchoPin); 
        while(ACrightDistance == 0){
          Stop();
          ACrightDistance = getDistance(rightTrigPin, rightEchoPin); 
          digitalWrite(LEDRight, HIGH);     
        }
      }
    }
}

void snake(void){
    moveForward(10000);
    turnLeft();
    Stop();
    moveForward(150);
    Stop();
    turnLeft();
    Stop();
    moveForward(10000);
    Stop();
    turnRight();
    Stop();
    moveForward(150);
    Stop();
    turnRight();
    Stop();
    moveForward(10000);
    Stop();
} 

void swirl(void){
  frontRDistanceGB = 40;
  frontLDistanceGB = 40;
  modMF = 2;
  moveForward2(10000);
  turnRight();
  Stop();
  delay(1000);
  moveForward2(10200);
  turnRight();
  Stop();
  delay(1000);
  moveForward2(10400);
  turnRight();
  Stop();
  delay(1000);
  moveForward2(10900);
  turnRight(); 
}

void moveForward2(int ForwardDistanceMF)  {
  Stop();
  digitalWrite(MotorRA, LOW);
  analogWrite(MotorRB, 232); 
  analogWrite(MotorLA, 198);
  digitalWrite(MotorLB, LOW);
  int forwardcheckMF = 0;
  int RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
  while(RightfrontdistMF == 0){
    Stop();
    RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  int LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
  while(LeftfrontdistMF == 0){
    Stop();
    LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
    digitalWrite(LEDFront, HIGH);
  }
  while ((forwardcheckMF < ForwardDistanceMF) || (RightfrontdistMF > frontRDistanceGB) || (LeftfrontdistMF > frontLDistanceGB)){
    digitalWrite(MotorRA, LOW);
    analogWrite(MotorRB, 232); 
    analogWrite(MotorLA, 198);
    digitalWrite(MotorLB, LOW);
    if ((encoderArray[0] != digitalRead(encoderLeftA)) || (encoderArray[1] != digitalRead(encoderLeftB))){
      forwardcheckMF++;
      DistanceGB++;
      encoderArray[0] = digitalRead(encoderLeftA);
      encoderArray[1] = digitalRead(encoderLeftB); 
    }
    if(forwardcheckMF % modMF == 0) {
      RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin); 
      while(RightfrontdistMF == 0){
        Stop();
        RightfrontdistMF = getDistance(frontRightTrigPin, frontRightEchoPin);
        digitalWrite(LEDFront, HIGH);
      }
      LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin); 
      while(LeftfrontdistMF == 0){
        Stop();
        LeftfrontdistMF = getDistance(frontLeftTrigPin, frontLeftEchoPin);
        digitalWrite(LEDFront, HIGH);
      }
    }
  }
}





```