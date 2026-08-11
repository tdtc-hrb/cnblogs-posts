---
title: "WebSockets over HTTP/2"
description: "WebSockets over HTTP/2 using ASP.NET"
date: 2024-11-18T20:38:18+08:00
---
[NET 7 introduces Websockets over HTTP/2 support for Kestrel, the SignalR JavaScript client, and SignalR with Blazor WebAssembly.](https://github.com/dotnet/AspNetCore.Docs/commit/75ce89b25e9ba61f9687ce36a41cd3c50aec1e3c)

### HTTP2
- Multiplexing
![Request multiplexing](https://miro.medium.com/v2/resize:fit:720/format:webp/0*lY05UTuA-dWCXU-q.png)
- Server push
![cacheable information](https://miro.medium.com/v2/resize:fit:640/format:webp/0*ZJLgVdXq_06hcF1o.png)
- Header compression
![HPACK](https://miro.medium.com/v2/resize:fit:720/format:webp/0*5r8-MbhEseP6lEQg.png)

ref site:
- [HTTP/2 vs. HTTP/1.1: How do they affect web performance?](https://www.cloudflare.com/learning/performance/http2-vs-http1.1/)
- [HTTP/2: the difference between HTTP/1.1, benefits and how to use it](https://factoryhr.medium.com/http-2-the-difference-between-http-1-1-benefits-and-how-to-use-it-38094fa0e95b)

#### os and browsers support
* Windows 10 1607+
* TLS 1.2+
* [browsers](https://caniuse.com/http2)

Note: recommended to use Chrome/FireFox


## WebSockets
- Accept a WebSocket connection as an asynchronous operation    
[HttpListenerContext.AcceptWebSocketAsync()](https://learn.microsoft.com/en-us/dotnet/api/system.net.httplistenercontext.acceptwebsocketasync)
- Receives data from the WebSocket connection asynchronously    
[WebSocket.ReceiveAsync()](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocket.receiveasync)
- Sends data over the WebSocket connection asynchronously    
[WebSocket.SendAsync()](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocket.sendasync)
- Closes the WebSocket connection as an asynchronous operation    
[CloseAsync](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocket.closeasync)

### Enable WebSocket
- default
```
app.UseWebSockets()
```
- options
```
var webSocketOptions = new WebSocketOptions
{
    KeepAliveInterval = TimeSpan.FromMinutes(2)
};

app.UseWebSockets(webSocketOptions);
```

### Accept WebSocket requests
```
if (HttpContext.WebSockets.IsWebSocketRequest)
{
    using var webSocket = await HttpContext.WebSockets.AcceptWebSocketAsync();
}
```

### receive messages
```
var buffer = new byte[1024 * 4];

var receiveResult = await webSocket.ReceiveAsync(
    new ArraySegment<byte>(buffer), CancellationToken.None);
```

#### [ArraySegment](https://learn.microsoft.com/en-us/dotnet/api/system.arraysegment-1)
Array version of substring()
```
String[] names = { "Adam", "Bruce", "Charles", "Daniel",
                         "Ebenezer", "Francis", "Gilbert",
                         "Henry", "Irving", "John", "Karl",
                         "Lucian", "Michael" };
      var partNames = new ArraySegment<string>(names, 2, 5);
```
The example displays the following output:
```
//    Charles
//    Daniel
//    Ebenezer
//    Francis
//    Gilbert
```

### Send messages
```
var memory = new ReadOnlyMemory<byte>(Encoding.UTF8.GetBytes(str));

await webSocket.SendAsync(
                memory,
                0, // text message
                0, // none
                CancellationToken.None);
```

#### [ReadOnlyMemory](https://learn.microsoft.com/en-us/dotnet/api/system.readonlymemory-1)
- [message](https://stackoverflow.com/a/68306380)
```
var memory = new ReadOnlyMemory<byte>(Encoding.UTF8.GetBytes(str));
```


#### [MessageType](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocketmessagetype)
- Binary
```
1
```
The message is in binary format.

- Close
```
2
```
A receive has completed because a close message was received.

- Text
```
0
```
The message is clear text.

#### [WebSocketMessageFlags](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocketmessageflags)
- DisableCompression
```
2
```
Disables compression for the message if compression has been enabled for the WebSocket instance.

- EndOfMessage
```
1
```
Indicates that the data in "buffer" is the last part of a message.

- None
```
0
```
None


### close
```
await webSocket.CloseAsync(
            1000,  // NormalClosure
            "normal closure",
            CancellationToken.None);
```
#### [WebSocketCloseStatus](https://learn.microsoft.com/en-us/dotnet/api/system.net.websockets.websocketclosestatus)
Fields:
```
1000, 1001, 1002, 1003, 1005, 1007, 1008, 1009, 1010, 1100
```

### [Samples](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/websockets/samples)
- WebSocketController    
Operation WebSocket
- Program.cs    
Enable WebSocket


## Ref
- [WebSockets support in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/websockets)
- [Writing a WebSocket server in C#](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_server)
- [what is ws and how it work](https://www.wallarm.com/what/a-simple-explanation-of-what-a-websocket-is)
