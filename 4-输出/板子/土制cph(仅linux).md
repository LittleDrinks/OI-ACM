---
tags: 
aliases:
---
第一版，使用 `timeout`，仅适用 linux
```python
from os import system as e, listdir as l
from sys import argv
_, q, f = argv  # q: 题号, f: 文件名

# r: 运行命令
# 跑 py 需要 `python3 cph.py A A.py`
if "py" in f:
	r = f"python3 {f}"
else:
	e(f"g++ -std=c++23 -o2 -Wall {f}.cpp -o {f}")
	r = "./" + f

#
d = "samples-" + q.capitalize()
for i in l(d):
	if not "in" in i: continue
	print(i)
	p = d + "/" + i[:-2]
	if e(f"timeout 2 {r}<{p}in>{p}out"): print("TLE or RE")
	else:
		with open(f"{p}out") as o, open(f"{p}ans") as a:
			print("AC" if o.read().split()==a.read().split() else "WA")
```
