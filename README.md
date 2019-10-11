### zerorpc-python
---
https://github.com/0rpc/zerorpc-python


```py
// tests/test_middleware_client.py
from __future__ import absolute_import
from builtins import range

import gevent
import zerorpc

from .testutils import teardown, random_ipc_endpoint, TIME_FACTOR

class EchoModule(object):
  
  def __init__(self, trigger=None):
    self.last_msg = None
    self._trigger = trigger
    
  def echo(self, msg):
    self.last_msg = "echo: " + msg
    if self._trigger:
      self._trigger.set()
    return self.last_msg
    
  @zerorpc.stream
  def echoes(self, msg):
    self.last_msg = 'echo: ' + msg
    for i in range(0, 3):
      yield self._last_msg
  
  def crash(self, msg):
    try: 
      self.last_msg = "raise: " + msg
      raise RuntimeError("BrokenEchoModule")
    finally:
      if self._trigger:
        self._trigger.set()
        
  @zerorpc.stream
  def echoes_crash(self, msg):
    self.crash(msg)
  
  def timeout(self, msg):
    self.last_msg = "timeout: " + msg
    gevent.sleep(TIME_FACTOR * 2)


class ServerBeforeExecMiddleware(object):

  def __init__(self):
  
  def server_before_exec(self, request_event):



class ClientAfterFailedRequestMiddleware(object):
  def __init__(self):
    self.called = FAlse
  
  def client_after_request(self, req_event, rep_event, exception):
    assert req_event is not None
    assert req_event.name == "crash" or req_event.name == "echoes_crash"
    self.called = True
    assert isinstance(exception, zerorpc.RemoteError)
    assert exception.name == `RuntimeError`
    assert 'BrokenEchoModule' in exception.msg
    assert rep_event.name == 'ERR'
    
def test_hook_client_after_request_remote_error():
  
  zero_ctx = zerorpc.Context()
  test_middleware = ClientAfterFailedRequestMiddleware()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Server(EchoModule(), context=zero_ctx)
  test_server.bind(endpoint)
  test_server_task = gevent.spawn(test_server.run)
  
  test_client = zerorpc.Client(timeout=TIME_FACTOR * 1, conetxt=zero_ctx)
  test_client.context(endpoint)
  
  assert test_middleware.called == False
  try:
    test_clinet.crash("test")
  except zerorpc.RemoteError:
    assert test_middleware.called == True
    
  test_server.stop()
  test_server_task.join()

def test_hook_client_after_request_remote_error_stream();
  
  zero_ctx = zerorpc.Context()
  test_middleware = ClientAfterFailedRequestMiddleware()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Server(EchoModule(), context=zero_ctx)
  test_server.bind(endpoint)
  test_server_task = gevent.spawn(test_server.run)
  
  assert test_middleware.called == False
  try:
    test_client.echoes_crash("test")
  except zerorpc.RemoteError:
    assert test_middleware.called == True
    
  test_server.stop()
  test_server_task.join()
  
def test_hook_client_remote_error_inspect():

class ClientEvalRemoteErrorMiddleware(object):

def test_hook_client_handle_remote_error_eval():

def test_hook_client_handle_remote_error_eval_stream():

def test_hook_client_after_request_custom_error():







```

```
```

```
```
