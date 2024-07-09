### **1.数据类型和变量**

- 基本数据类型：整型如 `int age = 27`，浮点型如 `double price = 99.9`，字符类型如 `char letter = 'A'`，布尔类型如 `boolean isValid = false`.
- 引用数据类型：如 `String name = "John"`.
- 变量：局部变量，实例变量（非静态变量），类变量（静态变量）

### **2.运算符**

- 算术运算符例如：`int result = 10 + 5;`
- 关系运算符例如：`boolean isEqual = 5 == 5;`

### **3.控制语句**

- 选择结构：例如 `if (age > 18) {...}`
- 循环结构：例如 `for (int i = 0; i < 10; i++) {...}`





### **4.类和对象**

```java
    // 创建一个 Dog 类
    public class Dog {
      String breed;
      int age;
      String color;

      void barking() {
        ...
      }

      void hungry() {
        ...
      }

      void sleeping() {
        ...
      }
    }
    // 创建一个 Dog 类的对象
    Dog myDog = new Dog();
    myDog.barking();
```

### **5.封装**

```java
    // 使用 getter 和 setter 方法封装一个类的字段
    public class Student {
      private String name;

      public String getName() {
        return name;
      }

      public void setName(String name) {
        this.name = name;
      }
    }
```

### **6.继承**

```java
   // 子类 inherits the superclass
   class Animal {
     void eat() {
       System.out.println("eating...");
     }
   }

   class Dog extends Animal {
     void bark() {
       System.out.println("barking...");
     }
   }
```



### **7.多态**

```java
   // 一个方法的多种形态
   class Animal {
     void eat() {
       System.out.println("animal is eating...");
     }
   }

   class Dog extends Animal {
     //dog also eat but in different way, so we are overwriting the eat method
     void eat() {
       System.out.println("dog is eating...");
     }
   }
```



### **8.泛型**

```java

   ArrayList<String> list = new ArrayList<String>();
   list.add("Hello");
```





### **9.字符串操作**

```java
   //字符串操作
   String str = "Hello World!"；
    String replaceString = str.replace('H', 'W');  //output: "Wello World!"
```



### **10.包装类**

```java
   // 包装类
   int i = 5;
   Integer obj = Integer.valueOf(i); //converting int into Integer
```

### **11.集合框架**

```java
   // Example of Java Collections
   ArrayList<String> list = new ArrayList<String>();
   list.add("Apple");
   list.add("Banana");
   list.add("Mango");
```

### **12.异常处理**

```java
   //Example of handling exceptions
   try {
     int[] myNumbers = {1, 2, 3};
     System.out.println(myNumbers[10]);
   } catch (Exception e) {
     System.out.println("Something went wrong.");
   }
```



### 13.io流

```java
   //Example of Java File I/O
   try {
     FileWriter myWriter = new FileWriter("filename.txt");
     myWriter.write("Hello World!");
     myWriter.close();
   } catch (IOException e) {
     System.out.println("An error occurred.");
     e.printStackTrace();
   }
```



### **14.Java多线程**

**线程的创建和运行**

```java
   //创建线程
   Thread t = new Thread(new Runnable() {
     public void run() {
       // code of the thread
     }
   });
   t.start(); //启动线程
```