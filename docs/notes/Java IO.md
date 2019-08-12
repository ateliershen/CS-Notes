<!-- GFM-TOC -->
* [一、概覽](#一概覽)
* [二、磁盤操作](#二磁盤操作)
* [三、字節操作](#三字節操作)
    * [實現文件複製](#實現文件複製)
    * [裝飾者模式](#裝飾者模式)
* [四、字符操作](#四字符操作)
    * [編碼與解碼](#編碼與解碼)
    * [String 的編碼方式](#string-的編碼方式)
    * [Reader 與 Writer](#reader-與-writer)
    * [實現逐行輸出文本文件的內容](#實現逐行輸出文本文件的內容)
* [五、對象操作](#五對象操作)
    * [序列化](#序列化)
    * [Serializable](#serializable)
    * [transient](#transient)
* [六、網絡操作](#六網絡操作)
    * [InetAddress](#inetaddress)
    * [URL](#url)
    * [Sockets](#sockets)
    * [Datagram](#datagram)
* [七、NIO](#七nio)
    * [流與塊](#流與塊)
    * [通道與緩衝區](#通道與緩衝區)
    * [緩衝區狀態變量](#緩衝區狀態變量)
    * [文件 NIO 實例](#文件-nio-實例)
    * [選擇器](#選擇器)
    * [套接字 NIO 實例](#套接字-nio-實例)
    * [內存映射文件](#內存映射文件)
    * [對比](#對比)
* [八、參考資料](#八參考資料)
<!-- GFM-TOC -->


# 一、概覽

Java 的 I/O 大概可以分成以下幾類：

- 磁盤操作：File
- 字節操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 對象操作：Serializable
- 網絡操作：Socket
- 新的輸入/輸出：NIO

# 二、磁盤操作

File 類可以用於表示文件和目錄的信息，但是它不表示文件的內容。

遞歸地列出一個目錄下所有文件：

```java
public static void listAllFiles(File dir) {
    if (dir == null || !dir.exists()) {
        return;
    }
    if (dir.isFile()) {
        System.out.println(dir.getName());
        return;
    }
    for (File file : dir.listFiles()) {
        listAllFiles(file);
    }
}
```

從 Java7 開始，可以使用 Paths 和 Files 代替 File。

# 三、字節操作

## 實現文件複製

```java
public static void copyFile(String src, String dist) throws IOException {
    FileInputStream in = new FileInputStream(src);
    FileOutputStream out = new FileOutputStream(dist);

    byte[] buffer = new byte[20 * 1024];
    int cnt;

    // read() 最多讀取 buffer.length 個字節
    // 返回的是實際讀取的個數
    // 返回 -1 的時候表示讀到 eof，即文件尾
    while ((cnt = in.read(buffer, 0, buffer.length)) != -1) {
        out.write(buffer, 0, cnt);
    }

    in.close();
    out.close();
}
```

## 裝飾者模式

Java I/O 使用了裝飾者模式來實現。以 InputStream 為例，

- InputStream 是抽象組件；
- FileInputStream 是 InputStream 的子類，屬於具體組件，提供了字節流的輸入操作；
- FilterInputStream 屬於抽象裝飾者，裝飾者用於裝飾組件，為組件提供額外的功能。例如 BufferedInputStream 為 FileInputStream 提供緩存的功能。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9709694b-db05-4cce-8d2f-1c8b09f4d921.png" width="650px"> </div><br>

實例化一個具有緩存功能的字節流對象時，只需要在 FileInputStream 對象上再套一層 BufferedInputStream 對象即可。

```java
FileInputStream fileInputStream = new FileInputStream(filePath);
BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
```

DataInputStream 裝飾者提供了對更多數據類型進行輸入的操作，比如 int、double 等基本類型。

# 四、字符操作

## 編碼與解碼

編碼就是把字符轉換為字節，而解碼是把字節重新組合成字符。

如果編碼和解碼過程使用不同的編碼方式那麼就出現了亂碼。

- GBK 編碼中，中文字符佔 2 個字節，英文字符佔 1 個字節；
- UTF-8 編碼中，中文字符佔 3 個字節，英文字符佔 1 個字節；
- UTF-16be 編碼中，中文字符和英文字符都佔 2 個字節。

UTF-16be 中的 be 指的是 Big Endian，也就是大端。相應地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

Java 的內存編碼使用雙字節編碼 UTF-16be，這不是指 Java 只支持這一種編碼方式，而是說 char 這種類型使用 UTF-16be 進行編碼。char 類型佔 16 位，也就是兩個字節，Java 使用這種雙字節編碼是為了讓一箇中文或者一個英文都能使用一個 char 來存儲。

## String 的編碼方式

String 可以看成一個字符序列，可以指定一個編碼方式將它編碼為字節序列，也可以指定一個編碼方式將一個字節序列解碼為 String。

```java
String str1 = "中文";
byte[] bytes = str1.getBytes("UTF-8");
String str2 = new String(bytes, "UTF-8");
System.out.println(str2);
```

在調用無參數 getBytes() 方法時，默認的編碼方式不是 UTF-16be。雙字節編碼的好處是可以使用一個 char 存儲中文和英文，而將 String 轉為 bytes[] 字節數組就不再需要這個好處，因此也就不再需要雙字節編碼。getBytes() 的默認編碼方式與平臺有關，一般為 UTF-8。

```java
byte[] bytes = str1.getBytes();
```

## Reader 與 Writer

不管是磁盤還是網絡傳輸，最小的存儲單元都是字節，而不是字符。但是在程序中操作的通常是字符形式的數據，因此需要提供對字符進行操作的方法。

- InputStreamReader 實現從字節流解碼成字符流；
- OutputStreamWriter 實現字符流編碼成為字節流。

## 實現逐行輸出文本文件的內容

```java
public static void readFileContent(String filePath) throws IOException {

    FileReader fileReader = new FileReader(filePath);
    BufferedReader bufferedReader = new BufferedReader(fileReader);

    String line;
    while ((line = bufferedReader.readLine()) != null) {
        System.out.println(line);
    }

    // 裝飾者模式使得 BufferedReader 組合了一個 Reader 對象
    // 在調用 BufferedReader 的 close() 方法時會去調用 Reader 的 close() 方法
    // 因此只要一個 close() 調用即可
    bufferedReader.close();
}
```

# 五、對象操作

## 序列化

序列化就是將一個對象轉換成字節序列，方便存儲和傳輸。

- 序列化：ObjectOutputStream.writeObject()
- 反序列化：ObjectInputStream.readObject()

不會對靜態變量進行序列化，因為序列化只是保存對象的狀態，靜態變量屬於類的狀態。

## Serializable

序列化的類需要實現 Serializable 接口，它只是一個標準，沒有任何方法需要實現，但是如果不去實現它的話而進行序列化，會拋出異常。

```java
public static void main(String[] args) throws IOException, ClassNotFoundException {

    A a1 = new A(123, "abc");
    String objectFile = "file/a1";

    ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(objectFile));
    objectOutputStream.writeObject(a1);
    objectOutputStream.close();

    ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(objectFile));
    A a2 = (A) objectInputStream.readObject();
    objectInputStream.close();
    System.out.println(a2);
}

private static class A implements Serializable {

    private int x;
    private String y;

    A(int x, String y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return "x = " + x + "  " + "y = " + y;
    }
}
```

## transient

transient 關鍵字可以使一些屬性不會被序列化。

ArrayList 中存儲數據的數組 elementData 是用 transient 修飾的，因為這個數組是動態擴展的，並不是所有的空間都被使用，因此就不需要所有的內容都被序列化。通過重寫序列化和反序列化方法，使得可以只序列化數組中有內容的那部分數據。

```java
private transient Object[] elementData;
```

# 六、網絡操作

Java 中的網絡支持：

- InetAddress：用於表示網絡上的硬件資源，即 IP 地址；
- URL：統一資源定位符；
- Sockets：使用 TCP 協議實現網絡通信；
- Datagram：使用 UDP 協議實現網絡通信。

## InetAddress

沒有公有的構造函數，只能通過靜態方法來創建實例。

```java
InetAddress.getByName(String host);
InetAddress.getByAddress(byte[] address);
```

## URL

可以直接從 URL 中讀取字節流數據。

```java
public static void main(String[] args) throws IOException {

    URL url = new URL("http://www.baidu.com");

    /* 字節流 */
    InputStream is = url.openStream();

    /* 字符流 */
    InputStreamReader isr = new InputStreamReader(is, "utf-8");

    /* 提供緩存功能 */
    BufferedReader br = new BufferedReader(isr);

    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }

    br.close();
}
```

## Sockets

- ServerSocket：服務器端類
- Socket：客戶端類
- 服務器和客戶端通過 InputStream 和 OutputStream 進行輸入輸出。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1e6affc4-18e5-4596-96ef-fb84c63bf88a.png" width="550px"> </div><br>

## Datagram

- DatagramSocket：通信類
- DatagramPacket：數據包類

# 七、NIO

新的輸入/輸出 (NIO) 庫是在 JDK 1.4 中引入的，彌補了原來的 I/O 的不足，提供了高速的、面向塊的 I/O。

## 流與塊

I/O 與 NIO 最重要的區別是數據打包和傳輸的方式，I/O 以流的方式處理數據，而 NIO 以塊的方式處理數據。

面向流的 I/O 一次處理一個字節數據：一個輸入流產生一個字節數據，一個輸出流消費一個字節數據。為流式數據創建過濾器非常容易，鏈接幾個過濾器，以便每個過濾器只負責複雜處理機制的一部分。不利的一面是，面向流的 I/O 通常相當慢。

面向塊的 I/O 一次處理一個數據塊，按塊處理數據比按流處理數據要快得多。但是面向塊的 I/O 缺少一些面向流的 I/O 所具有的優雅性和簡單性。

I/O 包和 NIO 已經很好地集成了，java.io.\* 已經以 NIO 為基礎重新實現了，所以現在它可以利用 NIO 的一些特性。例如，java.io.\* 包中的一些類包含以塊的形式讀寫數據的方法，這使得即使在面向流的系統中，處理速度也會更快。

## 通道與緩衝區

### 1. 通道

通道 Channel 是對原 I/O 包中的流的模擬，可以通過它讀取和寫入數據。

通道與流的不同之處在於，流只能在一個方向上移動(一個流必須是 InputStream 或者 OutputStream 的子類)，而通道是雙向的，可以用於讀、寫或者同時用於讀寫。

通道包括以下類型：

- FileChannel：從文件中讀寫數據；
- DatagramChannel：通過 UDP 讀寫網絡中數據；
- SocketChannel：通過 TCP 讀寫網絡中數據；
- ServerSocketChannel：可以監聽新進來的 TCP 連接，對每一個新進來的連接都會創建一個 SocketChannel。

### 2. 緩衝區

發送給一個通道的所有數據都必須首先放到緩衝區中，同樣地，從通道中讀取的任何數據都要先讀到緩衝區中。也就是說，不會直接對通道進行讀寫數據，而是要先經過緩衝區。

緩衝區實質上是一個數組，但它不僅僅是一個數組。緩衝區提供了對數據的結構化訪問，而且還可以跟蹤系統的讀/寫進程。

緩衝區包括以下類型：

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer

## 緩衝區狀態變量

- capacity：最大容量；
- position：當前已經讀寫的字節數；
- limit：還可以讀寫的字節數。

狀態變量的改變過程舉例：

① 新建一個大小為 8 個字節的緩衝區，此時 position 為 0，而 limit = capacity = 8。capacity 變量不會改變，下面的討論會忽略它。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1bea398f-17a7-4f67-a90b-9e2d243eaa9a.png"/> </div><br>

② 從輸入通道中讀取 5 個字節數據寫入緩衝區中，此時 position 為 5，limit 保持不變。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/80804f52-8815-4096-b506-48eef3eed5c6.png"/> </div><br>

③ 在將緩衝區的數據寫到輸出通道之前，需要先調用 flip() 方法，這個方法將 limit 設置為當前 position，並將 position 設置為 0。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/952e06bd-5a65-4cab-82e4-dd1536462f38.png"/> </div><br>

④ 從緩衝區中取 4 個字節到輸出緩衝中，此時 position 設為 4。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/b5bdcbe2-b958-4aef-9151-6ad963cb28b4.png"/> </div><br>

⑤ 最後需要調用 clear() 方法來清空緩衝區，此時 position 和 limit 都被設置為最初位置。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/67bf5487-c45d-49b6-b9c0-a058d8c68902.png"/> </div><br>

## 文件 NIO 實例

以下展示了使用 NIO 快速複製文件的實例：

```java
public static void fastCopy(String src, String dist) throws IOException {

    /* 獲得源文件的輸入字節流 */
    FileInputStream fin = new FileInputStream(src);

    /* 獲取輸入字節流的文件通道 */
    FileChannel fcin = fin.getChannel();

    /* 獲取目標文件的輸出字節流 */
    FileOutputStream fout = new FileOutputStream(dist);

    /* 獲取輸出字節流的文件通道 */
    FileChannel fcout = fout.getChannel();

    /* 為緩衝區分配 1024 個字節 */
    ByteBuffer buffer = ByteBuffer.allocateDirect(1024);

    while (true) {

        /* 從輸入通道中讀取數據到緩衝區中 */
        int r = fcin.read(buffer);

        /* read() 返回 -1 表示 EOF */
        if (r == -1) {
            break;
        }

        /* 切換讀寫 */
        buffer.flip();

        /* 把緩衝區的內容寫入輸出文件中 */
        fcout.write(buffer);

        /* 清空緩衝區 */
        buffer.clear();
    }
}
```

## 選擇器

NIO 常常被叫做非阻塞 IO，主要是因為 NIO 在網絡通信中的非阻塞特性被廣泛使用。

NIO 實現了 IO 多路複用中的 Reactor 模型，一個線程 Thread 使用一個選擇器 Selector 通過輪詢的方式去監聽多個通道 Channel 上的事件，從而讓一個線程就可以處理多個事件。

通過配置監聽的通道 Channel 為非阻塞，那麼當 Channel 上的 IO 事件還未到達時，就不會進入阻塞狀態一直等待，而是繼續輪詢其它 Channel，找到 IO 事件已經到達的 Channel 執行。

因為創建和切換線程的開銷很大，因此使用一個線程來處理多個事件而不是一個線程處理一個事件，對於 IO 密集型的應用具有很好地性能。

應該注意的是，只有套接字 Channel 才能配置為非阻塞，而 FileChannel 不能，為 FileChannel 配置非阻塞也沒有意義。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/093f9e57-429c-413a-83ee-c689ba596cef.png" width="350px"> </div><br>

### 1. 創建選擇器

```java
Selector selector = Selector.open();
```

### 2. 將通道註冊到選擇器上

```java
ServerSocketChannel ssChannel = ServerSocketChannel.open();
ssChannel.configureBlocking(false);
ssChannel.register(selector, SelectionKey.OP_ACCEPT);
```

通道必須配置為非阻塞模式，否則使用選擇器就沒有任何意義了，因為如果通道在某個事件上被阻塞，那麼服務器就不能響應其它事件，必須等待這個事件處理完畢才能去處理其它事件，顯然這和選擇器的作用背道而馳。

在將通道註冊到選擇器上時，還需要指定要註冊的具體事件，主要有以下幾類：

- SelectionKey.OP_CONNECT
- SelectionKey.OP_ACCEPT
- SelectionKey.OP_READ
- SelectionKey.OP_WRITE

它們在 SelectionKey 的定義如下：

```java
public static final int OP_READ = 1 << 0;
public static final int OP_WRITE = 1 << 2;
public static final int OP_CONNECT = 1 << 3;
public static final int OP_ACCEPT = 1 << 4;
```

可以看出每個事件可以被當成一個位域，從而組成事件集整數。例如：

```java
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;
```

### 3. 監聽事件

```java
int num = selector.select();
```

使用 select() 來監聽到達的事件，它會一直阻塞直到有至少一個事件到達。

### 4. 獲取到達的事件

```java
Set<SelectionKey> keys = selector.selectedKeys();
Iterator<SelectionKey> keyIterator = keys.iterator();
while (keyIterator.hasNext()) {
    SelectionKey key = keyIterator.next();
    if (key.isAcceptable()) {
        // ...
    } else if (key.isReadable()) {
        // ...
    }
    keyIterator.remove();
}
```

### 5. 事件循環

因為一次 select() 調用不能處理完所有的事件，並且服務器端有可能需要一直監聽事件，因此服務器端處理事件的代碼一般會放在一個死循環內。

```java
while (true) {
    int num = selector.select();
    Set<SelectionKey> keys = selector.selectedKeys();
    Iterator<SelectionKey> keyIterator = keys.iterator();
    while (keyIterator.hasNext()) {
        SelectionKey key = keyIterator.next();
        if (key.isAcceptable()) {
            // ...
        } else if (key.isReadable()) {
            // ...
        }
        keyIterator.remove();
    }
}
```

## 套接字 NIO 實例

```java
public class NIOServer {

    public static void main(String[] args) throws IOException {

        Selector selector = Selector.open();

        ServerSocketChannel ssChannel = ServerSocketChannel.open();
        ssChannel.configureBlocking(false);
        ssChannel.register(selector, SelectionKey.OP_ACCEPT);

        ServerSocket serverSocket = ssChannel.socket();
        InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8888);
        serverSocket.bind(address);

        while (true) {

            selector.select();
            Set<SelectionKey> keys = selector.selectedKeys();
            Iterator<SelectionKey> keyIterator = keys.iterator();

            while (keyIterator.hasNext()) {

                SelectionKey key = keyIterator.next();

                if (key.isAcceptable()) {

                    ServerSocketChannel ssChannel1 = (ServerSocketChannel) key.channel();

                    // 服務器會為每個新連接創建一個 SocketChannel
                    SocketChannel sChannel = ssChannel1.accept();
                    sChannel.configureBlocking(false);

                    // 這個新連接主要用於從客戶端讀取數據
                    sChannel.register(selector, SelectionKey.OP_READ);

                } else if (key.isReadable()) {

                    SocketChannel sChannel = (SocketChannel) key.channel();
                    System.out.println(readDataFromSocketChannel(sChannel));
                    sChannel.close();
                }

                keyIterator.remove();
            }
        }
    }

    private static String readDataFromSocketChannel(SocketChannel sChannel) throws IOException {

        ByteBuffer buffer = ByteBuffer.allocate(1024);
        StringBuilder data = new StringBuilder();

        while (true) {

            buffer.clear();
            int n = sChannel.read(buffer);
            if (n == -1) {
                break;
            }
            buffer.flip();
            int limit = buffer.limit();
            char[] dst = new char[limit];
            for (int i = 0; i < limit; i++) {
                dst[i] = (char) buffer.get(i);
            }
            data.append(dst);
            buffer.clear();
        }
        return data.toString();
    }
}
```

```java
public class NIOClient {

    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("127.0.0.1", 8888);
        OutputStream out = socket.getOutputStream();
        String s = "hello world";
        out.write(s.getBytes());
        out.close();
    }
}
```

## 內存映射文件

內存映射文件 I/O 是一種讀和寫文件數據的方法，它可以比常規的基於流或者基於通道的 I/O 快得多。

向內存映射文件寫入可能是危險的，只是改變數組的單個元素這樣的簡單操作，就可能會直接修改磁盤上的文件。修改數據與將數據保存到磁盤是沒有分開的。

下面代碼行將文件的前 1024 個字節映射到內存中，map() 方法返回一個 MappedByteBuffer，它是 ByteBuffer 的子類。因此，可以像使用其他任何 ByteBuffer 一樣使用新映射的緩衝區，操作系統會在需要時負責執行映射。

```java
MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0, 1024);
```

## 對比

NIO 與普通 I/O 的區別主要有以下兩點：

- NIO 是非阻塞的；
- NIO 面向塊，I/O 面向流。

# 八、參考資料

- Eckel B, 埃克爾, 昊鵬, 等. Java 編程思想 [M]. 機械工業出版社, 2002.
- [IBM: NIO 入門](https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html)
- [Java NIO Tutorial](http://tutorials.jenkov.com/java-nio/index.html)
- [Java NIO 淺析](https://tech.meituan.com/nio.html)
- [IBM: 深入分析 Java I/O 的工作機制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/index.html)
- [IBM: 深入分析 Java 中的中文編碼問題](https://www.ibm.com/developerworks/cn/java/j-lo-chinesecoding/index.html)
- [IBM: Java 序列化的高級認識](https://www.ibm.com/developerworks/cn/java/j-lo-serial/index.html)
- [NIO 與傳統 IO 的區別](http://blog.csdn.net/shimiso/article/details/24990499)
- [Decorator Design Pattern](http://stg-tud.github.io/sedc/Lecture/ws13-14/5.3-Decorator.html#mode=document)
- [Socket Multicast](http://labojava.blogspot.com/2012/12/socket-multicast.html)




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
