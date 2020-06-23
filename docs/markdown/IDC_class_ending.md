🐞 author: richie bao 📅 Oct 31, 2017
# 数字营造导论_12_结课
<a href="http://digit-x.org/digitLink/digitaldesignIntro/DCI_12_ending.html" target = "_blank"><img src="./imgs/0108.png" height="auto" width="auto"  title="digit-x"></a>

[download 'DCI_12_ending.html'](https://github.com/digit-x/digit_x/tree/master/docs/html)

* is_num

```python
bool=[]
def is_num(num):
    try:
        float(num)
        return True
    except ValueError:
        return False
bool=[is_num(i) for i in text]
```