---
tags: 
aliases:
---
```python
from os import listdir as l, system as e
from os.path import join as j
from sys import argv
import subprocess as s

q,f = argv[1:3]
tid = argv[3] if len(argv)>3 else None

if "py" in f:
    cmd = ["python3", f]
else:
    e(f"g++ -std=gnu++20 -O2 -Wall {f}.cpp -o {f}")
    cmd = j(".", f)

d='samples-'+q.capitalize()
g,r,p,n='\033[32m','\033[31m','\033[35m','\033[0m'
for i in l(d):
    if ("in" not in i) or (tid and i != tid + ".in"):
        continue
    t = j(d, i[:-2])
    print(i,end=' ',flush=1)
    try:
        k = s.run(cmd, timeout=2, stdin=open(f"{t}in"), 
                 stdout=open(f"{t}out", 'w'), stderr=s.PIPE, text=1)
        # c = lambda o,a : all(abs(float(x)-float(y))/max(1,abs(float(y)))<=1e-4 
        #               for x,y in zip(o.read().split(),a.read().split()))
		o = open(f"{t}out").read().split()
		a = open(f"{t}ans").read().split()
		print(f"{g}AC{n}" if o == a else f"{r}WA{n}")
        if k.stderr:print(f'{r}{k.stderr}{n}')
    except s.TimeoutExpired as k:
        print(f'{p}TLE or RE{n}')
        if k.stderr: print(f'{r}{k.stderr}{n}')
```
