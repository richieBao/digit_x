> 🐞 author: richie bao 📅 Jun 4, 2018
# 08_结构与联合

> 实验器材：Arduino UNO(1)，面包板(1)，电阻(100Ω(2，限流电阻))，LED灯(1)，DHT11温湿度传感器(1)，共阳极数码管(1)

虽然数组能够条理化的存储数据，但是数组是把具有相同数据类型的若干变量按有序形式组织起来的集合。如果需要存储不同类型的数据在一个变量或结构中，例如同时包含商品名称(字符型)、编码(整型)、数量(整型)、单价(浮点型)、出口国(字符型)等数据，就需要再选择一个更好表示数据的的方法。结构变量(structure variable)则增强了表达数据的能力，结构的基本形式足以灵活的存储各类型数据。
## 结构
### 结构的定义与声明
结构是一种构造类型，是由数目固定、类型不同的若干有序成员集合而成的数据类。其中每一个成员可以是一个基本数据类型，甚至可以是一个构造类型。结构不是基本的数据类型，需要在使用之前进行定义，结构的一般定义形式：
<img src="./imgs/0138.jpg" height="auto" width="auto"  title="digit-x">

>  //对于字符型数据，一般使用字符指针，才能顺利编译

该结构定义结构名即标记为form，由5类数据商品名称Pname、编码Pcode、数量Pnum、价格Pprice、出口国Pexporter组成，包含char、int、float三种数据类型。结构定义只是描述了组成form结构对象的元素存储类型及如何存储。在定义结构之后，需要再声明结构变量。
### 结构变量的声明

```c
//1.先定义结构再声明结构变量
struct form{
	char *Pname;
	int Pcode;
	int Pnum;
	float Pprice;
	char *Pexporter;
};
struct form fruit,vegetation; //可以一次声明多个结构变量
//2.在定义结构的同时，声明结构变量
struct form{
	char *Pname;
	int Pcode;
	int Pnum;
	float Pprice;
	char *Pexporter;
}fruit,vegetation;
//3.直接说明结构变量
struct {
	char *Pname;
	int Pcode;
	int Pnum;
	float Pprice;
	char *Pexporter;
}fruit,vegetation;
/*
未给出结构名，直接声明结构变量fruit、vegetation。需要注意，直接说明结构变量的方法，可以使用此处声明的两个结构变量，而不能再额外声明其他结构的变量。
*/
//声明的结构变量fruit、vegetation均包含结构定义中的Pname、Pcode、Pnum、Pprice、Pexporter五个部分。
```
### 访问结构(变量)成员
访问数组元素时，可以使用下标访问数组的各个元素。而访问结构成员时，可以使用结构成员运算浮点"."。例如fruit.Pname 即访问结构变量fruit的Pname字段(数据库中使用字段表示结构中的单个成员数据)

* fruit.Pprice 即访问结构变量fruit的Pprice字段
* vegetation.Pname  即访问结构变量vegetation的Pname字段

本质上.Pname，.Pprice，.Pname在form结构中具有下标作用。结构变量fruit、vegetation是结构类型，而fruit.Pname、fruit.Pprice或vegetation.Pname则为具体数据类型字符型、浮点型和字符型，可以像使用任何其他数据类型变量一样来使用。例如，&(fruit.Pname)为获取Pname字段的首地址，注意结构成员运算浮点"."优先级高于取地址运算符"&"，因此需要圆括弧调整优先级。

### 结构的嵌套
嵌套结构是一个结构中包含另一个结构。例如在form的结构中Price字段又包含国内价格和国外价格两类数据，可以使用嵌套结构来定义。

```c
struct form{  
	char *Pname;
	int Pcode;
	int Pnum;
	struct price Pprice;  //声明子结构变量Pprice，Pprice则具有price结构所包含的字段domestic和abroad
	char *Pexporter;
}
struct price{
	float domestic;
	float abroad;
};
struct form fruit,vegetation;
```
### 结构变量的赋值

