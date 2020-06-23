> 🐞 author: richie bao 📅 Jun 10, 2018
# 10_C语言(arduino)串讲，考前必备

```c
#include <stdio.h>

int days[12]={31,28,31,30,31,30,31,31,30,31,30,31};
int _123=7;
#define N 10
int func(int *ptr);
void setup() {
  Serial.begin(9600);
}

void loop() {
  /*
  Serial.println(6+3%5);
  int x=9,y=7;
  x=y;y=x;
  Serial.println(x);
  Serial.println(y);
  int a[7]={1,2,3,4,5,6},*p=a;
  Serial.println(*(p+2));
  */
 
  /*
  Serial.println(0b10010101&0b11110000,BIN);

  int c=7,d=9;
  //Serial.println(2(c+d));
  Serial.println(c+(d=6));
 // Serial.println(c+d=6);
 //Serial.println((c+d)=9);
 // int pv=0219;
 // int pv=IE-5;
//  Serial.println(pv);
  char name[15]="digit-x.org",*ptr=name;
  Serial.println(name[7]);
  Serial.println(*(ptr+7));
  Serial.println(*(&name));
  Serial.println(ptr[7]);

  Serial.println(1/1+1/2+1/4);

  float a=5.6;  int b=2; 
  Serial.println(a++);
  Serial.println(++a);
  Serial.println(a||b);
  Serial.println(a&&b);
  //Serial.println(b=a%2);
  
  int counter=0; for(int i=0;i<6;i++){if (i%2==0){counter++;}} Serial.println(counter);

  int M=20;
 // N=M;
 Serial.println(N);
 int arrayA[N];
 int arrayB[M];
 Serial.println(N+1);
 
 int e=0,f=0;
// if (e==0)f++;else e++;f++;
 if (e<0)e++;else f++;
 Serial.println(f);

 int g[2]={1,100},*p=g;*p++;Serial.println(*p);

  int arrayMin[2]={13,65};  Serial.println(func(arrayMin));

  int h=1,j=2; while(h<3) Serial.println(++j),h++;

  int count=0,s=0; while(true){if(count<6){count++;s+=count;}else{ break;}}Serial.println(s);

  int k[3][4]={{1,2},{3,4},5};
  Serial.println(k[1][2]);
  Serial.println(k[0][1]+k[1][2]);
  int readSD=8;
  switch(readSD){
    case 1:Serial.println("OK");
    case 8:Serial.println("switch传入参数为整形数据");
  }
*/

/*
   char str[20]="digit-x.org",*strPtr=str;
   Serial.println(str[0]);
   Serial.println(strlen(str));
   Serial.println(strPtr);
   Serial.println(str);

  int xa=9,xb=2;//9的二进制位00001001,2的二进制为00000010
  Serial.print(xa/xb);Serial.print(" ");Serial.print(xa%xb);Serial.print(" ");Serial.print(xa>>xb);Serial.print(" ");Serial.print(xa&xb);Serial.print(" ");Serial.print((xa<0)&&(xb>0));Serial.print(" ");Serial.print(xa||xb);Serial.print(" ");Serial.print(xa>xb?xa++:++xb);
*/

   int a,b,c,d;
   c=(a=(2,3));
   d=(b= 2,3 );
   Serial.print(a); Serial.print(",");
   Serial.print(b); Serial.print(",");
   Serial.print(c); Serial.print(",");
   Serial.println(d); 

  Serial.flush();
  exit(0);
}

int func(int *ptr){
  Serial.println(*ptr);
  return min(*ptr,*(ptr+1));
}

```

