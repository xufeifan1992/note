## Final关键字作用



* 最终类：表示类不可以被继承
* 修饰方法：表示方法不能被重写
* 修饰变量：必须初始化，并且不能被修改

------

1. 修饰类变量，只能在静态初始化块中指定初始值或者声名该类变量时指定初始值
2. 修饰成员变量，可以在非静态初始化块，声名该变量或者构造器中执行初始值
3. 修饰局部变量，系统不会为局部变量初始化，局部变量必须由程序员显示初始化，因此使用final修饰局部变量时，即可以在定义时指定默认值，也可以不指定默认值，而在后面的代码中对final变量赋初始值
4. 修饰基本类型数据和引用数据
   1. 如果是基本数据类型的类变量，则其数值一旦在初始化后便不能再修改
   2. 如果是引用类型的变量，则在对其初始化之后便不能让其在指向另一个对象，但是**引用的值是可变的**

------

* 为什么局部内部类和匿名内部类只能访问局部final变量

  ​     

  ​       

  **编译后会生成两个class文件，Test.class和Test1.class**

  ```java
  package singel.test;
  
  public class Test {
  
      public static void main(String[] args) {
      }
  
      public void test(final int b) {
          final int a = 10;
          //匿名内部类
          new Thread() {
              public void run() {
                  System.out.println(a);
                  System.out.println(b);
              }
          }.start();
      }
  }
  
  class outClass {
  
      private int age = 12;
  
      public void outPrint(final int x) {
          class inClass {
              public void print() {
                  System.out.println(x);
                  System.out.println(age);
              }
          }
      }
  
  }
  
  ```

  ​    

     

**内部类和外部类是属于同一个级别的，内部类不会因为定义在方法中就随着方法的执行完毕就被销毁**

**这里就会产生一个问题：当外部类的方法结束时，局部变量就被销毁了，但是内部类对象还可能存在(只有没有引用才可能死亡)，这里出现一个矛盾：内部类对象访问一个不存在的变量。为了解决这个问题，就将局部变量复制一份作为内部类的局部变量，这样当局部变量死亡后，内部变量依旧可以访问，实际访问的是局部变量的copy**

**将局部变量复制为内部类的成员变量时，必须保证这两个变量时一致的，如果内部类修改了成员变量，那么方法中局部变量也需要变化，所以需要设置为final，使局部变量和内部类建立的拷贝一致**

