#### 笔记本USB接口案例

1. main.java 

   ```Java
   public class Main {
       public static void main(String[] args) {
           Computer computer = new Computer();
           computer.powerOn();
   
           //准备一个鼠标
   //        Mouse mouse = new Mouse();
           //进行向上转型，就是多态写法
           USB usbMouse = new Mouse();
           //参数是USB类型，传递进去的就是USB鼠标
           computer.useDevice(usbMouse);
   
           //准备一个键盘
           KeyBoard keyBoard = new KeyBoard();   //没有使用多态写法
           //参数是USB类型，传递进去的是实现类对象。
           computer.useDevice(keyBoard);   //自动发生向上转型，小范围 -> 大范围
   
           computer.powerOff();
       }
   }
   ```

   

2. USB.java

   ```Java
   public interface USB {
       public abstract void open();
   
       public abstract void close();
   }
   ```

   

3. computer.java

   ```Java
   public class Computer {
       public void powerOn() {
           System.out.println("笔记本开机");
       }
   
       public void powerOff() {
           System.out.println("笔记本关机");
       }
   
       public void useDevice(USB usb) {
           usb.open();
           if(usb instanceof Mouse) {
               Mouse mouse = (Mouse) usb;
               mouse.click();
           } else if (usb instanceof KeyBoard) {
               KeyBoard keyBoard = (KeyBoard) usb;
               keyBoard.keyIn();
           }
   
           usb.close();
       }
   }
   
   ```

   

4. mouse.java

   ```Java
   public class Mouse implements USB {
       @Override
       public void open() {
           System.out.println("打开鼠标");
       }
   
       @Override
       public void close() {
           System.out.println("关闭鼠标");
       }
   
       public void click() {
           System.out.println("鼠标点击");
       }
   }
   
   ```

   

5. keyboard.java

   ```Java
   public class KeyBoard implements USB {
       @Override
       public void open() {
           System.out.println("打开键盘");
       }
   
       @Override
       public void close() {
           System.out.println("关闭键盘");
       }
   
       public void keyIn() {
           System.out.println("键盘输入");
       }
   }
   ```

   