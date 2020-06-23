> 🐞 author: richie bao 📅 Nov 1, 2017
# python测验_20171101_根据提供的数据采集程序下载自选城市的poi数据，基于python编写描述性统计(包括评分和价格等相关性分析)及图表代码
* 题目：根据提供的数据采集程序下载自选城市的美食/旅游景点等数据，基于python编写描述性统计(包括评分和价格等相关性分析)及图表代码

代码实现内容包括：描述性统计(基于numpy库)+图表(基于matplotlib库)

| 描述性统计 |. |.|图表/示例 |
|:------------ |: -------------|: -------------|: -------------|
|1 | 中心位置|均值Mean、中位数Median、众数Mode|<img src="./imgs/0107.png" height="auto" width="auto"  title="digit-x" />|
| 2 |发散程度|极差PTP、平均差、方差Variance、标准差STD、变异系数CV| |
| 3| 偏差程度|Z-Score/z-分数| |
|4| 相关程度|协方差、相关系数||

* 百度poi数据采集代码

```python
# -*- coding: utf-8 -*-
"""
Created on Tue Sep 12 15:58:43 2017

@author: RichieBall
"""
#百度地图POI数据采集, http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-placeapi 
#POI,  http://lbsyun.baidu.com/index.php?title=lbscloud/poitags 
from urllib.request import urlopen
from urllib import parse
import json  #json数据格式
import csv  #csv数据格式
import conversionofCoordi as cc  #百度坐标转换为GPS84坐标系
import time

#leftBottom=[108.776852,34.186027]   #百度地图坐标拾取系统  http://api.map.baidu.com/lbsapi/getpoint/index.html 
#rightTop=[109.129275,34.382171]

leftBottom=[108.789929,34.177257]  
rightTop=[109.137753,34.394395]

partition=4
urlRoot=' http://api.map.baidu.com/place/v2/search? ' #行政区划区域检索: http://api.map.baidu.com/place/v2/search?query=ATM 机&tag=银行&region=北京&output=json&ak=您的ak //GET请求
#urlRootDetail=' http://api.map.baidu.com/place/v2/detail? ' #地点详情检索服务: http://api.map.baidu.com/place/v2/detail?uid=5a8fb739999a70a54207c130& ;output=json&scope=2&ak=您的密钥 //GET请求
poiName='美食'
AK='myAK' #百度地图API，注册后获取。 http://lbsyun.baidu.com/ 
fileName="baiduMapPoiSpotDetail.csv"
fileJsonName="baiduMapPoiSpotDetail.json"
csvFile=open(fileName,'w')
jsonFile=open(fileJsonName,'w')

'''数据采集，并分别存储为csv和json两种数据格式'''
def scrapingData(leftBottom,rightTop,partition,urlRoot,poiName,AK,csvFile):
    xDis=(rightTop[0]-leftBottom[0])/partition  #分片范围，确保数据下载量
    yDis=(rightTop[1]-leftBottom[1])/partition
    jsonDic=[]  #提取采集数据，用于json数据格式的文件存储
    writer=csv.writer(csvFile)    #csv写入数据
    num=0  
    for i in range(partition):  
        for j in range(partition):
            leftBottomCoordi=[leftBottom[0]+i*xDis,leftBottom[1]+j*yDis]  #定义左下角坐标
            #rightTopCoordi=[rightTop[0]+i*xDis,rightTop[1]+j*yDis]
            rightTopCoordi=[leftBottom[0]+(i+1)*xDis,leftBottom[1]+(j+1)*yDis]  #定义右上角坐标
            #print(leftBottomCoordi,rightTopCoordi)
            for p in range(20):   #逐次采集 
                query={  #参数设置可以查看百度数据采集API说明  http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-placeapi 
                   'query':poiName,
                   'page_size':'20', 
                   'page_num':str(p),
                   'scope':2,  #此参数设置为2时，即采集detail详细信息，设置为1是仅采集基本信息
                   'bounds':str(leftBottomCoordi[1]) + ',' + str(leftBottomCoordi[0]) + ','+str(rightTopCoordi[1]) + ',' + str(rightTopCoordi[0]),
                   'output':'json',  #设置采集的格式
                   'ak':AK,                   
                }
                #url=urlRoot+'query=' + parse.urlencode(query) + '&page_size=20&page_num=' + str(p) + '&scope=1&bounds=' + str(leftBottomCoordi[1]) + ',' + str(leftBottomCoordi[0]) + ','+str(rightTopCoordi[1]) + ',' + str(rightTopCoordi[0]) + '&output=json&ak=' + AK;     
                url=urlRoot+parse.urlencode(query)
                #print(url)
                #time.sleep(0.1)
                data=urlopen(url)
#                print(data)
                responseJson=json.loads(data.read())  #读取采集的数据，该数据为json格式，因此调用json库方法              
                #print(responseJson.get("message"))  #查看采集信息是否成功
                if responseJson.get("message")=='ok':
                    results=responseJson.get("results")     
                    #print(results)
                    csvRow=[]
                    for row in range(len(results)):  
                        #print(results[row])
                        subData=results[row]
                        orgiCoordi=[subData.get('location').get('lng'),subData.get('location').get('lat')]
                        converCoordiGCJ=cc.bd09togcj02(orgiCoordi[0], orgiCoordi[1])  #转换百度坐标系为GPS84
                        converCoordiGPS84=cc.gcj02towgs84(converCoordiGCJ[0],converCoordiGCJ[1])
                        #csvRow=[subData.get('name'),subData.get('location').get('lat'),subData.get('location').get('lng'),subData.get('address')]
                        csvRow=[subData.get('name'),converCoordiGPS84[0],converCoordiGPS84[1],subData.get('uid'),subData]
                        #print(csvRow)
                        writer.writerow(csvRow)  #写入csv格式
                        writer.writerow([subData])
                        jsonDic.append(subData)
                        #jsonData=json.dumps([subData])
                        #jsonFile.write(jsonData)                        
            num+=1
            print("第"+str(num)+"个区域写入csv/json文件")
    json.dump(jsonDic,jsonFile)  #写入json格式
    jsonFile.write('\n') 

try:
    scrapingData(leftBottom,rightTop,partition,urlRoot,poiName,AK,csvFile)
finally:
    csvFile.close()
    jsonFile.close()
```

