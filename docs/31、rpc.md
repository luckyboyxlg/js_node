# 三十一、RPC

+ js客户端向服务端发起请求

  ```javascript
  ws = new WebSocket('ws://127.0.0.1:8848/browser/?name=wencai');
  ws.onmessage = function (msg) {
      // console.log('接受服务器端消息=====',msg.data)
      ws.send('123456789')
  }
  ```

+ Python客户端向服务端发起请求

  ```python
  import websockets
  import asyncio
  
  
  async def main():
      async with websockets.connect('ws://127.0.0.1:8848/python/?name=wencai') as ws:
          print('连接')
          await ws.send('python发送的请求')
          msg = await ws.recv()
          print('服务器发回的消息：', msg)
  
  
  
  
  if __name__ == '__main__':
      asyncio.run(main())
  ```

+ 服务端

  pip install websockets

  ```python
  import websockets
  import asyncio
  import re
  
  # 存储ws连接对象
  python_server = {}
  js_server = {}
  
  # 处理事客户端浏览器还是python发过来的请求
  async def register(ws, path):
      # 根据请求类型不同存储对应的对象
      obj = re.compile(r"/(?P<action>.*?)/\?name=(?P<name>.*)")
      search_result = obj.search(path)
      action = search_result.group("action")
      name = search_result.group("name")
      print(name,'name')
      # 判断是浏览器还是python端
      if action == 'browser':
          js_server[name] = ws
          return action, name
  
      elif action == 'python':
          python_server[name] = ws
          return action, name
  
  
  async def handle(ws, path):
      print(ws, path, '------')
      action,name = await register(ws, path)
  
      async for msg in ws:
          if action == 'browser':
              print('接受到了browser传递过来的请求=====', msg)
              # 把浏览器端数据发给python端
              await python_server[name].send(msg)
          elif action == 'python':
              print('接受到了python传递过来的请求=====', msg)
              await js_server[name].send(msg)
  
  
  async def main():
      async with websockets.serve(handle, '127.0.0.1', 8848) as wbs:
          print('成功启动了websocket服务')
          await asyncio.Future()
  
  
  if __name__ == '__main__':
      asyncio.run(main())
  ```

+ 异步的web服务端 用于对外提供服务的接口

  + 安装

    pip install sanic

  ```python
  from sanic import Sanic, HTTPResponse
  import websockets
  import asyncio
  app = Sanic(__name__)
  import json
  
  
  @app.route("/get", methods=["GET", "POST"])
  async def func(req):
      # 在这里. 你可以接受参数. 指定哪个项目 rs4
      # 在url上面传参过来
      project_name = req.args.get("project_name")
      # 我制定的规则是:
      #   url上面传递的参数: project_name.
      #   该项目需要的其他参数, 全部通过json的形式传递过来
      # 强制规定了. 有参数必须走post. 参数通过json传递过来....
      # 此时浏览器上就无法完成测试了....
      project_params = req.json  # {url: "xxxxx"}
      if not project_params:
          project_params = "没参数"
  
      if project_name:
          async with websockets.connect(f"ws://127.0.0.1:8848/python/?name={project_name}") as ws:
              await ws.send(json.dumps(project_params))  # 发送数据过去
              print("连接成功了")
              ret = await ws.recv()
          return HTTPResponse(ret)
      else:
          return HTTPResponse("至少要给我一个project_name")
  
  
  if __name__ == '__main__':
      app.run()
  ```