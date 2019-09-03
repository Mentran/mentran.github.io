title: Socket
author: Mentran
tags:
  - socket
  - java
  - python
categories:
  - java
  - python
date: 2019-09-03 16:46:00
---
### Socket 代码实现
### Java版
#### 服务端
```java
class Server {
    private ServerSocket serverSocket;

    // 初始化socket服务和端口
    public void init(int port) {
        try {
            serverSocket = new ServerSocket(port);
            System.out.println("Server is running on port: " + port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void run() {
        while (true) {
            try {
                System.out.println("wait connection");
                Socket socket = serverSocket.accept();

                // 接收信息
                InputStream inputStream = socket.getInputStream();
                String sInput = trans2string(inputStream);
                System.out.println("receive: " + sInput);

                // 处理信息
                String sResult = mainProcess(sInput);
                System.out.println("send: " + sResult);

                // 发送结果
                PrintStream printStream = new PrintStream(socket.getOutputStream(), false, "utf8");
                printStream.print(sResult);

                // 关闭连接
                printStream.close();
                inputStream.close();
                socket.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    private String trans2string(InputStream inputStream) throws IOException {
        ByteArrayOutputStream result = new ByteArrayOutputStream();
        byte[] buffer = new byte[1024];
        int length;
        while ((length = inputStream.read(buffer)) != -1) {
            result.write(buffer, 0, length);
        }
        result.close();
        return result.toString("UTF-8");
    }
}
```

<!--more -->
#### 客户端
```java
public static String clientProcess(String ip, int port, String message) throws IOException {
        // step1: init
        Socket socket = new Socket(ip, port);

        // step2: send
        PrintStream printStream = new PrintStream(socket.getOutputStream());
        printStream.print(message);
        socket.shutdownOutput();

        // step3: receive
        InputStream inputStream = socket.getInputStream();
        String result = trans2string(inputStream);

        // step4: close
        inputStream.close();
        printStream.close();
        socket.close();

        return result;
    }
```

### Python 版
#### 服务端
```python
def socketserver(HOST, PORT):
    # step1: init
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind((HOST, PORT))
    s.listen(20)
    print("listening", HOST, PORT)

    while True:
        # step2: accept
        conn, addr = s.accept()
        print("connected by", addr)

        try:
            receive_message = conn.recv(10240)

            receive_message = bytes.decode(receive_message, "utf8")
            print("receive_message:", receive_message)

            # step3: parse data
            result_message = mainProcess(receive_message)
            print("process result", result_message)

            # step4: send back
            conn.send(bytes(str(result_message), encoding='utf8'))
        except Exception as e:
            print(e)
        finally:
            conn.close()
```

#### 客户端
```python
def socketclient(HOST, PORT, message):
    # step1: init
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((HOST, PORT))

    # step2: send
    message = bytes(message, encoding='utf8')
    s.sendall(message)
    s.shutdown(socket.SHUT_WR)

    # step3: receive (default receive bytes:)
    data = recv_basic(s)
    print(bytes.decode(data, 'utf8'))

    # step: close
    s.close()

def recv_basic(tmp_socket):
    out_data = ""
    while True:
        data = tmp_socket.recv(1024).decode("utf8")
        if not data:
            break
        out_data += data
    return out_data
```