```c
//richieball 2018-04-30
struct form{  //定义form结构
  char *Pname;  
  int Pcode;
  int Pnum;
  float Pprice;
  char *Pexporter;
};

void setup() {
  Serial.begin(9600);
  struct form fruit;  //声明fruit结构变量
  fruit.Pname="apple";
  fruit.Pcode=001;
  fruit.Pnum=33;
  fruit.Pprice=10.2;
  fruit.Pexporter="London";
  Serial.print("name=");Serial.println(fruit.Pname);
  Serial.print("exporter=");Serial.println(fruit.Pexporter);
  Serial.print("code=");Serial.println(fruit.Pcode);
  Serial.print("num=");Serial.println(fruit.Pnum);
  Serial.print("price=");Serial.println(fruit.Pprice);
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**

```
name=apple
exporter=London
code=1
num=33
price=10.20
```
嵌套结构赋值，先找到父结构声明的结构变量fruit，再顺应找到定义子结构变量的字段，例如Pprice，最后找到子结构的字段，使用结构成员运算浮点"."连接为fruit.Pprice.domestic或者fruit.Pprice.abroad。

```c
//richieball 2018-04-30
struct price{  //定义子结构
  float domestic;
  float abroad;
};

struct form{  //定义父结构
  char *Pname;
  int Pcode;
  int Pnum;
  struct price Pprice;  //该字段为声明子结构变量Pprice
  char *Pexporter;
};

