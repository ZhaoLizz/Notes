# 一.File类

- 既可以指文件,也指文件夹,可以通过方法盘对

  - `file.isFile()`
  - `file.isDirectory()`

## 目录列表器list,listFiles

- 它既能代表**一个特定文件的名称**,又能代表**一个目录下的一组文件的名称**

  - 如果它指一个文件集,就可以对此集合调用`list()`,这个方法返回一个**String[]**
  - `File.list(FilenameFilter)`可以对文件名过滤,返回String[]
  - `File.listFiles(FileFilter)`返回`File[]`

```java
public class Main {
    public static void main(String[] args) {
        File path = new File(".");

        String[] list;

        if (args.length == 0) {
            list = path.list();
        } else {
            list = path.list(filter("txt"));
        }
        if (list.length != 0) {
            Arrays.sort(list);
        }

        for (String dirItem : list) {
            System.out.println(dirItem);
        }
    }

    //也可以直接在list(new ...)匿名内部类参数取代此方法
    public static FilenameFilter filter(final String type) {
        return new FilenameFilter() {
            @Override
            public boolean accept(File dir, String name) {
                return (name != null && name.toLowerCase().endsWith(type));
            }
        };
    }
}
```

## 目录(文件夹) 的检查和创建

- 检查: `file.exists()`
- 删除: `file.delete()`
- 创建: `file.mkdir() / mkdirs()`

```java

//在根目录创建 newMade文件夹

        File file = new File("./newMade");
        System.out.println(file.mkdir());

        File file1 = new File("./1/2/3");
        System.out.println(file1.mkdir());   //false
        System.out.println(file1.mkdirs());  //true
```

# 二.输入和输出

- 任何自`InputStream 或 Reader`派生而来的类都含有名为`read()`的方法用于读取**单个字节或字节数组**
- 自我独立的类`RandomAccessFile`适用于大小已知的文件
- 任何自`OutputStream 或 Writer`派生而来的类都含有`write()`方法用于**写单个字节或者字节数组**

  - `InputStream 和 OutputStream`常用于**面向字节形式**的IO,可以以字节的方式读取任何形式的数据
  - `Reader 和 Writer`常用于**面向字符**的IO
  - 尽量使用Reader 和 Writer

- **close()方法最好放在finally中处理**

## 输入

### 1\. Reader类: BufferedReader的 readLine()缓冲输入文件

- `利用BufferedReader包装`的readLine方法可以缓冲按行输入

```java
//缓冲输入文件
    public static String read(String filename) throws IOException {
        //Reading input by lines

        //BufferedReader in = new BufferedReader(new FileReader(new File(filename).getAbsoluteFile()));
        BufferedReader in = new BufferedReader(new FileReader(filename));
        String s;
        StringBuilder sb = new StringBuilder();

        while ((s = in.readLine()) != null)
            sb.append(s + '\n');
        in.close();

        return sb.toString();
    }
```

### 2\. Reader类: 直接使用read()从内存输入

- 不使用`BufferedReader包装`的情况下只能通过`read`按每个字节输入

```java
//从内存按照字节输入
    public static void memoryInput(String filename) {
        try {
            //从文件中按字符读取,保存为String
            String s = read(filename);

            //把字符串按照字节形式读取,read是以int形式返回下一字节
            StringReader in = new StringReader(s);
            int c;
            while ((c = in.read()) != -1) {
                System.out.print((char) c);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

### 3\. InputStream类: DataInputStream格式化的内存 字节 输入

- `DataInputStream`是一个**面向字节的IO类**
- 可以通过`InputStream`类以字节形式读取任何数据

```java
//格式化的内存字节输入
    public static void formattedMemoryInput(String filename) throws IOException {
        //把通过read以BufferedReader包装FileReader按 字符 读取到的String
        //转换为字节数组的形式
        byte[] b = read(filename).getBytes();

        DataInputStream in = new DataInputStream(new ByteArrayInputStream(b));


        while (in.available() != 0) {
            System.out.print((char) in.readByte());
        }
    }
```

## 输出

### Writer: 基本的文件输出

- 输出时只能输出**字节 或 缓冲的line字节**,因此要先利用`Reader类`读取要输出的数据(比如String)

```java
public static void basicFileOutput(String s,String filename) throws IOException {
        BufferedReader in = new BufferedReader(new StringReader(s));

        PrintWriter writer = new PrintWriter(new BufferedWriter(new FileWriter(filename)));
        int lineCount = 1;
        String lineString;

        while ((lineString = in.readLine()) != null) {
            writer.println(lineCount++ + ": " + lineString);
        }

        writer.close();
    }
```

- **txt文本文件**(且只适用于txt文本文件)**输出的快捷方式**

```java
//文本文件的输出快捷方式
    public static void fileOutputShortCut(String s, String filename) throws IOException {
        BufferedReader in = new BufferedReader(new StringReader(s));

        PrintWriter writer = new PrintWriter(filename);
        int lineCount = 1;
        String lineString;

        while ((lineString = in.readLine()) != null) {
            writer.println(lineCount++ + ": " + lineString);
        }

        writer.close();
    }
```

### OutputStream: 存储数据 和 InputStream 恢复数据

- 为了输出可供另一个流恢复的数据,需要用`DataOutputStream`写入数据,并用`DataInputStream`恢复数据
- 这两个方法都是面向**字节**的

```java
// 存储和恢复数据
    public static void storingAndRecoveringData(String filename) throws IOException {
        //存储数据
        DataOutputStream out = new DataOutputStream(new BufferedOutputStream(new FileOutputStream(filename)));
        out.writeDouble(Math.PI);
        out.writeUTF("That is PI");
        out.write("shit".getBytes());
        out.close();

        //读取数据
        DataInputStream in = new DataInputStream(new BufferedInputStream(new FileInputStream(filename)));
        System.out.println(in.readDouble());
        System.out.println(in.readUTF());
        while (in.available() != 0) {
            System.out.print((char) in.readByte());
        }

    }
```

#### 实用类模板

```java
/**
 * 用于简化对文件的读写操作,并可以用一个ArrayList保存文件的若干行
 */
public class TextFile extends ArrayList<String> {
    public TextFile(String filename, String splitter) {
        super(Arrays.asList(read(filename).split(splitter)));

        if (get(0).equals("")) {
            this.remove(0);
        }
    }

    public TextFile(String filename) {
        this(filename, "\n");
    }

    public static String read(String filename) {
        StringBuilder sb = new StringBuilder();
        try {
            BufferedReader in = new BufferedReader(new FileReader(new File(filename).getAbsoluteFile()));
            try {
                String s;
                while ((s = in.readLine()) != null) {
                    sb.append(s);
                    sb.append("\n");
                }
            }finally {
                in.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException();
        }

        return sb.toString();
    }

    public static void write(String filename, String text) {
        try {
            PrintWriter out = new PrintWriter(new File(filename).getAbsoluteFile());
            try {
                out.print(text);
            }finally {
                out.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException();
        }
    }

    public void write(String filename) {
        try {
            PrintWriter out = new PrintWriter(new File(filename).getAbsoluteFile());
            try {
                for (String item : this) {
                    out.println(item);
                }
            } finally {
                out.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException();
        }
    }

    public static void main(String[] args) {

    }

}
```
