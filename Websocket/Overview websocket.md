The WebSocket protocol enables ongoing, full-duplex, bidirectional communication between web servers and web clients over an underlying TCP connection
![[web-socket.png]]The base WebSocket protocol consists of an opening handshake (upgrading the connection from HTTP to WebSockets), followed by data transfer. After the client and server successfully negotiate the opening handshake, the WebSocket connection acts as a persistent full-duplex communication channel where each side can, independently, send data at will.
## URI schemes and syntax
![[uri-schemes-ws.png]]
## Opening handshake
Reference to this link for mechanism to open handshake by HTTP/1.1, HTTP/2.0, HTTP/3.0
https://websocket.org/guides/websocket-protocol/#opening-handshake

## Data framing
Once the WebSocket connection is established, the client and server can send WebSocket data frames back and forth in either direction. Each message consists of one or more frames. There are different types of frames:
- Text frames - contain UTF-8 encoded text data
- Binary frames - contain binary data
- Control frames - used for protocol-level signaling, such as pings, pongs, and close frames
For frame structure. Reference below link for more detail
https://websocket.org/guides/websocket-protocol/#websocket-frame-structure---deep-dive