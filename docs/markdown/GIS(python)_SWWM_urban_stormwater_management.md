🐞 author: richie bao 📅 Sep 8, 2017
# GIS(python)+SWWM城市雨洪管理
用python编写SWWM与ArcGIS平台的数据交互文件，通过GIS计算分析区域下流域范围，以及坡度、坡向、高程、不透水面积及地表径流等数据，生成SWWM计算文件，计算开发前后径流系数，并将计算结果调回GIS平台可视化。
[视频_清晰版下载](https://pan.baidu.com/s/1o8aBnMa)

```pdf
./pdf/swwmgis.pdf
```

```python
import arcpy
from arcpy import env
from arcpy.sa import *
import time
 
#original_para
worksapce_path="D:\SWMM_cal"
original_dem="D:\SWMM_cal\gdem_m_P.tif"
extent_region="D:\SWMM_cal\extent_region.shp"
fla_para=500
research_region_polygon="D:\SWMM_cal\\research_region_polygon.shp"
imperv="D:\SWMM_cal\imperv.tif"
Gage_1="D:\SWMM_cal\Gage1.shp"
 
#environment
env.workspace=worksapce_path
env.overwriteOutput=True
env.extent=extent_region
 
#Hydrology_basic
fill_dem=arcpy.sa.Fill(original_dem)
flowdirection_dem=arcpy.sa.FlowDirection(fill_dem)
flowaccumulation_dem=arcpy.sa.FlowAccumulation(flowdirection_dem)
flowaccumulation_dem_copy=arcpy.CopyRaster_management(flowaccumulation_dem,"flowaccumulation_dem_copy.tif") #?
overland_flow=arcpy.sa.Con(Raster("flowaccumulation_dem_copy.tif")>float(fla_para),1) #how to solve?
stream_link=arcpy.sa.StreamLink(overland_flow,flowdirection_dem)
watershed_r=arcpy.sa.Watershed(flowdirection_dem,stream_link)
watershed_polygon=worksapce_path+"\watershed_polygon.shp"
arcpy.RasterToPolygon_conversion(watershed_r,watershed_polygon,"SIMPLIFY","VALUE")
clip_watershed_p=arcpy.Clip_analysis(watershed_polygon,research_region_polygon)
flowlength_d=FlowLength(flowdirection_dem,"UPSTREAM")
 
#extract subwatershed
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
 
    p_s=arcpy.CreateFeatureclass_management(worksapce_path,'p_s',"POLYGON")
    arcpy.AddField_management(p_s,'ID_name',"FLOAT")
 
    cursor=arcpy.da.InsertCursor("p_s",['SHAPE@','ID_name'])
    for i in range(len(p_select)):
        cursor.insertRow([p_select[i],ID_lst[i]])
    del cursor
    return p_s
p_s=extract_subwatershed(clip_watershed_p,watershed_polygon)
 
#SWMM_para
Area_lst=[row[0] for row in arcpy.da.SearchCursor(p_s,['SHAPE@AREA'])]
 
zonal_flowlength=ZonalStatisticsAsTable(p_s,"FID",flowlength_d,"zonal_flowlength","NODATA","MEAN")
zonal_flowlength_lst=[row[0] for row in arcpy.da.SearchCursor(zonal_flowlength,["MEAN"])]
 
slope_d=arcpy.sa.Slope(original_dem)
zonal_slope=arcpy.sa.ZonalStatisticsAsTable(p_s,"FID",slope_d,"zonal_slope","NODATA","mean")
slope_lst=[row[0] for row in arcpy.da.SearchCursor(zonal_slope,['MEAN'])]
 
zonal_imperv=ZonalStatisticsAsTable(p_s,"FID",imperv,"imperv_t","NODATA","SUM")
imperv_lst=[row[0] for row in arcpy.da.SearchCursor(zonal_imperv,['SUM'])]
 
stream_link_extract=ExtractByMask(stream_link,p_s)
stream_link_feature=StreamToFeature(stream_link_extract,flowdirection_dem,"stream_link_feature")
outlet=arcpy.FeatureVerticesToPoints_management(stream_link_feature,"outlet","END")
stream_mid=arcpy.FeatureVerticesToPoints_management(stream_link_feature,"stream_mid","MID")
sj_m=arcpy.SpatialJoin_analysis(p_s,stream_mid,"sj_m","JOIN_ONE_TO_ONE","KEEP_ALL","","COMPLETELY_CONTAINS")
outlet_xy=[(row[1],row[0]) for row in arcpy.da.SearchCursor(outlet,["SHAPE@XY","ARCID"])]
outlet_xy_dic=dict(outlet_xy)
ARCID_lst=[(row[0],row[1]) for row in arcpy.da.SearchCursor(sj_m,["ARCID","ID_name"]) if row[0]!=0]
ARCID_lst_dic=dict(ARCID_lst)
 
outlet_ele=ExtractValuesToPoints(outlet,fill_dem,"outlet_ele")
outlet_ele_lst=[(row[1],row[0]) for row in arcpy.da.SearchCursor(outlet_ele,["RASTERVALU","ARCID"])]
outlet_ele_dic=dict(outlet_ele_lst)
 
#remove invalid value
def remove_invalid_v(outlet_xy_dic,ARCID_lst_dic):
    ARCID_xy={}
    ARCID_ele={}
    invalid_ID_name_lst=[]
    for key in outlet_xy_dic.keys():
        if key in ARCID_lst_dic.keys():
            ARCID_xy[ARCID_lst_dic[key]]=outlet_xy_dic[key]
            ARCID_ele[ARCID_lst_dic[key]]=outlet_ele_dic[key]
    ID_name=[row[0] for row in arcpy.da.SearchCursor('p_s',['ID_name'])]
    ID_name_xy=[]
    ID_name_ele=[]
    for i in ID_name:
        if i in ARCID_xy.keys():
            ID_name_xy.append(ARCID_xy[i])
            ID_name_ele.append(ARCID_ele[i])
        else:
            invalid_ID_name_lst.append(i)
            ID_name_xy.append((0,0))
            ID_name_ele.append(0)
    return invalid_ID_name_lst,ID_name_xy,ID_name_ele
invalid_ID_name_lst,ID_name_xy,ID_name_ele=remove_invalid_v(outlet_xy_dic,ARCID_lst_dic)
 
invalid_FID_lst=[row[0] for row in arcpy.da.SearchCursor("p_s",["FID","ID_name"]) if row[1] in invalid_ID_name_lst]
 
#add fields and value
arcpy.AddField_management(p_s,"Area","FLOAT")
arcpy.AddField_management(p_s,"Slope","FLOAT")
arcpy.AddField_management(p_s,"Imperv","FLOAT")
arcpy.AddField_management(p_s,"Jxcoord","FLOAT")
arcpy.AddField_management(p_s,"Jycoord","FLOAT")
arcpy.AddField_management(p_s,"slopWidth","FLOAT")
arcpy.AddField_management(p_s,"OutletEle","FLOAT")
 
cursor=arcpy.da.UpdateCursor(p_s,['Area',"Slope","Imperv","ID","Jxcoord","Jycoord","slopWidth","OutletEle"])
n=0
for row in cursor:
    row[0]=Area_lst[n]
    row[1]=slope_lst[n]
    row[2]=imperv_lst[n]*900*100/Area_lst[n]
    row[3]=n
    row[4]=ID_name_xy[n][0]
    row[5]=ID_name_xy[n][1]
    if zonal_flowlength_lst[n]!=0 and zonal_flowlength_lst[n]<=150:
        row[6]=Area_lst[n]/zonal_flowlength_lst[n]
    else:row[6]=Area_lst[n]/150
    row[7]=ID_name_ele[n]
    cursor.updateRow(row)
    n+=1
del cursor,row
 
subcatchments_v=arcpy.FeatureVerticesToPoints_management(p_s,"subcatchments_v","ALL")
arcpy.AddField_management(subcatchments_v,"subxcoord","FLOAT")
arcpy.AddField_management(subcatchments_v,"subycoord","FLOAT")
subcatchments_xy=[row[0] for row in arcpy.da.SearchCursor(subcatchments_v,["SHAPE@XY"])]
 
cursor=arcpy.da.UpdateCursor(subcatchments_v,["subxcoord","subycoord"])
m=0
for row in cursor:
    row[0]=subcatchments_xy[m][0]
    row[1]=subcatchments_xy[m][1]
    cursor.updateRow(row)
    m+=1
del cursor,row
 
#value for.inp
#Imperv 0 or row[2]
subcatchments_p=[("S"+str(row[0]+1),"Gage_1","O"+str(row[0]+1),row[1]/10000,5,row[3],row[4],0," ") for row in arcpy.da.SearchCursor(p_s,["FID","Area","Imperv","slopWidth","Slope"]) if row[0] not in invalid_FID_lst]
subcatchments_name=[n[0] for n in subcatchments_p]
outlet_name=[n[2] for n in subcatchments_p]
subcatchments_ele=[row[0] for row in arcpy.da.SearchCursor(p_s,["OutletEle","FID"]) if row[1] not in invalid_FID_lst]
sub_xcoord=[row[0] for row in arcpy.da.SearchCursor(subcatchments_v,["subxcoord"])]
max_sub_xcoord=max(sub_xcoord)+100
min_sub_xcoord=min(sub_xcoord)-100
sub_ycoord=[row[0] for row in arcpy.da.SearchCursor(subcatchments_v,["subycoord"])]
max_sub_ycoord=max(sub_ycoord)+100
min_sub_ycoord=min(sub_ycoord)-100
outlet_xycoord=[(row[0],row[1]) for row in arcpy.da.SearchCursor(p_s,["Jxcoord","Jycoord","FID"]) if row[2] not in invalid_FID_lst]
subcatchment_polygon=[(row[0],row[1],row[2]) for row in arcpy.da.SearchCursor(subcatchments_v,["ID_name","subxcoord","subycoord"])]
subcatchment_FID_ID_name=[(row[0],"S"+str(row[1]+1)) for row in arcpy.da.SearchCursor(p_s,["ID_name","FID"]) if row[1] not in invalid_FID_lst]
subcatchment_FID_ID_name_dic=dict(subcatchment_FID_ID_name)
subcatchment_polygon_name=[(subcatchment_FID_ID_name_dic[i[0]],i[1],i[2]) for i in subcatchment_polygon if i[0] in subcatchment_FID_ID_name_dic.keys()]
Gage1_xy=[(row[0][0],row[0][1]) for row in arcpy.da.SearchCursor(Gage_1,["SHAPE@XY"])]
 
#.inp
swmm_inp="D:\SWMM_cal\swmm_inp_pre.inp"
swmm_name="huxian_swmm"
 
f=open(swmm_inp,'w')
f.write("[TITLE]\n;;Project Title/Notes\nswmm_name\n\n")
f.write("[OPTIONS]\n;;Option\tValue\n")
options_values=("CMS","HORTON","KINWAVE","DEPTH",0,"NO","NO")
f.write("FLOW_UNITS\t%s\nINFILTRATION\t%s\nFLOW_ROUTING\t%s\nLINK_OFFSETS\t%s\nMIN_SLOPE\t%.2f\nALLOW_PONDING\t%s\nSKIP_STEADY_STATE\t%s\n\n" % options_values)
 
 
START_DATE="11/15/2015"
START_TIME="00:00:00"
REPORT_START_DATE="11/15/2015"
REPORT_START_TIME="00:00:00"
END_DATE="11/15/2015"
END_TIME="04:00:00"
SWEEP_START="01/01"
SWEEP_END="12/31"
DRY_DAYS="0"
REPORT_STEP="00:01:00"
WET_STEP="00:01:00"
DRY_STEP="01:00:00"
ROUTING_STEP="0:01:00"
time_values=(START_DATE,START_TIME,REPORT_START_DATE,REPORT_START_TIME,END_DATE,END_TIME,SWEEP_START,SWEEP_END,DRY_DAYS,REPORT_STEP,WET_STEP,DRY_STEP,ROUTING_STEP)
f.write("START_DATE\t%s\nSTART_TIME\t%s\nREPORT_START_DATE\t%s\nREPORT_START_TIME\t%s\nEND_DATE\t%s\nEND_TIME\t%s\nSWEEP_START\t%s\nSWEEP_END\t%s\nDRY_DAYS\t%s\nREPORT_STEP\t%s\nWET_STEP\t%s\nDRY_STEP\t%s\nROUTING_STEP\t%s\n\n" % time_values)
 
f.write("[EVAPORATION]\n;;Data Source\tParameters\n;;-------------- ----------------\n")
evaporation_values=(0,"NO")
f.write("CONSTANT\t%.2f\nDRY_ONLY\t%s\n\n" % evaporation_values)
 
f.write("[RAINGAGES]\n;;Name\tFormat\tInterval\tSCF\tSource\n;;-------------- --------- ------ ------ ----------\n")
raingages_value=("Gage_1","INTENSITY","0:05",1.0,"TIMESERIES TS1")
f.write("%s\t%s\t%s\t%.1f\t%s\n\n" % raingages_value)
 
f.write("[SUBCATCHMENTS]\n;;Name\tRain Gage\tOutlet\tArea\t%Imperv\tWidth\t%Slope\tCurbLen\tSnowPack \n;;-------------- ---------------- ---------------- -------- -------- -------- -------- -------- ----------------\n")
for k in range(len(subcatchments_p)):
  f.write("%s\t%s\t%s\t%.2f\t%d\t%d\t%.2f\t%.2f\t%s\n" % subcatchments_p[k])
f.write("\n")
 
f.write("[SUBAREAS]\n;;Subcatchment\tN-Imperv\tN-Perv\tS-Imperv\tS-Perv\tPctZero\tRouteTo\tPctRouted\n;;-------------- ---------- ---------- ---------- ---------- ---------- ---------- ----------\n")
N_Imperv=0.015
N_Perv=0.24
S_Imperv=0.06
S_Perv=0.3
PctZero=25
RouteTo="OUTLET"
for k in subcatchments_name:
  f.write("%s\t%.2f\t%.2f\t%.2f\t%.2f\t%d\t%s\n" % (k,N_Imperv,N_Perv,S_Imperv,S_Perv,PctZero,RouteTo))
f.write("\n")
 
f.write("[INFILTRATION]\n;;Subcatchment\tMaxRate\tMinRate\tDecay\tDryTime\tMaxInfil\n;;-------------- ---------- ---------- ----------\n")
MaxRate=4.5
MinRate=0.2
Decay=6.5
DryTime=7
MaxInfil=0
for k in subcatchments_name:
  f.write("%s\t%.2f\t%.2f\t%.2f\t%d\t%d\n" % (k,MaxRate,MinRate,Decay,DryTime,MaxInfil))
f.write("\n")
 
f.write("[OUTFALLS]\n;;Name\tElevation\tType\tStage Data\tGated\tRoute To\n;;-------------- ---------- ---------- ---------------- -------- ----------------\n")
Type="FREE"
Gated="NO"
for k in range(len(outlet_name)):
  f.write("%s\t%.2f\t%s\t%s\t%s\t%s\n" % (outlet_name[k],subcatchments_ele[k],Type," ",Gated,""))
f.write("\n")
 
f.write("[TIMESERIES]\n;;Name\tDate\tTime\tValue\n;;-------------- ---------- ---------- ----------\n")
timeseries_name="TS1"
t_lst=[]
t_per_v="D:\SWMM_cal\\tyr.txt"
t_yr=open(t_per_v,"r")
for line in t_yr.readlines():
    lst=line.split()
    t_lst.append((lst[0],float(lst[1])*2.54))
t_yr.close()
#time_value=((0,0),(1,0.5),(2,1),(3,0.75),(4,0.5),(5,0.25),(6,0))
for k in range(len(t_lst)):
    f.write("%s\t%s\t%s\t%.2f\n" % (timeseries_name," ",t_lst[k][0],t_lst[k][1]))
f.write("\n")
 
f.write("[REPORT]\n;;Reporting Options\n")
report_value=("NO","NO","ALL","ALL","ALL")
f.write("INPUT\t%s\nCONTROLS\t%s\nSUBCATCHMENTS\t%s\nNODES\t%s\nLINKS\t%s\n" % report_value)
f.write("\n")
 
f.write("[TAGS]\n\n")
f.write("[MAP]\nDIMENSIONS\t%.3f\t%.3f\t%.3f\t%.3f\n" % (min_sub_xcoord,min_sub_ycoord,max_sub_xcoord,max_sub_ycoord))
Units_value="Meters"
f.write("Units\t%s\n\n" % Units_value)
 
f.write("[COORDINATES]\n;;Node\tX-Coord\tY-Coord\n;;-------------- ------------------ ------------------\n")
for k in range(len(outlet_name)):
  f.write("%s\t%.3f\t%.3f\n" % (outlet_name[k],outlet_xycoord[k][0],outlet_xycoord[k][1]))
f.write("\n")
 
f.write("[VERTICES]\n;;Link\tX-Coord\tY-Coord\n;;-------------- ------------------ -----------------\n")
 
f.write("[Polygons]\n;;Subcatchment\tX-Coord\tY-Coord\n;;-------------- ------------------ ------------------\n")
for k in subcatchment_polygon_name:
  f.write("%s\t%.3f\t%.3f\n" % k)
f.write("\n")
 
f.write("[SYMBOLS]\n;;Gage\tX-Coord\tY-Coord\n;;-------------- ------------------ ------------------\n")
f.write("%s\t%.3f\t%.3f\n" % ("Gage_1",Gage1_xy[0][0],Gage1_xy[0][1]))
 
 
f.close()
```


