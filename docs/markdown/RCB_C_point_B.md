> 🐞 author: richie bao 📅 May 30, 2018
# 07_指针_B

## 指针变量的运算
由于指针变量的特殊性，可以对指针变量做有限的运算，例如赋值运算、部分算数运算和关系运算。
* 只有指向同一数组的两个指针变量之间才能进行运算，否则无意义；
* 指针变量的加减运算只能对数组指针变量进行，对指向其它类型变量的指针变量无意义；
* 两个指针变量之间不能进行加法运算，即两个地址相加无意义。

### 关系运算

对于指向同一数组的两个指针变量做关系运算，即为指针所指向数组元素之间的关系运算。如果指针变量ptr_a和ptr_b指向同一个数组，则<、>、>=、<=、==等关系运算符都可以使用。

* ptr_a==ptr_b为真，则表示ptr_a和ptr_b指向同一数组元素；
* ptr_a<ptr_b为真，则表示ptr_a处于低地址位置，而ptr_b处于高地址位置；
* ptr_a>ptr_b为真，则表示ptr_a处于高地址位置，而ptr_a处于低地址位置。

当未给指针变量赋值时，即为空指针，其值为0，不指向任何变量。因此指针变量可以与0比较。

* pt==0为真时，表示pt为空指针；
* pt!=0为真时，表示pt非空指针。

### 算数运算

指针变量和整数可进行简单的加、减运算。如果ptr为指向数组array的指针变量，开始时指向数组的某个元素，设n为一个正整数，则如下运算合法。

* ptr+n，指针变量指向的位置向前移动n个位置；
* ptr-n，指针变量指向的位置向后移动n个位置；
* ptr++，指针变量指向的位置向后移动1个位置；
* ptr--，指针变量指向的位置向前移动1个位置；
* ++ptr，先取指针变量的当前位置，再将指针变量指向的位置向后移动1个位置；
* --ptr，先取指针变量的当前位置，再将指针变量指向的位置向前移动1个位置。

指针变量加减整数n，是把指针指向某一数组元素的当前位置前后移动n个位置。因为数组有不同类型，即数组的元素类型不同，不同类型的元素所占字节长度不同(*即指针的标量，例如int的标量为2byte，而指针的运算会自动适应数据类型的标量*)。因此，指针变量的加减1，即向前后移动1个位置时，为指针变量指向前一个或者下一个数组元素的首地址，而不是原地址加减1。

不论指针变量指向何种数据类型，指针和整数进行加减运算时，编译程序总根据所指对象的数据长度对n放大，char(字长8)类型放大因子为1，int(16)和short为2，long和float(32)为4，double为8.

两个指针变量在一定条件下，可以进行减法运算。两指针变量相减所得之差是两个指针所指数组元素之间相差的元素个数。实际上是两个指针，即地址相减的差再除以该数组元素的长度，即字节数。例如，ptr_1和ptr_2是指向同一浮点(字长32，4个字节)数组两个指针变量，如果ptr_1=2030H，ptr_2=2050H，则ptr_1-ptr_2=(2050H-2010H)/4=64D/4=16，表示ptr_1和ptr_2之间相差16个元素，每个元素4字节。

### 实验程序

```c
//Dr.Purdum,August 20,2012
//modified on 29 Apr 2018 by richieball
#include <string.h>

void setup() {
  Serial.begin(9600);
}

void loop() {
  char buffer[50];
  char *ptr;
  int i;
  int length;
  strcpy(buffer,"digit-x,编程让设计更具创造力！");
  ptr=buffer;
  length=strlen(buffer);
  Serial.print("指针的地址:");
  Serial.println((unsigned int)&ptr);
  Serial.print("指针指向地址的值：");
  Serial.println((unsigned int)ptr);
  while(*ptr){
    Serial.print(*ptr++);
  }
  Serial.println("");
  ptr=buffer;
  Serial.println("给定数组部分数据的地址：");
  Serial.println(*(ptr+2));
  Serial.println(*(ptr+7));
  Serial.println(*(ptr+8));  //注意汉字占用的byte数
  Serial.flush();//确保所有数据送出
  exit(0);
}
```

**运算结果**

```
指针的地址:2298
指针指向地址的值：2248
digit-x,编程让设计更具创造力！
给定数组部分数据的地址：
g
,
⸮
```

