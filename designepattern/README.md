# Hướng dẫn triển khai các design pattern phổ biến
Có rất nhiều design pattern có thể triển khai trong dự án, chúng ta cần xác định được nên dùng pattern nào phù hợp.
Trước khi triển khai một design pattern ta cần xác định nó là gì (__What__), sử dụng trong trường hợp nào (__When__) và cách triển khai ra sao (__How__)

## 1. Singleton pattern

### 1.1. What
`Singleton` đảm bảo chỉ duy nhất một thể hiện (instance) được tạo ra và nó sẽ cung cấp cho bạn một method để có thể truy xuất được thể hiện duy nhất đó mọi lúc mọi nơi trong chương trình.
Kiểm soát việc tạo ra các thực thể nhưng có thể lấy ra thực thế.

### 1.2. When
Sử dụng `Singleton` khi bạn tạo ra một class mà bạn chỉ muốn chỉ có duy nhất một thực thể là thể hiện của class đó và bạn có thể truy cập đến nó ở bất kỳ nơi đâu khi bạn muốn. Ví dụ như: Khi bạn tạo ra một class làm việc với file, config, class lưu collection dùng chung, hoặc database,...

### 1.3. How
1. Sử dụng `private constructor` để ngăn cho thành phần khác khởi tạo đối tượng
```java
public class Database {
  private Database();
}
```

2. Tạo thuộc tính INSTANCE có kiểu chính nó và một phương thức __static__(thuộc về quản lý của lớp) trả về giá trị thuộc tính đó.
```java
private static Database INSTANCE;
public static synchronized Database getInstance(){
   if (INSTANCE == null) {
      INSTANCE = new Database();
   }
   return INSTANCE;
}
```

Từ khóa `synchronized` để hạn chế tạo ra đối tượng mới trong trường hợp đa luồng. Khi hàm `getInstance()` đã được chạy thì bất kỳ các luồng khác phải đợi hàm này chạy xong mới gọi được.
Tuy nhiên có một nhược điểm là khi sử dụng `synchronized` sẽ chạy rất chậm và tốn hiệu năng, bất kỳ Thread nào gọi đến đều phải chờ nếu có một Thread khác đang sử dụng. Vì vậy, chúng ta cần cải tiến bằng cơ chế **Double Check Locking Singleton**
```java
private static volatile Database INSTANCE;
public static Database getInstance(){
   if (INSTANCE == null) {
     synchronized (Database.class) {
       if (INSTANCE == null) {
         INSTANCE = new Database();
       }
     }
   }
   return INSTANCE;
}
```

Chúng ta sẽ kiểm tra sự tồn tại thể hiện của lớp, với sự hổ trợ của đồng bộ hóa, hai lần trước khi khởi tạo. Phải khai báo volatile cho instance để tránh lớp làm việc không chính xác do quá trình tối ưu hóa của trình biên dịch.

## 2. Observer pattern

### 2.1. What
`Observer` định nghĩa mối quan hệ phụ thuộc một–nhiều giữa các đối tượng để khi mà một đối tượng có sự thay đổi trạng thái, tất các thành phần phụ thuộc của nó sẽ được thông báo và cập nhật một cách tự động.

### 2.2. When
Khi bạn muốn các đối tượng liên lạc với nhau. Khi đối tượng này gửi 1 thông điệp thì các đối tượng đăng ký lắng nghe thông điệp sẽ phản ứng lại với thông điệp đó. Đối tượng gửi thông điệp sẽ không cần biết nó sẽ gửi cho ai và đối tượng nhận thông điệp sẽ không cần biết ai gửi thông điệp đó.

### 2.3. How
1. Tạo ra một interface chứa các phương thức sẽ phản ứng khi có sự thay đổi.
```java
public interface Observer {    
   // phương thức phản ứng lại khi nhận được thông báo.
   void update(String message);    
}
```

2. Đối tượng triển khai interface `Observer`
```java
public class Customer implements Observer {    
    private String name;
    private int age;
 
    public Customer(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }
 
    @Override
    public void update(String message) {
        System.out.println(name + " " + message);
    }
}
```

3. Tạo một interface với các phương thức, cho phép đối tượng `Observer` đăng ký phản ứng, hủy đăng ký nhận, và thông báo đến tất cả các đối tượng `Observer` đã đăng ký
```java
public interface Subject {    
    public void attachObserver(Observer observer);// thêm đối tượng đăng ký lắng nghe thông báo.
 
    public void detachObserver(Observer observer);// hủy đối tượng đăng ký lắng nghe thông báo
 
    public void notifyObserver();// thong bao đến tất cả các đối tượng đã đăng ký thông báo.
}
```
4. Đối tượng triển khai interface `Subject`
```java
public class Product implements Subject {    
    private List<Observer> obs = new ArrayList<Observer>();
    private String nameProduct;
 
    public Product(String nameProduct) {
        super();
        this.nameProduct = nameProduct;
    }
 
    @Override
    public void attachObserver(Observer observer) {
        obs.add(observer);
    }
 
    @Override
    public void detachObserver(Observer observer) {
        obs.remove(observer);
    }
 
    @Override
    public void notifyObserver() {
        for (Observer ob : obs) {
            ob.update(nameProduct);
        }
    }
}
```
5. Triển khai
```java
public class Main {    
    public static void main(String[] args) {
         
        Customer cus1 = new Customer("Ti", 11);
        Customer cus2 = new Customer("Teo", 12);
        Product product1 = new Product("Laptop");
        product1.attachObserver(cus1); // cus1 đăng ký phản ứng khi có thông báo từ product
        product1.attachObserver(cus2);
        product1.notifyObserver(); // thông báo đến tất cả các Observer.
    }
}
```

