## 1. InputStream转化为String
### 1.1 JDK原生提供

方法1：
```java
byte[] bytes = new byte[0];
bytes = new byte[inputStream.available()];
inputStream.read(bytes);
String str = new String(bytes);
```

方法2：
```java
String result = new BufferedReader(new InputStreamReader(inputStream))
        .lines().collect(Collectors.joining(System.lineSeparator()));
```

方法3：
```java
String result = new BufferedReader(new InputStreamReader(inputStream))
       .lines().parallel().collect(Collectors.joining(System.lineSeparator()));
```

方法4：
```java
Scanner s = new Scanner(inputStream).useDelimiter("\\A");
String str = s.hasNext() ? s.next() : "";
```

方法5：
```java
String resource = new Scanner(inputStream).useDelimiter("\\Z").next();
```

方法6：
```java
StringBuilder sb = new StringBuilder();
String line;

BufferedReader br = new BufferedReader(new InputStreamReader(inputStream));
while ((line = br.readLine()) != null) {
    sb.append(line);
}
String str = sb.toString();
```

方法7：
```java
ByteArrayOutputStream result = new ByteArrayOutputStream();
byte[] buffer = new byte[1024];
int length;
while ((length = inputStream.read(buffer)) != -1) {
    result.write(buffer, 0, length);
}
String str = result.toString(StandardCharsets.UTF_8.name());
```

方法8：
```java
BufferedInputStream bis = new BufferedInputStream(inputStream);
ByteArrayOutputStream buf = new ByteArrayOutputStream();
int result = bis.read();
while(result != -1) {
    buf.write((byte) result);
    result = bis.read();
}
String str = buf.toString();
```

### 1.2 Apache Common提供

方法9：
```java
StringWriter writer = new StringWriter();
IOUtils.copy(inputStream, writer, StandardCharsets.UTF_8.name());
String str = writer.toString();
```

方法10：
```java
String str = IOUtils.toString(inputStream, "utf-8");
```
### 1.3 Google Guava提供

方法11：
```java
String str = CharStreams.toString(new InputStreamReader(inputStream, StandardCharsets.UTF_8));
```

方法12：
```java
String str = new String(ByteStreams.toByteArray(inputStream));
```
针对一个2MB的文件的输入流，多次执行测试如下(单位是毫秒)：

- 方法10: 111 
- 方法11: 236 
- 方法12: 36 
- 方法1: 36 
- 方法2: 87 
- 方法3: 66 
- 方法4: 101 
- 方法5: 178 
- 方法6: 40 
- 方法7: 21 
- 方法8: 107 
- 方法9: 31

从上述结果来看，方法7和方法9更好一些，而方法5和方法11会更差一些。


## 2. String转化为InputStream

### 2.1 JDK原生提供

方法1：
```java
InputStream is = new ByteArrayInputStream(str.getBytes());
```

### 2.2 Apache Common提供

方法2：
```java
InputStream targetStream = IOUtils.toInputStream(str, StandardCharsets.UTF_8.name());
```

### 2.3 Google Guava提供

方法3：
```java
InputStream targetStream =
        new ReaderInputStream(CharSource.wrap(str).openStream(), StandardCharsets.UTF_8.name());
```




















