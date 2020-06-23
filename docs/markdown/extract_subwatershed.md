🐞 author: richie bao 📅 Sep 8, 2017
# 提取子流域
通过ArcGis的Hydrology工具计算水文数据，也能够获取集水流域和盆地，但是，如何根据河网级别从集水流域中提取研究分析的区域，即子流域作为研究的边界？只能自行编写程序来完成。根据研究区域特征，以子流域为分析对象的研究边界在规划，尤其以生态为基础的规划设计中具有重要作用。

<img src="./imgs/0081.jpg" height="auto" width="auto"  title="digit-x">

```python
# -*- coding: utf-8 -*-
"""
Created on Sun Sep 17 00:05:54 2017

@author: Richie
"""
import arcpy
from arcpy import env
from arcpy.sa import *
import copy
 
#original_para
worksapce_path=r"D:\xiandata\hydrologyAnalysis"
tsinling_dem=r"D:\xiandata\hydrologyAnalysis\xianDEMMosaic.tif"
roughBoundary=r"D:\xiandata\hydrologyAnalysis\roughBoundary.shp"
research_region_polygon=r"D:\xiandata\hydrologyAnalysis\xa_cityBoundary.shp"
env.overwriteOutput=True
fla_para=300
 
#environment
env.workspace=worksapce_path
env.overwriteOutput=True
#env.extent=extent_region
 
clipDemOutName=worksapce_path+"\clipDem.tif"
clipDem=arcpy.Clip_management(tsinling_dem,"",clipDemOutName,roughBoundary,"","ClippingGeometry")

#Hydrology_basic
fill_dem=arcpy.sa.Fill(clipDem)
flowdirection_dem=arcpy.sa.FlowDirection(fill_dem)
flowaccumulation_dem=arcpy.sa.FlowAccumulation(flowdirection_dem)
flowaccumulation_dem_copy=arcpy.CopyRaster_management(flowaccumulation_dem,"flowaccumulation_dem_copy.tif") 
overland_flow=arcpy.sa.Con(Raster("flowaccumulation_dem_copy.tif")>float(fla_para),1) 
stream_link=arcpy.sa.StreamLink(overland_flow,flowdirection_dem)
outStreamOrder=arcpy.sa.StreamOrder(stream_link,flowdirection_dem,"STRAHLER")

outStreamOrderNum=arcpy.GetRasterProperties_management(outStreamOrder,"UNIQUEVALUECOUNT").getOutput(0)
orderThreshold=5
attExtract= ExtractByAttributes(outStreamOrder, "Value >="+str(orderThreshold)+" AND Value<="+str(outStreamOrderNum)) 

streamToFeatureName=worksapce_path+r"\streamToFeature.shp"
streamToFeature=arcpy.sa.StreamToFeature(attExtract,flowdirection_dem,streamToFeatureName)
endpointsName=worksapce_path+r"\endpoints.shp"
endpoints=arcpy.FeatureVerticesToPoints_management(streamToFeature,endpointsName,"END")
snapPP=SnapPourPoint(endpoints,flowaccumulation_dem_copy,0,"FID")
subWatershed=Watershed(flowdirection_dem, snapPP, "VALUE")

subWatershed_polygon=worksapce_path+"\watershed_polygon.shp"
arcpy.RasterToPolygon_conversion(subWatershed,subWatershed_polygon,"SIMPLIFY","VALUE")
clip_watershed_p=arcpy.Clip_analysis(subWatershed_polygon,research_region_polygon)
#flowlength_d=FlowLength(flowdirection_dem,"UPSTREAM")

def extract_subwatershed(clip_watershed_p,watershed_polygon):
    ID_lst=[]
    cursor=arcpy.da.SearchCursor(clip_watershed_p,'ID')
    for row in cursor:
      ID_lst.append(row[0]) 
    del cursor,row
 
    p_select=[]
    cursor=arcpy.da.SearchCursor(watershed_polygon,['ID','SHAPE@'])
    for row in cursor:
        if row[0] in ID_lst:
            p_select.append(row[1])
    del cursor,row
 
    p_s=arcpy.CreateFeatureclass_management(worksapce_path,'p_s',"POLYGON","","","",tsinling_dem)
    arcpy.AddField_management(p_s,'ID_name',"FLOAT")
 
    cursor=arcpy.da.InsertCursor("p_s",['SHAPE@','ID_name'])
    for i in range(len(p_select)):
        cursor.insertRow([p_select[i],ID_lst[i]])
    del cursor
    return p_s
p_s=extract_subwatershed(clip_watershed_p,subWatershed_polygon)

contourInterval=200
baseContour=0
outContours=worksapce_path+"\outContours.shp"
Contour(clipDem,outContours,contourInterval,baseContour)

footElevation=600
footContour=worksapce_path+"\\footContours.shp"
arcpy.Select_analysis(outContours, footContour, "CONTOUR="+ str(footElevation))
```