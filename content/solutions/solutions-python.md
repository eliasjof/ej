# Python 3.8

## Error np.float

type: module numpy has no attribute 'float'. np.float was a deprecated alias for the builtin float. to avoid this error in existing code, use float by itself.

![[error_float.png]]

### Solution
```python
# Override np.float to np.float64 
# put that after import numpy as np and before the lib which uses np.float
np.float = np.float64
```