```c
int Fun(int x);

void setup() {
  Serial.begin(9600);
}

void loop() {
  int  x = 10, y = 0, k;
  for(k=0; k<2; k++){
   y = Fun(x);
   Serial.print("y =");
   Serial.println(y);
   } 
  Serial.flush();
  exit(0);   
}

int Fun(int x){
  static int y = 0;
  y += x;
  return y;
}
    
void setup() {
  Serial.begin(9600);
  int i=0;
  while(1){
    Serial.print("*");
    i++;
    if(i<3) break;
  }
}

void loop() {

}

void setup() {
  Serial.begin(9600);
  int x=2,y=5,z=8,r;
  r=runc(runc(x,y),z);
  Serial.print(r);
}

void loop() {

}

int runc(int a,int b){
  return(a+b);
}

void setup() {
  Serial.begin(9600);
  char a[] = "programming" , b[] = "language" ;
  char  *p1, *p2;
  int  i;
  p1 = a;
  p2 = b;
  for (i=0; i<7; i++){
     if (*(p1+i) == *(p2+i)) Serial.print(*(p1+i));
  }
}

void loop() {

}

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
}fruit={"apple",001,33,{10.2,60.2},"London"};

void setup() {
  Serial.begin(9600);
  Serial.print(fruit.Pprice.abroad);
}

void loop() {

}

struct da_price{  
  float domestic;
  float abroad;
}; 
struct form{  //定义父结构
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
  struct form *ptr; 
  ptr=fruit;  
  Serial.print((ptr+1)->Pnum);
}
void loop() {

}

void setup() {
  Serial.begin(9600);
  int n=4;
  while(n--){
    Serial.print(--n);Serial.print(",");
  }
}

void loop() {

}

void setup() {
  Serial.begin(9600);
  int x=10,y=10,i;
  for(i=0;x>8;y=++i){
    Serial.print(x--);
    Serial.print(",");
    Serial.print(y);
    Serial.print(",");
  }
}

void loop() {

}

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
  fruit.Pcode=101; 
  Serial.print("name=");Serial.println(fruit.Pname);
  Serial.print("Pcode=");Serial.println(fruit.Pcode);
}
 
void loop() {

}

void setup() {
  Serial.begin(9600);
  int i,j;
  for(j=10;j<11;j++){
    for(i=9;i<j;i++){
      if(!(j%i)) break;
      if(i>=j-1) Serial.print(j);
    }
  }
}

void loop() {

}

void setup() {
  Serial.begin(9600);
  char str[100]="digit-x.org",t;
  int i,j,len;
  Serial.println(str);
  len=strlen(str);
  for(i=0,j=len-1;i<len/2;i++,j--){
    t=str[i];
    str[i]=str[j];
    str[j]=t;
  }
  Serial.println(str);
}

void loop() {

}

int a[10]={12,23,34,45,56,67,78,89,90,91},key,m,h;
void setup() {
  Serial.begin(9600);
  key=89; //查找值为23的下标（索引）
  h=9;
  m=(1+h)/2;
  while(m>=0 && m<=9){    
    if(key<a[m]) --m;
    if(key>a[m]) ++m;
    if(key==a[m]) break;
  }
  if(key==a[m]){
    Serial.println(m);
  } else{
    Serial.println("Not found"); 
  }
}

void loop() {

}

#define n 20
int intData[n]={23,45,22,3,4,-5,-7,3,-88,999,45,9,-56,-3,-5,67,45,-66,35,-93};

void setup() {
  Serial.begin(9600);
  int i=0,j=0;
  for(int k=0;k<n;k++){
    if(intData[k]>0) i++;
    if(intData[k]<0) j++;      
  }
  Serial.print(i);
  Serial.print(",");
  Serial.println(j);
}

void loop() {

}

#define n 20
float scoreData[n]={23.5,45,22,39.9,47,59.0,76,39,88,99,45,91.5,56,33,54,67.8,45,66,35,93};
void setup() {
  Serial.begin(9600);
  float x,max=0,min=100;
  for(int i=0;i<n;i++){
    float x=scoreData[i];
    if(x>max) max=x;
    if(x<min) min=x;
  }
  Serial.print(max);
  Serial.print(",");
  Serial.println(min);
}

void loop() {

}

void setup() {
  Serial.begin(9600);
  int i,j,k,n=0;
  for(i=1;i<=19;i++)for(j=1;j<31;j++){
    k=100-i-j;
    if(5*i+3*j+0.5*k==100){
      n++;
      Serial.print(n);
      Serial.print(",");
      Serial.print(i);
      Serial.print(",");
      Serial.print(j);
      Serial.print(",");
      Serial.print(k);
      Serial.println(".");
    }
  }
}

void loop() {

}

void setup() {
  Serial.begin(9600);
  float x,y;
  x=3.0;
  if(x<0) y=1;
  else if(x<=5) y=2*x;
  else y=sqrt(x);
  Serial.print(x);
  Serial.print(",");
  Serial.println(y);
}

void loop() {

}
```