## 数组指针
数组是把具有相同数据类型的若干变量按有序形式组织起来的集合。数组是有序存放于连续的存储单元中，数组名就是数组在存储单元中的首地址。数组指针是指向数组的指针变量，可以指向数组的首地址或数组元素的地址，即指针可以指向数组或数组元素。因此，对于数组而言，除了使用下标，也可以使用指针引用数组和数组元素。
### 指向一维数组的指针
由于数组元素连续存储于存储单元中，因此通过指针变量及有关运算可以间接访问数组中的任何一个元素。如果定义数组array和指向该数组元素首地址的指针变量ptr，则：
*  array+i和ptr+i，均表示数组元素array[i]的地址，即指向数组第i个元素，因为数组名表示数组的首地址，而指针变量ptr也指向数组的首地址，因此array+i和ptr+i都是地址加i；
* (array+i)和*(ptr+i)，因为array+i和ptr+i相同，均为数组元素array[i]的地址，而*为取内容运算符，因此*(array+i)和*(ptr+i)即为array[i]的值；
* C允许指针变量带下标，即指向数组元素的指针也可以用数组的形式表示，即pt[i]与*(ptr+i)等价；
*  int array[12],*ptr; ptr=array+5;，该语句中赋予指向数组的指针变量初值并不是数组array的首地址，而是首地址+5所指向的地址，因此pt[2]为array[7]，pt[-2]为array[5]。

#### 实验程序(一维数组)

```c
//richieball 2018-04-28
#define months 12
int days[months]={31,28,31,30,31,30,31,31,30,31,30,31};  //定义一维数组，包含12个元素
int idx,*pt=days;  //声明整型变量Idx，以及声明指针变量pt并初始化为数组days的首地址

void setup() {
  Serial.begin(9600);
}

void loop() {
  for(idx=0;idx<months;idx++){  //循环数组
    Serial.println("Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x");
    Serial.println(idx+1);
    Serial.println(*(days+idx));
    Serial.println(pt[idx]);
    Serial.println(*(pt+idx));
    Serial.println(days[idx]);
  }
  Serial.flush();
  exit(0);
}
```

**运算结果**

```
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
1
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
2
28
28
28
28
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
3
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
4
30
30
30
30
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
5
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
6
30
30
30
30
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
7
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
8
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
9
30
30
30
30
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
10
31
31
31
31
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
11
30
30
30
30
Month x has x days. pt[idx]=x,*(pt+idx)=x,days[idx]=x
12
31
31
31
31
```

对于一维数组如果int array[3],*ptr;则具有如下等价关系：
* array+i=ptr+i;
* array[i]=*(array+i)=ptr[i]=*(ptr+i)

<img src="./imgs/0132.jpg" height="auto" width="auto"  title="digit-x" />

### 指向二维数组的指针

```c
int array_t[3][5]={{2，4，6，8，10}，{1，3，5，7，9}，{12，14，16，18，20}};  //定义整型3行，5列包含15个元素的二维数组array_t
```




<img src="./imgs/0133.jpg" height="auto" width="auto"  title="digit-x" />


二维数组同样需要根据元素的类型对地址作适当的放大。例如array_t[3][5]声明为二维整型数组，每个数组元素占两个字节的存储单元。因此，假设array_t的首地址为0x1000，array_t+1的地址则为0x100A，即0x1000+2×5D=0x1000+0x000A=0x100A，array_t+2的地址则为，即0x1000+4×5D=0x1000+0x0014=0x1014。
在C51中，array_t[0]、array_t[1]、array_t[2]可以看作3行一维数组名，分别代表每行一维数组的首地址，因此：
* array_t[0]代表第0行第0列元素的地址，即&array_t[0][0];array_t[1]则是第1行第0列元素的地址，即&array_t[1][0]。
* array_t[0]+1代表第0行第1列的地址，即&array_t[0][1]；则array_t[i]+j代表第i行第j列的地址，即&array_t[i][j]。

因为数组名类似于指针变量，因此：
* array[0]=&array[0][0]=*(array+0);
*  array[i]+j=&array[i][j]=*(array+i)+j;
* array[i][j]=*(array[i]+j)=*(*(array+i)+j)=(*(array+i))[j];

对于二维数组如果int array[3][5],*ptr;ptr=array;则具有如下等价关系：
* array=*array=array[0]=ptr;
* array[i]=*(array+i)=*(ptr+i)
* &array[i][j]=array[i]+j=*(array+i)+j=*(ptr+i)+j;
* array[i][j]=*(array[i]+j)=*(*(array+i)+j)=*(*(ptr+i)+j)=(*(ptr+i))[j]。

#### 实验程序(二维数组)

```c
//richieball 2018-04-28
#include <stdio.h>
int array_t[3][5]={{2,4,6,8,10},{1,3,5,7,9},{12,14,16,18,20}};
int *ptr,i,j;

void setup() {
  Serial.begin(9600);
}

void loop() {
  for(i=0;i<3;i++) for(j=0;j<5;j++){
    Serial.println("array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d\n");    
    Serial.print(i);
    Serial.print(" ");
    Serial.println(j);
    Serial.println(array_t[i][j]);
    Serial.println(*(*(array_t+i)+j));
  }
  for(ptr=array_t[0];ptr<array_t[3];ptr++) Serial.println(*ptr);
  Serial.flush();
  exit(0);
}
```

