# 1. Quy tắc sử dụng comment
Sử dụng `/** Document comments */` để tạo Javadoc document cho code (Người sử dụng có thể sử dụng phím tắt Ctrl + Q trong Android Studio để đọc document).

Sử dụng `/* Code block */` để comment một đoạn code.

Sử dụng `//` để comment dòng code.

Tất cả các method public nên có Javadoc document để mô tả công việc mà nó thực thi.
```Kotlin
/**
 * Hàm sử dụng để chuyển đổi đối tượng từ kiểu dữ liệu bất kì thành String
 */
fun toString(any:Any) {
    ...
}
```

# 2. Quy tắc Kotlin code
## 1.2 Quy tắc đặt tên
### 1.2.1: Đặt tên package và tên class
Đặt tên package và classs tuân thủ các quy tắc sau:
*  Tên của class luôn luôn viết thường và không sử dụng dấu gạch dưới (ví dụ: `org.example.project`). Sử dụng tên có nhiều từ không được khuyến khích, nhưng nếu cần sử dụng tên có nhiều từ thì viết thường và ghép các từ lại.

* Tên class và object nên bắt đầu với một chữ cái viết hoa và sử dụng camel case
``` kotlin
open class DeclarationProcessor { /*...*/ }

object EmptyDeclarationProcessor : DeclarationProcessor() { /*...*/ }
```

### 1.2.2: Đặt tên function

Tên các function, thuộc tính, và local variable nên bắt đầu với một chữ cái viết thường và sử dụng camel case và không có dấu gạch dưới:
```kotlin
open class DeclarationProcessor { /*...*/ }

object EmptyDeclarationProcessor : DeclarationProcessor() { /*...*/ }
```
**Tên của test method**
Trong test (và chỉ trong test) có thể chấp nhận tên có dấu gạch dưới và spaces enclosed in backticks
```Kotlin
class MyTestCase {
     @Test fun `ensure everything works`() { /*...*/ }
     
     @Test fun ensureEverythingWorks_onAndroid() { /*...*/ }
}
```
### 1.2.3: Đặt tên thuộc tính
- Đối với hằng số (sử dụng const, hoặc top-level hoặc val properties mà không custom phương thức get nghĩa là thuộc tính giữ chỉ một giá trị) thì sử dụng chữ viết in hoa và có dấu gạch dưới để ngăn cách các từ:

```kotlin
const val MAX_COUNT = 8
val USER_NAME_FIELD = "UserName"
```
Ngoài ra tên của hằng số cần có các tiền tố như sau:
Một số đối tượng trong Android như SharedPreferences, Bundle, Intent sử dụng cặp key-value
Khi sử dụng các thành phần này bạn cần định nghĩa các keys như là `static final`:

| Đối tượng            | Tiền tố |
| -----------------  | ----------------- |
| SharedPreferences  | `PREF_`             |
| Bundle             | `BUNDLE_`           | 
| Fragment Arguments | `ARGUMENT_`         |   
| Intent Extra       | `EXTRA_`            |
| Intent Action      | `ACTION_`           |

- Đối với tên của top-level hoặc object properties giữ các đối tượng mà behavior hoặc data có thể thay đổi đượng thì sử dụng camel case:
```Kotlin
val mutableCollection: MutableSet<String> = HashSet()
```

- Đối với thuộc tính biến bình thường nên bắt đầu với một chữ cái viết thường và sử dụng camel case và không có dấu gạch dưới:
```kotlin
var name:String="Name"
```

- Đối với tên của thuộc tính giữ tham chiếu đến đối tượng là Singleton thì nên khai báo như khai báo `object`

```Kotlin
val PersonComparator: Comparator<Person> = /*...*/
```
## 1.3. Sử dụng ngoặc kép chuẩn trong java
```kotlin
class MyClass {
    fun func() {
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
```kotlin
if (condition) body();
```

Không nên
```kotlin
if (condition)
    body();  // bad!
```
## 1.2. Thứ tham số cho phương thức
Nếu có, `Context` phải là tham số đầu tiên và `Callback` luôn là tham số cuối cùng.

```kotlin
fun loadFonts(context:Context, userId:Int, int:FontCallback);
```

## 1.3. Quy tắc check đối với kiểu dữ liệu nullable
Đối với kiểu dữ liệu nullable tuyệt đối không được tùy tiện force not null (sử dụng `!!`), trước khỉ force not null cần phải check null trước
Không tốt 
```Kotlin
var varaible:Int?
function(variable!!)
```

Tốt:
```Kotlin
var varaible:Int?
if(variable!=null){
  function(variable!!)
}
```
Hoặc:
```Kotlin
var varaible:Int?
variable?.let{
  function(it)
```

## 1.4: Sử dụng các scope function
Sử dụng các scope function để code clean hơn:
Cách sử dụng có thể xem tại Xem tại [đây](https://viblo.asia/p/kotlin-scope-function-let-apply-run-also-with-bWrZnNqwZxw)

## 1.5: Quy tắc về khoảng trắng
Sử dụng 4 khoảng trắng thụt vào đầu dòng. Không sử dụng tab
Xem cách setting Android Studio để sửa ở [đây](https://viblo.asia/p/kotlin-coding-conventions-se-khac-voi-mac-dinh-cua-intelij-idea-nhu-the-nao-ByEZkOJqZQ0)



# 3. Quy tắc XML code
### 3.1. Tự đóng thẻ nếu không có nội dung
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

### 3.2. Đặt tên resources và ID
Xem tại [đây](https://github.com/LY-NA/AndroidCodingRules/tree/master/general#1-c%C3%A1ch-%C4%91%E1%BA%B7t-t%C3%AAn-files)

### 3.3. Đặt tên strings
Tên chuối nên bắt đầu bằng một định danh. Ví dụ `registration_email_hint` hoặc `registration_name_hint`. 
Có thể tham khảo quy ước sau:

| Tiền tố             | Mô tả                           |
| -----------------  | --------------------------------------|
| `error_`             | Cho thông báo lỗi                   |
| `msg_`               | Cho một thông báo hoặc tin nhắn         |       
| `title_`             | Cho tiêu đề, vd tiêu đề dialog, activity         | 
| `action_`            | Hành vi như `Lưu`, `Sửa` , `Xóa`  |

### 3.4. Thứ tự thuộc tính
Các thuộc tính trong XML nên được sắp xếp theo quy ước sau:
1. View Id
2. Style
3. Layout width and layout height
4. Các thuộc tính khác sắp xếp theo bảng chữ cái

 