## 3. Builder pattern

### 3.1. What
`Builder Pattern` xây dựng một đối tượng phức tạp bằng cách sử dụng các đối tượng đơn giản và sử dụng tiếp cận từng bước, việc xây dựng các đối tượng đôc lập với các đối tượng khác.

### 3.2. When
Sử dụng `Builder` khi muốn xây dựng đối tượng phức tạp nhưng chỉ cần thông qua các câu lệnh đơn giản để tác động nên các thuộc tính của nó.

### 3.3. How
1. Tạo lớp đối tượng với các phương thức getter cho các thuộc tính
```java
public class Computer {
  // required parameters
  private String HDD;
  private String RAM;
  // optional parameters
  private boolean isGraphicsCardEnabled;
  private boolean isBluetoothEnabled;
  
  public String getHDD() {
    return HDD;
  }
  
  public String getRAM() {
    return RAM;
  }
  
  public boolean isGraphicsCardEnabled() {
    return isGraphicsCardEnabled;
  }
  
  public boolean isBluetoothEnabled() {
    return isBluetoothEnabled;
  }
}
```
2. Tạo một `static nested class` (builder class) và copy tất cả các tham số từ class bên ngoài vào.
```java
public class Computer {
  ...
  public static class ComputerBuilder {
    // required parameters
    private String HDD;
    private String RAM;
    // optional parameters
    private boolean isGraphicsCardEnabled;
    private boolean isBluetoothEnabled;
    
    public ComputerBuilder(String hdd, String ram) {
      this.HDD = hdd;
      this.RAM = ram;
    }
  }
}
```
3. Tạo `private constructor` cho lớp đối tượng với tham số là class Builder
```java
public class Computer {
  ...
  
  private Computer(ComputerBuilder builder) {
    this.HDD = builder.HDD;
    this.RAM = builder.RAM;
    this.isGraphicsCardEnabled = builder.isGraphicsCardEnabled;
    this.isBluetoothEnabled = builder.isBluetoothEnabled;
  }
  ...
}
```
4. Thực hiện xây dựng từng thuộc tính cho class Builder và trả về đối tượng bằng phương thức `build()`
```java
public class Computer {
  ...
  public static class ComputerBuilder {
  
  ... // Các thuộc tính
  
    public ComputerBuilder(String hdd, String ram) {
        this.HDD = hdd;
        this.RAM = ram;
    }
    
    public ComputerBuilder setGraphicsCardEnabled(boolean isGraphicsCardEnabled) {
        this.isGraphicsCardEnabled = isGraphicsCardEnabled;
        return this;
     }
     
    public ComputerBuilder setBluetoothEnabled(boolean isBluetoothEnabled) {
      this.isBluetoothEnabled = isBluetoothEnabled;
      return this;
    }

      public Computer build() {
      return new Computer(this);
    }
  }
  ```
  5. Tạo đối tượng bằng Builder
  ```java
  Computer computer = new Computer.ComputerBuilder("500 GB", "2 GB")
                                  .setBluetoothEnabled(true)
                                  .setGraphicsCardEnabled(true)
                                  .build();
```
## 4. MVC Pattern
### 4.1. What
MVC Pattern là viết tắt của Model-View-Controller. Mô hình này được sử dụng để thể hiện thành phần ứng dụng riêng biệt.
* Model - Model đại diện cho một đối tượng hoặc JAVA POJO mang dữ liệu. Nó cũng có thể có logic để cập nhật bộ điều khiển nếu thay đổi dữ liệu của nó.
* View - View đại diện cho thành phần dữ liệu.
* Controller - Điều khiển hoạt động trên cả hai Model & View. Nó kiểm soát các luồng dữ liệu vào mô hình đối tượng và cập nhật xem bất cứ khi nào thay đổi dữ liệu.

### 4.2. When
Sử dụng `MVC Pattern` khi muốn phân tầng riêng biệt các thành phần của ứng dụng
### 4.3. How
1. Model
```java
public class Student {
}
```
2. View
```java
public class StudentView {
    public void display(Student student) {
    
    }
}

3. Controller
```java
public class StudentController {
   private Student model;
   private StudentView view;
 
   public StudentController(Student model, StudentView view){
      this.model = model;
      this.view = view;
   }
   
   public void updateView(){                
      view.display(model.getStudent()));
   }    
}
```
4. Triển khai
```java
Student model  = getFromDatabase();
StudentView view = new StudentView();
StudentController controller = new StudentController(model, view);
controller.updateView();
```