void setup() {
  Serial.begin(9600);
  struct form fruit;  //声明父结构变量fruit
  fruit.Pname="apple";
  fruit.Pcode=001;
  fruit.Pnum=33;
  fruit.Pprice.domestic=10.2;  //为子结构变量字段domestic赋值
  fruit.Pprice.abroad=60.2;  //为子结构变量字段abroad赋值
  fruit.Pexporter="London";
  Serial.print("price_domestic=");Serial.println(fruit.Pprice.domestic);
  Serial.print("price_abroad=");Serial.println(fruit.Pprice.abroad);
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**

```
price_domestic=10.20
price_abroad=60.20
```
### 结构变量的初始化

```c
struct 结构名{
    类型说明符 成员名[]；
    类型说明符 成员名[]；
    …
}结构变量={值，值，…}；
```

```c
//richieball 2018-04-30
struct price{
  float domestic;
  float abroad;
};

struct form{
  char *Pname;
  int Pcode;
  int Pnum;
  struct price Pprice;
  char *Pexporter;
}fruit={"apple",001,33,{10.2,60.2},"London"};   //待赋予的值放于{ }大括号中，包含的子结构变量也同样放于大括号中
void setup() {
  Serial.begin(9600);
  Serial.print("name=");Serial.println(fruit.Pname);
  Serial.print("exporter=");Serial.println(fruit.Pexporter);
  Serial.print("code=");Serial.println(fruit.Pcode);
  Serial.print("num=");Serial.println(fruit.Pnum);
  Serial.print("price_domestic=");Serial.println(fruit.Pprice.domestic);
  Serial.print("price_abroad=");Serial.println(fruit.Pprice.abroad);
}

void loop() {
  // put your main code here, to run repeatedly
}
```
**运行结果**

```
name=apple
exporter=London
code=1
num=33
price_domestic=10.20
price_abroad=60.20
```
> 或者先定义结构再声明结构变量时初始化结构变量：`struct form fruit={"apple",001,33,{10.2,60.2},"London"};`

### 结构数组
如果结构变量不只是包含一组数据，例如`struct form fruit={"apple",001,33,{10.2,60.2},"London"};`只有一种水果及其相关属性值，如果需要同时包含多组数据，则可以使用结构数组。

```c
//richieball 2018-04-30
struct form{
  char *Pname;
  int Pcode;
  int Pnum;
  float Price;
  char *Pexporter;
};

void setup() {
  Serial.begin(9600);
  int i;
  struct form fruit[3]={
    {"apple",001,100,10.2,"London"},
    {"orange",002,200,9.8,"Singapore"},
    {"banana",003,300,6.6,"Mexcico"}
  };
  for(i=0;i<3;i++){     //循环数组的行输出各组数据
     if(fruit[i].Price<10){  //可以给出条件，打印满足条件的数据
        Serial.print("name=");Serial.println(fruit[i].Pname);
        Serial.print("exporter=");Serial.println(fruit[i].Pexporter);
        Serial.print("code=");Serial.println(fruit[i].Pcode);
        Serial.print("num=");Serial.println(fruit[i].Pnum);
        Serial.print("price=");Serial.println(fruit[i].Price);
    }
  }
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**
```
name=orange
exporter=Singapore
code=2
num=200
price=9.80
name=banana
exporter=Mexcico
code=3
num=300
price=6.60
```
上述结构变量初始化等价于：

```c
struct form{
	char *Pname;
	int Pcode;
	int Pnum;
	float Price;
	char *Pexporter;
}fruit[3]={
		{"apple",001,100,10.2,"London"},
		{"orange",002,200,9.8,"Singapore"},
		{"banana",003,300,6.6,"Mexcico"}
	};
```
### 结构指针
结构指针是指针变量指向一个结构变量。结构指针变量的值是指向结构变量的首地址。通过结构指针可以访问结构变量，与数组指针类似。定义的一般形式为：
<img src="./imgs/0136.jpg" height="auto" width="auto"  title="digit-x">

```c
//richieball 2018-04-30
struct da_price{  //定义子结构
  float domestic;
  float abroad;
};

struct form{  //定义父结构
  char *Pname;
  int Pcode;
  int Pnum;
  struct da_price Price;
  char *Pexporter;
}fruit[3]={  //初始化赋值
    {"apple",001,100,{10.2,60.2},"London"},
    {"orange",002,200,{6.8,68.8},"Singapore"},
    {"banana",003,300,{9.6,66.6},"Mexcico"}
  };
  
void setup() {
  Serial.begin(9600);
  struct form *ptr;  //定义结构指针
  ptr=fruit;  //初始化结构指针为fruit结构变量的首地址
  Serial.print("Oname=");Serial.print(fruit[1].Pname);Serial.print(" OPnum=");Serial.println(fruit[1].Pnum);
  Serial.print("Oname=");Serial.print((*ptr).Pname);Serial.print(" OPnum=");Serial.println((*ptr).Pnum);
  Serial.print("Oname=");Serial.print((ptr+1)->Pname);Serial.print(" OPnum=");Serial.println((ptr+1)->Pnum);
  Serial.print("Oname=");Serial.print((ptr+1)->Price.domestic);Serial.print(" OPnum=");Serial.println((*(ptr+1)).Price.abroad);
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**
```
Oname=orange OPnum=200
Oname=apple OPnum=100
Oname=orange OPnum=200
Oname=6.80 OPnum=68.80
```
<img src="./imgs/0137.jpg" height="auto" width="auto"  title="digit-x">

> `(*结构指针变量).字段`在Arduino C中测试错误。

`(ptr+1)->Price.domestic=(*(ptr+1)).Price.abroad`“.”与"->"为成员(字段)符，“.”用于结构变量.字段，而"->"用于结构指针->字段。"."的优先级高于*及四则运算，因此需要元括弧改变优先级。

ptr+1，因为ptr指向fruit的首地址，即第0行，字段"apple"的首地址。将其加一，直接跳到第1行，即字段"orange"的首地址，以此类推。*(ptr+1)，是获取第1行首地址的数值，即"orange"。

### 位结构
位结构是一种特殊的结构，用于访问一字节或字的多个位。位结构定义的一般形式：

```c
struct 位结构名{
    数据类型 变量名：整型常数；
    数据类型 变量名：整型常数；
}位结构变量;
```

```c
struct{
unsigned LBitV:8;  //LBitV占用低字节的0～7共8位
unsigned HBitV 1:4;  //HBitV 1占用高字节的0～3位共4位
unsigned HBitV2:3;  //HBitV 2占用高字节的4～6位共3位
unsigned HBitV 3:1;  //HBitV 3占用高字节的7位共1位
}BitV;
```
数据类型必须是Int，unsigned或者signed。整型常数必需是非负的整数，范围在0～15，表示二进制位的个数，即表示有多少位。变量名为可选项，可以不命名，这样规定是为了排列需要。

位结构成员(字段)的访问与结构成员的访问相同，例如BitV.LBitV

需要按位访问数据的位时，采用位结构比使用位运算符方便。在使用位结构时，应注意：
* 为结构中的成员既可以定义为unsigned，也可以定义为signed。
* 位结构总长度，即位数是各个位成员定义的位数之和，可以超过两个字节。
* 当成员长度为1时，会被认为是unsigned的类型，因为单个位不可能具有符号。
* 位结构中的成员不能使用数组和指针，但位结构变量可以是数组和指针。如果是指针，其成员访问方式同结构指针。
* 位结构成员可以与其它结构成员一起使用。

```c
struct form{
	char *Pname;
	int Pcode;
	int Pnum;
	struct da_price Price;
	char *Pexporter;
	unsigned quarantine:1; //quarantine用于存储是否检疫，而Packing用于存储是否装箱。每个位结构成员只有一位，因此一个字节保存了两个信息，节省存储空间。
	unsigned packing:1;
};
```
> 位结构部分未在Arduino C中测试。

### 向函数传递结构信息
结构(变量)可以作为参数传递给函数，或者指向结构(变量)的指针作为参数传递，以及结构(变量)成员作为参数传递。
#### 传递结构成员
只要结构成员是具有单个值的数据类型，例如int、char、float、double等，就可以把它作为参数传递给一个可以接受这个特定类型的函数。

```c
//richieball 2018-04-30
double sum(double,double,double);  //函数声明
struct da_price{  //定义子结构
  float domestic;
  float abroad;
};
struct form{  //定义父(多重)结构
  char *Pname;
  int Pcode;
  int Pnum;
  struct da_price Price;
  char *Pexporter;
}fruit[3]={  //初始化结构fruit赋值
    {"apple",001,100,{10.2,60.2},"London"},
    {"orange",002,200,{6.8,68.8},"Singapore"},
    {"banana",003,300,{9.6,66.6},"Mexcico"}
  };
  
void setup() {
  Serial.begin(9600);
  Serial.print("国内价格总和=");Serial.print(sum(fruit[0].Price.domestic,fruit[1].Price.domestic,fruit[2].Price.domestic));
}

void loop() {
  // put your main code here, to run repeatedly:

}

double sum(double x,double y,double z){
  return(x+y+z);
}
```
**运行结果**

```
国内价格总和=26.60
```
#### 使用结构变量地址

```c
//richieball 2018-04-30
double sum(const struct form *num);
struct da_price{
  float domestic;
  float abroad;
};

struct form{
  char *Pname;
  int Pcode;
  int Pnum;
  struct da_price Price;
  char *Pexporter;
}fruit[3]={
    {"apple",001,100,{10.2,60.2},"London"},
    {"orange",002,200,{6.8,68.8},"Singapore"},
    {"banana",003,300,{9.6,66.6},"Mexcico"}
  };
  
void setup() {
  Serial.begin(9600);
  Serial.print("数量总和=");Serial.println(sum(fruit));
}

void loop() {
  // put your main code here, to run repeatedly:
}

double sum(const struct form *num){
  return(num->Pnum+(num+1)->Pnum+(num+2)->Pnum);
}
```
**运行结果**
```
数量总和=600.00

```
#### 把结构变量作为参数传递
只有允许把结构变量作为参数传递的编译器才可以传递结构变量，对于Arduino C测试等大部分编译器暂时无法传递。暂不赘述。

## 联合
不同数据类型的组成为聚合类型。结构是典型的数据类型，另外在C语言下还有联合及枚举(前文)。

联合类型是将不同变量组织成一个整体的数据类型。这些变量在存储器中占用同一段存储单元，并在不同的时间保存不同的数据类型和不同长度的变量。联合类型也称为共用体。联合的作用类似小型缓存，记录预定义的数据类型。
### 联合的定义

```c
union 联合名{
数据类型 成员名；
数据类型 成员名；
…
};
```
### 联合变量的声明

```c
//1.定义联合时声明联合变量
union 联合名{
数据类型 成员名；
数据类型 成员名；
…
}联合变量；
//2.先定义联合，再定义联合变量
union 联合名{
数据类型 成员名；
数据类型 成员名；
…
}；
union 联合名 联合变量名；
//3.直接声明联合变量(没有联合名)
union {
数据类型 成员名；
数据类型 成员名；
…
}联合变量；
```
### 联合与结构的区别
从联合的定义、联合变量的声明、成员的引用到联合变量指针基本同于结构的用法。因此，需要先区别二者之间的差异，这个差异主要在于数据存储的方式。联合变量的成员占用同一个存储空间，而结构变量中的成员分别独占自己的存储空间，互不干扰。对于有不同数据类型组成的联合变量和结构变量，在任何同一时刻，联合变量中只存放一个被选中的成员，对于联合变量的不同成员赋值，将会对其它成员重写，原成员的值就不再存在；而结构的所有成员都存在。

```c
//richieball 2018-04-30
#include <stdio.h>
union form{
  char *Pname;
  int Pcode;
  int Pnum;
  union {
    float domestic;
    float abroad;   
  }Price;
  char *Pexporter;
};

void setup() {
  Serial.begin(9600);
  union form fruit;
  fruit.Pname="apple";
  fruit.Pcode=101; //写入Pcode成员值后，之前对Pname成员的赋值被重写
  Serial.print("name=");Serial.println(fruit.Pname);
  Serial.print("Pcode=");Serial.println(fruit.Pcode);
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**

```
name=??
Pcode=101
```
### 联合变量数组
给联合变量成员赋值，仅保留最后一个成员的值，如果希望值不被重写，最直接的方式是使用struct结构。如果仍然是使用union联合，则可以考虑使用联合变量数组。以数组的方式给出下标(索引)存储成员值。

```c
//richieball 2018-04-30
union form{
  char *Pname;
  int Pcode;
  int Pnum;
  union {
    float domestic;
    float abroad;   
  }Price;
  char *Pexporter;
};

void setup() {
  Serial.begin(9600);
  union form fruit[6];
  fruit[0].Pname="apple";
  fruit[1].Pcode=101;
  fruit[2].Pnum=100;
  fruit[3].Price.domestic=10.2;
  fruit[4].Price.abroad=60.2;
  fruit[5].Pexporter="London";
  Serial.print("name=");Serial.println(fruit[0].Pname);
  Serial.print("exporter=");Serial.println(fruit[5].Pexporter);
  Serial.print("code=");Serial.println(fruit[1].Pcode);
  Serial.print("num=");Serial.println(fruit[2].Pnum);
  Serial.print("price_A=");Serial.println(fruit[4].Price.abroad);
  Serial.print("price_D=");Serial.println(fruit[3].Price.domestic);
}

void loop() {
  // put your main code here, to run repeatedly:
}
```
**运行结果**
```
name=apple
exporter=London
code=101
num=100
price_A=60.20
price_D=10.20
```
## 按位操作符

| 符号 | 描述 | 运算规则 | 汇编语言助记符 |
| --- | --- | --- | --- |
| & | 按位与 | 两个位都为1时，结果才为1 | ANL |
| | | 按位或 | 两个位都为0时，结果才为0 | ORL |
| ^ | 按位异或 | 两个位相同为0，相异为1 | XRL |
| ~ | 按位取反| 0变1、1变0 | CPL |
| << | 左移 | 各二进位全部左移指定位数，高位丢弃，低位补0 | RL/RLC |
| >> | 右移 | 各二进位全部右移指定位数，对无符号数，高位补0；有符号数，各编译器处理方法不一，可补符号位(算数右移)、可补0(逻辑右移) | R/RRC |

C中有6种位运算符，为&(按位与)、|(按位或)、^(按位异或)、~(按位取反)、<<(左移)和>>(右移)。位运算符的作用是按位对变量运算，并不改变参与运算变量的值。位运算符只能用于整型操作，即只能用于带符号或无符号的char、short、int与long类型。在实际应用中建议用unsigned无符号整型操作数，因为带有符号操作数可能因为不同器件结果不同。
单片机中所有数据都是以二进制形式存储，而位运算是直接对二进制数操作，因此处理数据的速度非常快。位运算符的优先级从高到底依次为：按位取反(~)、左移(<<)、右移(>>)、按位与(&)、按位异或(^)、按位或(|)。

### &按位与典型用法
1. 取一个位串信息的某几位，例如x& 0xF0(P2 & 0xF0)，如果x为10010101，即10010101&11110000，结果为1001000，可见高4为被提取出来保持数值不变，低4为全部为0；
2. 让某变量保留某几位，例如x&= 0xF0，&=为复合赋值运算符_逻辑与赋值，相当于x=x & 0xF0。0xF0为设计好的一个常数，该常数种只有需要的为是1，不需要的位则为0。

### |按位或典型用法：
将一个位串信息的某几位置1。例如x|=0xF0(P2 |=0xF0)，|=为复合赋值运算符_逻辑或赋值，相当于x=x|0xF0。如果x为10010101，即10010101|11110000，结果为11110101，高4位全部变成1，低4位保持不变。

### ^按位异或典型用法
求一个位串信息某几位信息的反。例如x^=0xF0，^=为复合赋值运算符_逻辑异或赋值，相当于x=x ^ 0xF0，如果x=10010101，即10010101|11110000，结果为01100101，高4位求得其反，低4位保持不变。

### ~按位取反典型用法
相关于一个已知常数，将一个位串信息的某几位置0。例如x=x & ~0xF0，如果x为10010101，即10010101 & ~11110000=10010101 & 00001111=00000101，可见高4位置0，低4位保持不变。

### <<左移与>>右移配合位运算能够实现多种位串运算有关的复杂计算
* ~(~0<<n)，实现最低n位为1，其余位为0的位串信息，注意优先级计算；
* (x>>(1+p-n)) & ~(~0<<n)，截取变量x自p位开始，右边n位的信息；
* new|= ((old>>row)&1)<<(15-k)，截取old变量第row位，并将该位信息装配到变量new的第15-k位；
* s&= ~(1<<j)，将变量s的第j位置置0，其余位保持不变；
* for(j=0;((1<<j)&s)==0;j++)，设s不等于全0，代码寻找最右边为1的位的序号j。