**实验结果**

```
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

0 0
2
2
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

0 1
4
4
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

0 2
6
6
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

0 3
8
8
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

0 4
10
10
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

1 0
1
1
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

1 1
3
3
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

1 2
5
5
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

1 3
7
7
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

1 4
9
9
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

2 0
12
12
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

2 1
14
14
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

2 2
16
16
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

2 3
18
18
array_t[%d][%d]=%d  *(*(array_t+%d)+%d)=%d

2 4
20
20
2
4
6
8
10
1
3
5
7
9
12
14
16
18
20
```

### 指向一个由n个元素组成的数组指针
C中引入一个指向n个元素构成的数组指针。方便二维数组的处理，格式为：类型标识符 (*指针名)[n];
* int array[3][5];  //定义二维数组
* int (*ptr)[5];  //声明指针，包含参数[5]
* ptr=array;  //指针赋值数组首地址

> 指针pt是指向一个由列数，即5个元素组成的整型数组指针，当对该指针加1运算，其地址址实际上增加了2×5=10，相当于指向数组各行的元素。

#### 实验程序(含参数指针)

```c
//richieball 2018-04-30
int array_t[3][5]={{2,4,6,8,10},{1,3,5,7,9},{12,14,16,18,20}};
int i,(*ptr)[5]=array_t;

void setup() {
  Serial.begin(9600);
}

void loop() {
  for(i=0;i<3;i++){
    Serial.print(*ptr[i]);
    Serial.print(" ");
    Serial.println(*(ptr[i]+1));
  }
  ptr=&array_t[0];  //重新初始化数组指针
  Serial.println((long)&array_t[0][3],DEC);
  Serial.println((long)&array_t[0],DEC);
  for(i=0;i<3;i++) Serial.println(*ptr[i]);
  Serial.flush();
  exit(0);
}
```

**实验结果**

```
2 4
1 3
12 14
262
256
2
1
12
```

## 字符指针
字符指针指向字符串，然后通过字符指针来访问字符串的存储区域。
### 实验程序(字符指针-1)

```c
//richieball 2018-04-30
  char str[]="Hi digit-x!";
  char *pt="Hi digit-x!";
void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.println(str);
  Serial.println(*pt);
  Serial.println(pt[0]);
  Serial.println(pt[5]);
  Serial.println((*(pt+5)));
  Serial.flush();
  exit(0);
}
```

**实验结果**

```
Hi digit-x!
H
H
g
g
```

声明char str_array1[3][50]={"Hello ","caDesign ","Everybody should learn programming!"};数组，其中列的下标值为50，虽然最小应该为所包含各字符串中的最大长度，即36，但是部分字符串长度远小于36，例如Hello 只有6个(包括空格)，会浪费单片机的资源，而指针数组可以有效解决这个问题。可以定义为char *str_array1[3]={"Hello ","caDesign ","Everybody should learn programming!"};
### 实验程序(字符指针-2)

```c
//richieball 2018-04-30
int i;char *str_array[3]={"Hello ","数字营造学社","Everybody should learn programming!"};
void setup() {
  Serial.begin(9600);
}

void loop() {
  for(i=0;i<3;i++){
    Serial.print(i);
    Serial.print(":");
    Serial.println(str_array[i]);
    }  
    Serial.flush();
    exit(0);
}
```

**实验结果**

```
0:Hello 
1:数字营造学社
2:Everybody should learn programming!
```

## 函数指针
可以用指针调用函数。基本语法为：`float (*funPtr)(int x); funPtr=calculateSqrt; float result=(*funPtr)(readSD);`其中`float (*funPtr)(int x); `定义函数指针，即指向函数的指针，float数据类型为函数返回值数据类型，（int x）为传入的参数。`funPtr=calculateSqrt; `中calculateSqrt为自定义的函数，将函数指针指向该函数。`float result=(*funPtr)(readSD);`是使用函数指针，传入参数返回值。

如果不返回值则写作`void(*funPtr)(int x);`。如何既不返回值，也不传入参数则写作`void(*funPtr)();`
### 实验程序(函数指针)

