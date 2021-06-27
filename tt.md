//馬達一
#define M1Pin1 10
#define M1Pin2 9
#define E1Pin 11

//馬達二
#define M2Pin1 5
#define M2Pin2 4
#define E2Pin 3

void setup() {
  pinMode(M1Pin1,OUTPUT);
  pinMode(M1Pin2,OUTPUT);
  pinMode(E1Pin,OUTPUT);

  pinMode(M2Pin1,OUTPUT);
  pinMode(M2Pin2,OUTPUT);
  pinMode(E2Pin,OUTPUT);

  motorstop();
}

void loop() {
  directionControl();
  motorstop();
  delay(2000);
  speedControl();
  motorstop();
  delay(3000);
}

void motorstop()
{
  // 馬達停止
  digitalWrite(M1Pin1, LOW);
  digitalWrite(M1Pin2, LOW);
  digitalWrite(M2Pin1, LOW);
  digitalWrite(M2Pin2, LOW);
}

void directionControl()
{
  // 設定馬達為最大速度,可設定範圍為 0 to 255
  analogWrite(E1Pin, 255);
  analogWrite(E2Pin, 255);

  // 啟動馬達
  digitalWrite(M1Pin1, HIGH);
  digitalWrite(M1Pin2, LOW);
  digitalWrite(M2Pin1, HIGH);
  digitalWrite(M2Pin2, LOW);
  delay(2000);
  
  // 改變轉動方向
  digitalWrite(M1Pin1, LOW);
  digitalWrite(M1Pin2, HIGH);
  digitalWrite(M2Pin1, LOW);
  digitalWrite(M2Pin2, HIGH);
  delay(2000);
}

// 控制馬達速度
void speedControl()
{
  // 啟動馬達
  digitalWrite(M1Pin1, LOW);
  digitalWrite(M1Pin2, HIGH);
  digitalWrite(M2Pin1, LOW);
  digitalWrite(M2Pin2, HIGH);
  
  // 加速 從最小到最大
  for (int i = 0; i < 256; i++)
  {
    analogWrite(E1Pin, i);
    analogWrite(E2Pin, i);
    delay(20);
  }
  
  // 減速 從最大到最小
  for (int i = 255; i >= 0; --i)
  {
    analogWrite(E1Pin, i);
    analogWrite(E2Pin, i);
    delay(20);
  }
}
