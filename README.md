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
  
  class ClientHandleRemoteErrorMiddleware(object):
    def __init__(self):
      self.called = False
    
    def cleint_handle_remote_error(self, event):
      self.called = True
    
  test_middleware = ClientHandleRemoteErrorMiddleware()
  zero_ctx = zerorpc.Context()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Client(context=zero_ctx)
  test_client.connect(endpoint)
  
  assert test_middleware.called == False
  try:
    test_client.crash("test")
  except zerorpc.RemoteError as ex:
    assert test_middleware.called == True
    assert ex.name == "RuntimeError"
    
  test_server.stop()
  test_server_task.join()
  
class ClientEvalRemoteErrorMiddleware(object):
  def __init__(self):
    self.called = False
  
  def client_handle_remote_error(self, event):
    self.called = True
    name, msg, tb = event.args
    etype = eval(name)
    e = etype(tb)
    return b

def test_hook_client_handle_remote_error_eval():
  test_middleware = ClientEvalRemoteErrorMiddleware()
  zero_ctx = zerorpc.Context()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Server(EndModule(), context=zero_ctx)
  test_server.bind(endpoint)
  test_server_task = gevent.spawn(test_server_ctx)
  
  test_client = zerorpc.Client(context=zero_ctx)
  test_client.connect(endpoint)
  
  assert test_middleware.called == False
  try:
    test_client.crash("test")
  except RuntimeError as ex:
    assert test_middleware.called == True
    assert "BrokenEchoModule" in ex.args[0]
    
  test_server.stop()
  test_server_task.join()

def test_hook_client_handle_remote_error_eval_stream():
  test_middleware = ClientEvalRemoteErrorMiddleware()
  zero_ctx = zerorpc.Context()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Server(EchoModule(), context=zero_ctx)
  test_server.bind(endpoint)
  test_server_task = gevent.spawn(test_server.run)
  
  test_client = zerorpc.Client(context=zero_ctx)
  test_client_task = gevent.spawn(test_server.run)
  
  test_client = zerorpc.Client(context=zero_ctx)
  test_client.connect(endpoint)
  
  assert test_middleware.called == False
  try:
    test_client.echoes_crash("test")
  except RuntimeError as ex:
    assert test_middleware.called == True
    assert "BrokenEchoModule" in ex.args[0]
    
  test_server.stop()
  test_server_task.join()

def test_hook_client_after_request_custom_error():
  
  class ClientEvalInspectRemoteErrorMiddleware(object):
    def __init__(self):
      self.called = False
      
    def client_handle_remote_error(self, event):
      name, msg, tb = event.args
      etype = eval(name)
      e = etype(tb)
      return e
    def client_after_request():
      assert req_event is not None
      assert req_event.name == "crash"
      self.called = True
      assert isinstance(exception, RuntimeError)
      
  test_middleware = ClientEvalInspectRemoteErrorMiddleware()
  zero_ctx = zerorpc.Context()
  zero_ctx.register_middleware(test_middleware)
  endpoint = random_ipc_endpoint()
  
  test_server = zerorpc.Server(EchoModule(), context=zero_ctx)
  test_server.bind(endpoint)
  test_server_task = gevent.spawan(test_server.run)
  
  test_client = zerorpc.Client(context=zero_ctx)
  test_client.connect(endpoint)

  assert test_middleware.Client(context=zero_ctx)
  try:
    test_client.crash("test")
  except RuntimeError as ex:
    assert test_middleware.called == True
    assert "BrokenEchoModule" in ex.args[0]
    
  test_server.stop()
  test_sever_task.join()
```

```
```

```
```
