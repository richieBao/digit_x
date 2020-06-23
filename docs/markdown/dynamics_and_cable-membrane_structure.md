🐞 author: richie bao 📅 Sep 26, 2017
# 数字营造导论_07_动力学与索膜结构
<a href="http://digit-x.org/digitLink/digitaldesignIntro/DCI_07_Para_dynamics.html" target = "_blank"><img src="./imgs/0087.png" height="auto" width="auto"  title="digit-x"></a>

[download 'DCI_07_Para_dynamics.html'](https://github.com/digit-x/digit_x/tree/master/docs/html)

<img src="./imgs/0088.jpg" height="auto" width="auto"  title="digit-x" />
<img src="./imgs/0089.jpg" height="auto" width="auto"  title="digit-x" />
<img src="./imgs/0090.jpg" height="auto" width="auto"  title="digit-x" />
<img src="./imgs/0091.jpg" height="auto" width="auto"  title="digit-x" />
<img src="./imgs/0092.jpg" height="auto" width="auto"  title="digit-x" />

```python
import Rhino
import rhinoscriptsyntax as rs
from Grasshopper import DataTree
from Grasshopper.Kernel.Data import GH_Path
data=TreeData
branches=data.Branches
PT=DataTree[Rhino.Geometry.GeometryBase]()
def grouper(branches,dt):
    for m in range(len(branches)-1):
        a=branches[m]
        b=branches[m+1]
        for i in range(len(a)-1):
            lst=[]
            lst.append(b[i])
            lst.append(a[i])
            lst.append(b[i+1])
            lst.append(a[i+1])
            dt.AddRange(lst,GH_Path(m,i))
    return dt
PLst=grouper(branches,PT)
```