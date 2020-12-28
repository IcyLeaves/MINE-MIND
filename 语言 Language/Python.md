# Python

*summary*

<img src="./Python.assets/image-20201225225511373.png" alt="image-20201225225511373" style="zoom:80%;" />

**Python**是一门语言。

---

*2020.12.25*

### python-对每个元素依次执行同一个操作，并生成新列表

``` python
>> map(lambda x: x ** 2, [1,2,3])
[1,4,9]
```

### numpy-删除shape=1的维度

``` python
import numpy as np
arr.shape # (3,2,1)
np.squeeze(arr) # (3,2)
```

