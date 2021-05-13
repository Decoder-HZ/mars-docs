# Tensorflow2MarsNet

```python

import sys
import tensorflow as tf
import numpy as np
import os
import struct

if len(sys.argv) != 3:
    sys.stderr.write('usage: %s + model.ckpt + model.bin\n' % __file__)
    sys.exit(-1)

checkpoint=sys.argv[1]
bin_file = sys.argv[2]

fout = open(bin_file, "wb")

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

    arr = value.flatten().astype(np.float32) # half float
    fout.write(struct.pack('f', len(arr)))
    print (len(arr))
    fout.write(bytearray(arr.data))
    print (arr.sum())

def slice_lstm_weights(name, shape, value):
    print (name)
    if name.find('weights') != -1:
        node_dim = shape[1]/4
        x_dim = shape[0]-node_dim
        wx = value[:x_dim]
        wc = value[x_dim:]
        wx = np.split(wx, 4, axis=1)
        wc = np.split(wc, 4, axis=1)
        xshape = wx[0].shape
        cshape = wc[0].shape
        write_weights(name+"_wxi", xshape, wx[0])
        write_weights(name+"_wxj", xshape, wx[1])
        write_weights(name+"_wxf", xshape, wx[2])
        write_weights(name+"_wxo", xshape, wx[3])
        write_weights(name+"_wci", cshape, wc[0])
        write_weights(name+"_wcj", cshape, wc[1])
        write_weights(name+"_wcf", cshape, wc[2])
        write_weights(name+"_wco", cshape, wc[3])
    elif name.find('biases') != -1:
        b = np.split(value, 4)
        bshape = b[0].shape
        write_weights(name+"_bi", bshape, b[0])
        write_weights(name+"_bj", bshape, b[1])
        write_weights(name+"_bf", bshape, b[2])
        write_weights(name+"_bo", bshape, b[3])


var_list = tf.contrib.framework.list_variables(checkpoint)
reader = tf.contrib.framework.load_checkpoint(checkpoint)

for (name, shape) in var_list:
    if name.find('Adam') != -1 or name.find('global_step') != -1 or name.find('power') != -1 or name.find("Grad") != -1:
        continue
    if name.find('asr_fc_in') != -1:
        continue

    value = reader.get_tensor(name)

    name = name.replace("Matrix", "weights")
    name = name.replace("Bias", "biases")

    if name.find('multi_rnn_cell') != -1:
        name = name.replace("/ZoneoutLSTMCell/Linear/", "/")
        slice_lstm_weights(name, shape, value)
        continue

    write_weights(name, shape, value)

fout.close()
```