* conversionofCoordi代码

```python
# -*- coding: utf-8 -*-
"""
Created on Tue Sep 12 19:56:04 2017

@author: 未知，来源于网络
"""

# -*- coding: utf-8 -*-
import json
import requests
import math

key = 'your key here'  # 这里填写你的百度开放平台的key
x_pi = 3.14159265358979324 * 3000.0 / 180.0
pi = 3.1415926535897932384626  # π
a = 6378245.0  # 长半轴
ee = 0.00669342162296594323  # 扁率


def geocode(address):
    """
    利用百度geocoding服务解析地址获取位置坐标
    :param address:需要解析的地址
    :return:
    """
    geocoding = {'s': 'rsv3',
                 'key': key,
                 'city': '全国',
                 'address': address}
    res = requests.get(
        " http://restapi.amap.com/v3/geocode/geo ", params=geocoding)
    if res.status_code == 200:
        json = res.json()
        status = json.get('status')
        count = json.get('count')
        if status == '1' and int(count) >= 1:
            geocodes = json.get('geocodes')[0]
            lng = float(geocodes.get('location').split(',')[0])
            lat = float(geocodes.get('location').split(',')[1])
            return [lng, lat]
        else:
            return None
    else:
        return None


def gcj02tobd09(lng, lat):
    """
    火星坐标系(GCJ-02)转百度坐标系(BD-09)
    谷歌、高德——>百度
    :param lng:火星坐标经度
    :param lat:火星坐标纬度
    :return:
    """
    z = math.sqrt(lng * lng + lat * lat) + 0.00002 * math.sin(lat * x_pi)
    theta = math.atan2(lat, lng) + 0.000003 * math.cos(lng * x_pi)
    bd_lng = z * math.cos(theta) + 0.0065
    bd_lat = z * math.sin(theta) + 0.006
    return [bd_lng, bd_lat]


def bd09togcj02(bd_lon, bd_lat):
    """
    百度坐标系(BD-09)转火星坐标系(GCJ-02)
    百度——>谷歌、高德
    :param bd_lat:百度坐标纬度
    :param bd_lon:百度坐标经度
    :return:转换后的坐标列表形式
    """
    x = bd_lon - 0.0065
    y = bd_lat - 0.006
    z = math.sqrt(x * x + y * y) - 0.00002 * math.sin(y * x_pi)
    theta = math.atan2(y, x) - 0.000003 * math.cos(x * x_pi)
    gg_lng = z * math.cos(theta)
    gg_lat = z * math.sin(theta)
    return [gg_lng, gg_lat]


def wgs84togcj02(lng, lat):
    """
    WGS84转GCJ02(火星坐标系)
    :param lng:WGS84坐标系的经度
    :param lat:WGS84坐标系的纬度
    :return:
    """
    if out_of_china(lng, lat):  # 判断是否在国内
        return lng, lat
    dlat = transformlat(lng - 105.0, lat - 35.0)
    dlng = transformlng(lng - 105.0, lat - 35.0)
    radlat = lat / 180.0 * pi
    magic = math.sin(radlat)
    magic = 1 - ee * magic * magic
    sqrtmagic = math.sqrt(magic)
    dlat = (dlat * 180.0) / ((a * (1 - ee)) / (magic * sqrtmagic) * pi)
    dlng = (dlng * 180.0) / (a / sqrtmagic * math.cos(radlat) * pi)
    mglat = lat + dlat
    mglng = lng + dlng
    return [mglng, mglat]


def gcj02towgs84(lng, lat):
    """
    GCJ02(火星坐标系)转GPS84
    :param lng:火星坐标系的经度
    :param lat:火星坐标系纬度
    :return:
    """
    if out_of_china(lng, lat):
        return lng, lat
    dlat = transformlat(lng - 105.0, lat - 35.0)
    dlng = transformlng(lng - 105.0, lat - 35.0)
    radlat = lat / 180.0 * pi
    magic = math.sin(radlat)
    magic = 1 - ee * magic * magic
    sqrtmagic = math.sqrt(magic)
    dlat = (dlat * 180.0) / ((a * (1 - ee)) / (magic * sqrtmagic) * pi)
    dlng = (dlng * 180.0) / (a / sqrtmagic * math.cos(radlat) * pi)
    mglat = lat + dlat
    mglng = lng + dlng
    return [lng * 2 - mglng, lat * 2 - mglat]


def transformlat(lng, lat):
    ret = -100.0 + 2.0 * lng + 3.0 * lat + 0.2 * lat * lat + \
        0.1 * lng * lat + 0.2 * math.sqrt(math.fabs(lng))
    ret += (20.0 * math.sin(6.0 * lng * pi) + 20.0 *
            math.sin(2.0 * lng * pi)) * 2.0 / 3.0
    ret += (20.0 * math.sin(lat * pi) + 40.0 *
            math.sin(lat / 3.0 * pi)) * 2.0 / 3.0
    ret += (160.0 * math.sin(lat / 12.0 * pi) + 320 *
            math.sin(lat * pi / 30.0)) * 2.0 / 3.0
    return ret


def transformlng(lng, lat):
    ret = 300.0 + lng + 2.0 * lat + 0.1 * lng * lng + \
        0.1 * lng * lat + 0.1 * math.sqrt(math.fabs(lng))
    ret += (20.0 * math.sin(6.0 * lng * pi) + 20.0 *
            math.sin(2.0 * lng * pi)) * 2.0 / 3.0
    ret += (20.0 * math.sin(lng * pi) + 40.0 *
            math.sin(lng / 3.0 * pi)) * 2.0 / 3.0
    ret += (150.0 * math.sin(lng / 12.0 * pi) + 300.0 *
            math.sin(lng / 30.0 * pi)) * 2.0 / 3.0
    return ret


def out_of_china(lng, lat):
    """
    判断是否在国内，不在国内不做偏移
    :param lng:
    :param lat:
    :return:
    """
    if lng < 72.004 or lng > 137.8347:
        return True
    if lat < 0.8293 or lat > 55.8271:
        return True
    return False

'''
if __name__ == '__main__':
    lng = 128.543
    lat = 37.065
    result1 = gcj02tobd09(lng, lat)
    result2 = bd09togcj02(lng, lat)
    result3 = wgs84togcj02(lng, lat)
    result4 = gcj02towgs84(lng, lat)
    result5 = geocode('北京市朝阳区朝阳公园')
    print result1, result2, result3, result4, result5
'''
```

poi采集示例数据，西安绕城内美食数据，含detail_info，[json与csv数据格式下载](https://pan.baidu.com/s/1sl0WzJR)