# Robotics-mini-bot-code
code for a mini sumo robot competition project
#include <HCSR04.h>
const int In1 = 4;//L298N Motor Driver Module connected to Digital pins 4 to 7.
const int In2 = 5;//
const int In3 = 6;
const int In4 = 7;

const int MZ801 = 8;//MZ80 infrared sensor connected to digital pins 8 to 10. 
const int MZ802 = 9;
const int MZ803 = 10;

const int Reflector1 = 2;// Reflectance sensors connected to digital/interrupt pins 2 and 3.
const int Reflector2 = 3;

const int Force1 = A0;// Force sensor connected to analog pins A0 and A1.
const int Force2 = A1;
      int DisCM = 0;
      int state =LOW;
      int state1=LOW;
const int Stability = 13;//SW-520D Tilt angle sensor connected to digital pin 13.

UltraSonicDistanceSensor distanceSensor(12, 11); //11 on Trig pin, 12 on Echo pin of ultrasonic sensor

void setup()
{
  delay(5000);
  pinMode(In1,OUTPUT);//pins set as output for the two DC gear motors
  pinMode(In2,OUTPUT);
  pinMode(In3,OUTPUT);
  pinMode(In4,OUTPUT);
  
  pinMode(MZ801,INPUT);//pins set as input for the three MZ80 infrared sensor to detect opponent
  pinMode(MZ802,INPUT);
  pinMode(MZ803,INPUT);

  pinMode(Reflector1,INPUT);//pins set as input for Reflector sensors and Tilt angle sensor to detect the white and black changes of the dohyo
  pinMode(Reflector2,INPUT);
  pinMode(Stability,INPUT);//tilt angle sensor set as input to detect stability of the robot
  
  attachInterrupt(0, TurnBack, HIGH);// configuration of pins 2 and 3 as interrupt pins set to interrupt when a logic HIGH is detected from the 2 reflector sensors
  attachInterrupt(1, TurnBack, HIGH);//executing the turnback fucntion when activated
}

void loop()
{
  analogRead(Force1);
  analogRead(Force2);
 if (analogRead(Force1)>=200)
 {
  Attack();
 }
 else if( analogRead(Force2)>=200||MZ803==LOW)
 {
  Evade();
 }
 else
 {
  search();
 }
}

void search()
{
  
  DisCM = distanceSensor.measureDistanceCm(); //Measures distance in cm from oponent detected
  spin();
  if(DisCM==2&&DisCM==60)
  {
    STOP();
    if (digitalRead(MZ801)==HIGH && digitalRead(MZ802)==HIGH)
    {
      if(digitalRead(MZ803)==LOW)
      {
        STOP();
        TurnRight(180);
      }
      else
      {spin();}
    }
    else if(digitalRead(MZ801)==LOW && digitalRead(MZ802)==LOW)
    {
      Moveforward();
    }
    
    else if(digitalRead(MZ801)==HIGH && digitalRead(MZ802)==LOW)
    {
      TurnRight(100);
    }
    else if(digitalRead(MZ801)==LOW && digitalRead(MZ802)==HIGH)
    {
      TurnLeft(100);
    }
  }  
}
void MoveF(int DEL)
{
  digitalWrite(In1,HIGH);
  digitalWrite(In2,LOW);
  digitalWrite(In3,HIGH);
  digitalWrite(In4,LOW);
  delay(DEL);
}
void moveB(int LAY)
{
  digitalWrite(In1,LOW);
  digitalWrite(In2,HIGH);
  digitalWrite(In3,LOW);
  digitalWrite(In4,HIGH);
  delay(LAY);
}
void Moveforward()
{
  MoveF(100);
}
void Movebackward()
{
  moveB(100);
}
void Attack()
{
  digitalWrite(In1,HIGH);
  digitalWrite(In2,LOW);
  digitalWrite(In3,HIGH);
  digitalWrite(In4,LOW);
}
void TurnBack()
{
  
  state=digitalRead(Reflector1);
  state1=digitalRead(Reflector2);
  if (state==HIGH || state1==HIGH )
  {
  digitalWrite(In1,HIGH);
  digitalWrite(In2,LOW);
  digitalWrite(In3,LOW);
  digitalWrite(In4,HIGH);
  delay(100);
  }
  
}

void TurnLeft(int del)
{
  digitalWrite(In1,LOW);
  digitalWrite(In2,HIGH);
  digitalWrite(In3,HIGH);
  digitalWrite(In4,LOW);
  delay(del);
}
void TurnRight(int del)
{
  digitalWrite(In1,HIGH);
  digitalWrite(In2,LOW);
  digitalWrite(In3,LOW);
  digitalWrite(In4,HIGH);
  delay(del);
}
void spin()
{
  digitalWrite(In1,HIGH);
  digitalWrite(In2,LOW);
  digitalWrite(In3,LOW);
  digitalWrite(In4,HIGH);
}
void STOP()
{
  digitalWrite(In1,LOW);
  digitalWrite(In2,LOW);
  digitalWrite(In3,LOW);
  digitalWrite(In4,LOW);
}

void Evade()
{
  spin();
  delay(100);
  if (digitalRead(MZ801)==HIGH && digitalRead(MZ802)==HIGH && digitalRead(MZ803)==HIGH)
  {
    Moveforward();
    TurnLeft(10);
  }
}
