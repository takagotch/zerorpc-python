### zerorpc-python
---
https://github.com/0rpc/zerorpc-python


```py
//
from __future__ import absolute_import
from builtins import range

import gevent
import zerorpc

from .testutils import teardown, random_ipc_endpoint, TIME_FACTOR

class EchoModule(object):
  
  def __init__(self, trigger=None):
  
  def echo(self, msg):
  
  @zerorpc.stream
  def echoes(self, msg):
    self.last_msg = 'echo: ' + msg
    for i in range(0, 3):
      yield self._last_msg

class ServerBeforeExecMiddleware(object):

  def __init__(self):
  
  def server_before_exec(self, request_event):

```

```
```

```
```
