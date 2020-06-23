🐞 author: richie bao 📅 Jun 21, 2017
# 装置3_水流线捕捉
结合大一组，课程模型水流模拟，使用Pixy CMUcam5 图像识别传感器捕捉水流路径，在grasshopper中读取数据，自动绘制水流线，分析地形与水流，认知地形设计与地表径流的关系。本次实验将由大一组完成，记录实验结果并分析。

| arduino+Pixy CMUcam5 |. |
|:------------ |: -------------|
|<img src="./imgs/0075.jpg" height="auto" width="auto"  title="digit-x" /> | <img src="./imgs/0076.jpg" height="auto" width="auto"  title="digit-x" />|
| arduino IDE+grasshopper |  grasshopper程序|
|<img src="./imgs/0077.jpg" height="auto" width="auto"  title="digit-x" /> | <img src="./imgs/0078.png" height="auto" width="auto"  title="digit-x" />|

* arduino程序

```c
#include <SPI.h>  
#include <Pixy.h>
Pixy pixy;
int led=2;
void setup() {
 Serial.begin(115200);
 pinMode(led,OUTPUT);
 digitalWrite(led,LOW);
 pixy.init();
}

void loop() {
   uint16_t blocks;
   int j;
   char buf[32]; 
   digitalWrite(led,LOW);
   blocks = pixy.getBlocks();
    if (blocks){
    for(j=0; j<blocks; j++){
      sprintf(buf,"%d,%d,%d,",pixy.blocks[j].signature,pixy.blocks[j].x,pixy.blocks[j].y);
      Serial.print(buf);
      Serial.println();
      digitalWrite(led,HIGH);
    }
   }
}
```

* grasshoper中ghPython程序pixyToDic：

```python
import Rhino,System
from Grasshopper import DataTree
from Grasshopper.Kernel.Data import GH_Path

pixy=[[float(j) for j in i.split(',') if j!=''] for i in pixyList ]

#pixy=[((j,int(i[0])),i) for i in pixy]
print(pixy)
pixyDic=dict()
for i in range(len(pixy)):
    pixyDic[(i,int(pixy[i][0]))]=pixy[i]
print(pixyDic)

def dictotree(dicdata,datatype):
    treedata=DataTree[datatype]()
    for i in dicdata.keys():
        p=i
        ghpath=GH_Path(p)
        treedata.AddRange(dicdata[i],ghpath)
    return treedata

datatype=System.Double
pixyTree=dictotree(pixyDic,datatype)
```