---
tags: 
aliases:
---
第二版，使用 `subprocess`，添加彩字，适用 windows 和 linux，支持计算相对误差
```python
from os import listdir as l, system as e
from os.path import join as jp
from sys import argv as a
import subprocess as s
_,q,f = a
if "py" in f:
    cmd = f"python {f}"
else:
    e(f"g++ -std=gnu++20 -O2 -Wall {f}.cpp -o {f}")
    cmd = jp(".", f"{f}.exe")

d='samples-'+q.capitalize()
g,r,p,n='\033[32m','\033[31m','\033[35m','\033[0m'
for i in l(d):
    if not 'in' in i: continue
    t=jp(d,i[:-2])
    print(i,'测评结果 ',end='',flush=1)
    try:
        k = s.run(cmd, timeout=2, stdin=open(f"{t}in"), 
                 stdout=open(f"{t}out", 'w'), stderr=s.PIPE, text=1)
        c = lambda o,a : o.read().split() == a.read().split()
        # c = lambda o,a : all(abs(float(x)-float(y))/max(1,abs(float(y)))<=1e-4 
        #               for x,y in zip(o.read().split(),a.read().split()))
        print(f'{g}AC{n}' if c(open(f"{t}out"),open(f"{t}ans")) else f'{r}WA{n}')
        if k.stderr:print(f'{r}{k.stderr}{n}')
    except s.TimeoutExpired as k:
        print(f'{p}TLE or RE{n}')
        if k.stderr: print(f'{r}{k.stderr}{n}')
```