```c
#include <stdio.h>

void main() {

    //printf("int_len=%d\n", sizeof(0));
    //printf("float length=%d\n", sizeof(0.0));

    /*/
    int x = 99, y = 88;
    x = y;y = x;
    printf("x=%d,y=%d\n", x, y);
    */

    //int array[3] = { 0,1,2,3 };

    /*
    int array[9] = { 1,2,3,4,5,6,7,8,9 },*ptr=array;
    printf("*(ptr+3)=%d", *(ptr + 3));
    */

    /*
    #define len 12  //定义为常量
    int array[len] = { 1,2,3,4 };
    printf("array[1]=%d", array[1]);
    */

    //int _000 = 100;    printf("_000=%d", _000);

    //printf("10101010&01010111=%x\n", 0b10101010 & 0b01010111);

    //int a = 56, b = 34;    printf("a+(b=12)=%d\n", a + (b = 12));

    //printf("0xAF+0b00001111+323=%x", 0xAF + 0b00001111 + 323);

    /*
    char dd[15] = "digit-x.org", *ptr = dd;
    printf("dd[5]=%c,*(dd+5)=%c,ptr[5]=%c", dd[5], *(dd + 5), ptr[5]);
    */

    //printf("1/1+1/2+1/4=%d\n", 1 / 1 + 1 / 2 + 1 / 4);
    //printf("1/1+1/2+1/4=%.2f\n", 1 / 1 + 1 / 2 + 1 / 4);

    /*
    float a = 1.2;
    int b = 2;
    printf("a++=%.2f,a||b=%d,a&&b=%d\n", a++, a || b, a&&b);
    printf("a++=%.2f\n", a++);
    */
    /*
    int counter = 0;
    for (int i = 0;i < 9;i++) { if (i % 3) counter++; }
    printf("counter=%d\n", counter);
    */

    /*
    #define n 12 //n被定义为常量，不可改变,即不可以再赋值
    int m = 12;
    int array_a[n];
    int array_b[m]; //m不是常量，不可以传入，因此此句错误
    */

    /*
    int a = 0, b = 0;
    if (a < 0) b++;
    if (a < 0) a++;else b++;
    if (a < 0)if (b == 0) b++;
    if (a == 0) b++;else a++;b++;
    printf("a=%d,b=%d\n", a, b);
    */

    //int array[3 ]= { 12,56 ,33}, *ptr = array;*ptr++;printf("*p=%d", *ptr);

    /*
    int getMinValue(int *ptr, int length);
    int array[6] = { 33,2,33,56,4,56 },*ptr=array;
    int minValue = getMinValue(ptr, 6);
    printf("minValue=%d\n", minValue);
    */

    /*
    int a = 3, b = 8;
    while (b < 15) {
        printf("++a=%d\n", ++a);
        b++;
    }
    */

    /*
    int count = 0, x = 7;
    while (1) {
        if (count < 8) { count++;x += count; }
        else break;
    }
    printf("x=%d\n", x);
    */

    /*
    int array[5][3] = { {4,5},{33,33},{56565,3},{6,-45},{-333,1} };
    printf("array[2][1]+array[1][0]=%d\n", array[2][1] + array[1][0]);
    */

    /*
    printf("please inputting your value:\n");
    int x;
    scanf_s("%d", &x);
    switch (x) {  //条件表达式要求为整数类型，int,char enum,bool
        case 11:printf("your value is %d", x);break;
        case 2:printf("your value is %d", x);break;
        default:printf("why did you put this kind of value???=%d",x);
        }
        */

    /*
    char website[30] = "digit-x.org", *ptr = website;
    printf("website[1]=%c,strlen(website)=%d,ptr=%s", website[1], strlen(website), ptr);
    */

    /*
    int a = 9, b = 2;//9的二进制位00001001,2的二进制为00000010
    printf("a/b=%d,a%%b=%d,b&a=%x,(b<0)&&(a>0)=%d,a<b?a++:b++=%d,a>>b=%x\n", a / b, a%b,  b&a, (b < 0) && (a > 0), a < b ? a++ : b++, a >> b );
    //自行将上述语句拆分，分别打印各个计算值
    */
    
    /*
    int x = 2, y = 3, i;
    for (i = 0;i < 3;i++) {
        y = func_a(x);
        printf("y=%d\n", y);
    }
    */

    /*
    int i = 0;
    while (1) {
        printf("%s\n", "$");
        i++;
        if (i < 6) break;
    }
    */

    /*
    int x = 3, y = 9, result;
    result = func_b(x, y);
    printf("result=%d", result);
    */

    /*
    char str_a[] = "programming", str_b[] = "language",*ptr_a=str_a,*ptr_b=str_b;
    for (int i = 0;i < 7;i++) {
        if (*(ptr_a + i) == *(ptr_b + i)) printf("%c", *(str_a + i));
    }
    */

    /*
    struct price {
        float domestic;
        float abroad;
    };
    struct form {
        char *Pname;
        int Pcode;
        int Pnum;
        struct price Pprice;
        char *Pexporter;
    }fruit = { "apple",001,33,{10.2,60.2},"London" };
    printf("fruit.Pprice.abroad=%.2f\n", fruit.Pprice.abroad);
    */

    /*
    struct da_price {
        float domestic;
        float abroad;
    };
    struct form {
        char *Pname;
        int Pcode;
        int Pnum;
        struct da_price Price;
        char *Pexporter;
    }fruit[3] = { {"apple",001,100,{10.2,60.2},"London"},{"orange",002,200,{6.8,68.8},"Singapore"}, {"banana",003,300,{9.6,66.6},"Mexcico"} };
    struct form *ptr;
    ptr = fruit;
    printf("(ptr+2)->Pexporter=%s\n", (ptr + 2)->Pexporter);
    */

    /*
    int k = 8; //注意如何k=9，结果会如何，请自行确认
    while (k--) {
        printf("--k=%d\n", --k);
    }
    */

    /*
    int x = 31, y = 11;
    for (int i = 1;x > 24;y = ++i) {
        printf("%d,", x--);
        printf("%d,", y);
    }
    */

    /*
    union form {
        char *Pname;
        int Pcode;
        int Pnum;
        union {
            float domestic;
            float abroad;
        }Price;
        char *Pexporter;
    };
    union form fruit;
    fruit.Pname = "apple";
    fruit.Pcode = 101;
    printf("Pcode=%d\n,", fruit.Pcode);
    printf("name = %s\n", fruit.Pname);
    */

    /*
    for (int j = 10;j < 12;j++) {
        for (int i = 8;i < j;i++) {
            if (!(j%i))break;
            if (i >= j - 1) printf("%d,", j);
        }
    }
    */

    //关于数组的一些统计操作，例如求数组的最大，最小值，求大于或小于某个数的值或其和等计算，可自行编写或网络搜索学习
    //...


    /*
    //关于结构的一些计算要熟悉
    struct form {
          char *Pname;
          int Pcode;
          int Pnum;
          float Price;
          char *Pexporter;    
    };
    struct form fruit[3] = {
        {"apple",001,100,10.2,"London"},
        {"orange",002,200,9.8,"Singapore"},
        {"banana",003,300,6.6,"Mexcico"}
         };
    float sum = 0;
    for (int i = 0;i < 3;i++) {
        if (fruit[i].Price < 10) sum += fruit[i].Price*fruit[i].Pnum;
    }
    printf("sum=%.2f\n", sum);
    */   

}


int getMinValue(int *ptr,int length) {
    int temp;
    for (int i = 0;i < length;i++) {
        if (*(ptr + 1) < *ptr) temp = *(ptr + 1);else temp = *ptr;
    }
    return temp;
}

int func_a(int x) {
    static int y = 9;
    y += x;
    return y;
}

int func_b(int x, int y) {
    return x + y;
}
```