```c
//richieball 2018-04-30
int incomingByte = 0;

void setup() {
  Serial.begin(9600);
}

void loop() {
  if (Serial.available() > 0) {
    char readSerialData[20];
    int index= readLine(readSerialData); 
    int readSD=atoi(readSerialData);
    Serial.print("sqrt(");Serial.print(readSD);Serial.print(")=");
    float (*funPtr)(int x);
    funPtr=calculateSqrt;
    float result=(*funPtr)(readSD);
    Serial.println(result);    
  }
  //Serial.flush();
  //exit(0);
}

float calculateSqrt(int val){
  return sqrt(val);
}

int readLine(char str[]){
  char c;
  int index=0;  
  while (true){
    if (Serial.available()>0){
      c=Serial.read();      
      if(c!= '#'){
        str[index++]=c;   
        //Serial.print("ok");  //用于调试标识
      }else{
        str[index]='\0';        
        break;
      }
    }
  }
  return index;
}
```

**实验结果**

```
sqrt(100)=10.00
sqrt(99)=9.95
```

### 函数指针数组
#### 枚举
枚举类型主要用于将变量的取值限定在一个有限范围内。枚举类型在定义中列举出所有可能的取值，被声明为该类型的变量取值不能超过定义的范围。枚举可以明了表示取值，并节约存储空间。

**枚举定义形式**

```c
enum 枚举名{
标识符[=整型常量];
标识符[=整型常量];
…
}；
```
**枚举变量的声明**

```c
//1.定义枚举时声明枚举变量
enum 枚举名{
标识符[=整型常量];
标识符[=整型常量];
…
}枚举变量；
//2.先定义枚举，再定义枚举变量
enum 枚举名{
标识符[=整型常量];
标识符[=整型常量];
…
}；
enum 枚举名 枚举变量名；
//3.直接声明枚举变量(没有枚举名)
enum  {
标识符[=整型常量];
标识符[=整型常量];
…
}枚举变量；
```
#### 实验（枚举）

```c
//richieball 2018-04-30
enum weekday{Monday,Tuesday,Wednsday,Thurday,Friday,Saturday,Sunday}a;
void setup() {
  Serial.begin(9600);
}

void loop() {
  a=Monday;Serial.println(a);
  a=Tuesday;Serial.println(a);
  a=Wednsday;Serial.println(a);
  a=Thurday;Serial.println(a);
  a=Friday;Serial.println(a);
  a=Saturday;Serial.println(a);
  a=Sunday;Serial.println(a);
  Serial.flush();
  exit(0);
}
```

**运行结果**

```
0
1
2
3
4
5
6
```
枚举元素本身由系统定义了一个表示序号的数组。

此例中通过调用函数，返回计算结果，使用函数指针，根据不同结果进一步调用不同函数。
### 实验程序(函数指针-共阳数码管/DHT11温湿度传感器)
**电路图**
<img src="./imgs/0135.jpg" height="auto" width="auto"  title="digit-x">

```c
//richieball 2018-04-30
#include <Adafruit_Sensor.h>
#include <DHT.h>
#include <DHT_U.h>
#define DHTPIN 8
#define DHTTYPE DHT11 // DHT 11
DHT_Unified dht(DHTPIN, DHTTYPE);
uint32_t delayMS;

byte idx=0;
const byte LEDs[13]{
  //B1111110, //0x7E
  0x7E,
  B0110000,
  B1101101,
  B1111001,
  B0110011,
  B1011011,
  B1011111,
  B1110000,
  B1111111,
  B1111011,
  //B0001110, //0xE
  0xE,
 // B0110111,//0x37
  0x37,
  //B0000001 //0x1
  0x1
};

enum humidityRank {HIGHVAL,MODERATEVAL,LOWVAL};
enum humidityRank whichRank;
const int highVal=37;
const int lowVal=10;

const int ledPin=12;

void setup() {
  DDRD=0x7F;
}

void loop() {
  static void (*funcPtr[])()={LEDHighWarning,LEDModerate,LEDLowWarning};     
  delay(delayMS);
  sensors_event_t event;    
  //读取湿度数据并串口打印
  dht.humidity().getEvent(&event);
  if (isnan(event.relative_humidity)) {
   digitalWrite(ledPin,HIGH);
  } else {
    whichRank=(enum humidityRank) whichRankJudge(event.relative_humidity);
    (*funcPtr[whichRank])();
  }  
  delay(1000);
}

int whichRankJudge(float humidity){
  if (humidity<lowVal){
    return LOWVAL;
  }else{
    if (humidity>highVal) {
      return HIGHVAL;
    }else return MODERATEVAL;    
  }
}

void LEDHighWarning(){
  PORTD=~LEDs[11];
}

void LEDModerate(){
  PORTD=~LEDs[12];
}

void LEDLowWarning(){
  PORTD=~LEDs[10];
}
```

**运行结果**
<img src="./imgs/0134.jpg" height="auto" width="auto"  title="digit-x">


