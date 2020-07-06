# Quy tắc chung viết code Java
Tài liệu được tham khảo trực tiếp từ [AOSP Java Code Style](https://source.android.com/setup/contribute/code-style)

## 1. Ngoại lệ nên được xử lý
### 1.1 Không nên bắt ngoại lệ mà không xử lý
```java
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}
```
Mặc dù bạn có thể nghĩ rằng code của mình sẽ không bao giờ gặp phải tình trạng lỗi này hoặc việc xử lý nó không quan trọng, nhưng việc bỏ qua loại ngoại lệ này sẽ tạo ra các bug tiềm ẩn trong code của bạn.

Có một số cách xử lý ngoại lệ như sau:

### 1.2. Ném ngoại lệ cho người gọi phương thức của bạn
```java
  void setServerPort(String value) throws NumberFormatException {
      serverPort = Integer.parseInt(value);
  }
  ```
  
### 1.3. Xử lý ngoại lệ trong khối ```catch {}```
```java
  /** Set port. If value is not a valid number, 80 is substituted. */

  void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        serverPort = 80;  // default port for server
    }
  }
  ```
  
  ### 1.4. Nếu tự tin bỏ qua ngoại lệ không tạo ra bug tiềm ẩn thì bạn nên comment giải thích với lý do phù hợp
  ```java
  /** If value is not a valid number, original port number is used. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        // Method is documented to just ignore invalid user input.
        // serverPort will just be unchanged.
    }
}
```

## 2. Không nên bắt ngoại lệ quá chung
```java
  try {
      someComplicatedIOFunction();        // may throw IOException
      someComplicatedParsingFunction();   // may throw ParsingException
      someComplicatedSecurityFunction();  // may throw SecurityException
      // phew, made it all the way
  } catch (Exception e) {                 // I'll just catch all exceptions
      handleError();                      // with one generic handler!
  }
  ```
  
Nên xử lý từng ngoại lệ theo từng cách riêng. Xem thêm tại [đây](https://source.android.com/setup/contribute/code-style#dont-catch-generic-exception).
  
## 3. Chỉ nên import các thành phần được sử dụng
Ví dụ nếu bạn chỉ muốn sử dụng class ```Bar``` trong package ```foo```

Không nên : ```import foo.*;```

Nên       : ```import foo.Bar;```

## 4. Quy tắc sử dụng comments
Sử dụng ```/** Document comments */ ``` để tạo Javadoc document cho code (Người sử dụng có thể sử dụng phím tắt Ctrl + Q trong Android Studio để đọc document).

Sử dụng ```/* Code block */``` để comment một đoạn code.

Sử dụng ```//``` để comment dòng code.

Tất cả các **method public** nên có Javadoc document để mô tả công việc mà nó thực thi.
```java
/**
 * Constructs a new String by converting the specified array of
 * bytes using the platform's default character encoding.
 */
public String(byte[] bytes) {
    ...
}
```
Bạn có thể tham khảo thêm về cách sử dụng comment tại [đây](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html#implementationspecific).

## 5. Quy tắc Java code
### 5.1. Quy ước đặt tên các trường
Các trường cần được định nghĩa ở đầu file và tuân theo cú pháp đặt tên như sau:
* Non-public, non-static tên trường bắt đầu bằng chữ ```m```.
* static tên trường bắt đầu bằng chữ ```s```.
* Các trường hợp khác bắt đầu bằng chữ viết thường(lower case).
* Public static final là một hằng số được đặt tên theo định dạng ```ALL_CAPS_WITH_UNDERSCORES```.

```java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    public int publicField;
    private static MyClass sSingleton;
    int mPackagePrivate;
    private int mPrivate;
    protected int mProtected;
}
```

### 5.2. Sử dụng ngoặc kép chuẩn trong java
```java
class MyClass {
    int func() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}
```

Trường hợp không sử dụng ngoặc kép:
Nên
```java
if (condition) body();
```

Không nên
```java
if (condition)
    body();  // bad!
```

### 5.3. Quy ước đặt tên biến cho một số từ viết tắt
| Good           | Bad            |
| -------------- | -------------- |
| `XmlHttpRequest` | `XMLHTTPRequest` | 
| `getCustomerId`  | `getCustomerID`  | 
| `String url`     | `String URL`     |
| `long id`        | `long ID`        |

### 5.4. Cách đặt tên constant
Một số đối tượng trong Android như SharedPreferences, Bundle, Intent sử dụng cặp key-value
Khi sử dụng các thành phần này bạn cần định nghĩa các keys như là `static final`:

| Đối tượng            | Tiền tố |
| -----------------  | ----------------- |
| SharedPreferences  | `PREF_`             |
| Bundle             | `BUNDLE_`           | 
| Fragment Arguments | `ARGUMENT_`         |   
| Intent Extra       | `EXTRA_`            |
| Intent Action      | `ACTION_`           |

Ví dụ:

```java

// Giá trị của biến giống như tên để tránh sự trùng lặp
static final String PREF_EMAIL = "PREF_EMAIL";
static final String BUNDLE_AGE = "BUNDLE_AGE";
static final String ARGUMENT_USER_ID = "ARGUMENT_USER_ID";


// Intent, tên của Action Broadcast nên sử dụng đẩy đủ tên gói như là một giá trị
static final String EXTRA_SURNAME = "com.myapp.extras.EXTRA_SURNAME";
static final String ACTION_OPEN_USER = "com.myapp.action.ACTION_OPEN_USER";

```

### 5.5. Giới hạn độ dài dòng code
Mỗi dòng code không nên dài quá 100 ký tự. Tuy nhiên vẫn có một số ngoại lệ chấp nhận được.

### 5.6. Thứ tham số cho phương thức
Nếu có, `Context` phải là tham số đầu tiên và `Callback` luôn là tham số cuối cùng.

```java
public void loadFonts(Context context, int userId, FontCallback callback);
```
## 6. Cách sử dụng ```Log```
`Log` là một class in ra kết quả lỗi hoặc thông tin nào đó giúp lập trình viên gỡ rối vấn đề:

* `Log.v(String tag, String msg)` (verbose)
* `Log.d(String tag, String msg)` (debug)
* `Log.i(String tag, String msg)` (information)
* `Log.w(String tag, String msg)` (warning)
* `Log.e(String tag, String msg)` (error)

Nên khai báo một TAG ở mỗi một file:

```java
public class MyClass {
    private static final String TAG = "MyClass";

    public myMethod() {
        Log.e(TAG, "Thông báo lỗi");
    }
}
```

## 7. Quy tắc XML code
### 7.1. Tự đóng thẻ nếu không có nội dung
Khi một phần tử XML không có nội dung, bạn cần phải tự đóng thẻ

Nên: 

```xml

<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
   
```

Không nên 

```xml

<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
   
```

### 7.2. Đặt tên resources và ID
Xem tại [đây](https://github.com/LY-NA/AndroidCodingRules/tree/master/general#1-c%C3%A1ch-%C4%91%E1%BA%B7t-t%C3%AAn-files)

### 7.3. Đặt tên strings
Tên chuối nên bắt đầu bằng một định danh. Ví dụ `registration_email_hint` hoặc `registration_name_hint`. 
Có thể tham khảo quy ước sau:

| Tiền tố             | Mô tả                           |
| -----------------  | --------------------------------------|
| `error_`             | Cho thông báo lỗi                   |
| `msg_`               | Cho một thông báo hoặc tin nhắn         |       
| `title_`             | Cho tiêu đề, vd tiêu đề dialog, activity         | 
| `action_`            | Hành vi như `Lưu`, `Sửa` , `Xóa`  |

### 7.4. Thứ tự thuộc tính
Các thuộc tính trong XML nên được sắp xếp theo quy ước sau:
1. View Id
2. Style
3. Layout width and layout height
4. Các thuộc tính khác sắp xếp theo bảng chữ cái
