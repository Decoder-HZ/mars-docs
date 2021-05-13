# PyTorch2MarsNet

```python
import sys
import numpy as np
import torch
import os
import struct

if len(sys.argv) != 3:
    sys.stderr.write('usage: %s + torch.model + model.bin\n' % __file__)
    sys.exit(-1)

model = sys.argv[1]
bin_file = sys.argv[2]

def write_weights(name, shape, value):
    fout.write(struct.pack('f', len(name)))
    print (len(name))
    fout.write(str.encode(name))
    print (name)

    dim = len(shape)
    fout.write(struct.pack('f', dim))
    print (dim)
    for s in shape:
        fout.write(struct.pack('f', s))
    print (shape)

    arr = value.flatten()
    fout.write(struct.pack('f', len(arr)))
    print (len(arr))
    for i in arr:
        fout.write(struct.pack('f', i))
    print (arr.sum())

fout   = open(bin_file, "wb")
params = torch.load(model)

for name in params:
    value = params[name].cpu().numpy()
    shape = value.shape

    name = name.replace(".weight", ".weights")
    name = name.replace(".bias", ".biases")
    name = name.replace(".in_proj_weight", ".in_proj.weights")
    name = name.replace(".in_proj_bias", ".in_proj.biases")
    print (name)
    print (shape)

    write_weights(name, shape, value)
```
