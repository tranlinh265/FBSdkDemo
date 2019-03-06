# Login
Tích hợp Facebook login thông qua FacebookSDK

## Bước thực hiện
### Tạo project
Tạo một project android và ghi nhớ tên package name.

### Đăng ký ứng dụng với facebook
Đăng nhập vào trang [Facebook Developer](https://developers.facebook.com/apps/), nên sử dụng 1 tài khoản facebook riêng cho sản phẩm (nếu có ý định sẽ chuyển nhượng cho ng khác :)) ). Tài khoản sử dụng cần phải được xác thực với facebook bằng số điện thoại.

Chọn CreateApp, điền thông tin về Display Name, Contact Email, Categoty. Lưu ý Display Name không được phép chưa các cụm như Face, FB, Facebook, Instagram, ... Sau khi điền đầy đủ thông tin, chọn Create App ID, và xác nhận k phải là robot.

Ở màn hình tiếp theo, chọn Settings ->Basic. Một số việc cần làm: note lại App ID vì sau này sẽ sử dụng trong app, thiết lập 1 số thông tin cho phần Platform/Android.

Chọn Add Platform -> Android và điền các thông tin về platform Android như Google Play Package Name (đã có ở bước tạo project), Class Name (Class mà bạn sẽ implement button login facebook), Key Hashes.

Về Key Hashes, nên thêm đoạn code này trong MainActivity và lấy trong Log khi khởi chạy ứng dụng 
```java
import android.content.pm.PackageInfo;
import android.content.pm.PackageManager;
import android.content.pm.Signature;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
```

```java
public void printHashKey() {
    try {
        PackageInfo info = getPackageManager().getPackageInfo(getPackageName(), PackageManager.GET_SIGNATURES);
        for (Signature signature : info.signatures) {
            MessageDigest md = MessageDigest.getInstance("SHA");
            md.update(signature.toByteArray());
            String hashKey = new String(Base64.encode(md.digest(), 0));
            Log.i(TAG, "printHashKey() Hash Key: " + hashKey);
        }
    } catch (NoSuchAlgorithmException e) {
        Log.e(TAG, "printHashKey()", e);
    } catch (Exception e) {
        Log.e(TAG, "printHashKey()", e);
    }
}
 ```
Sau khi lấy được Key Hash, thêm vào phần Key Hashes. Key này khá quan trọng, dựa vào key để xác định máy đã build project. Nếu không đúng key, sẽ không thể login, và nhận được cảnh báo:
```
Invalid key hash. The key hash XXXXXXXX does not match any stored key hashes. Configure you app key hashes at https://developers.facebook.com/apps/XXXXX/
```

## Thêm Facebook SDK vào project
Thêm vào build.gradle
```java
repositories {
    mavenCentral()
}
```
Thêm vào app/build.gradle
```java
implementation 'com.facebook.android:facebook-android-sdk:4.40.0'
```

## Tích hợp Facebook Login SDK
Sửa AndroidManifest.xml
```java 
<uses-permission android:name="android.permission.INTERNET"/>

<application
    ...
    <meta-data
        android:name="com.facebook.sdk.ApplicationId"
        android:value="@string/facebook_app_id"/>
</application>
```
Thêm vào strings.xml
```java
<string name="facebook_app_id">YOUR_FACEBOOK_APP_ID</string>
```
Thêm Widget LoginButton vào file layout, cụ thể ở đây tôi đã sửa file [activity_main.xml]( https://github.com/tranlinh265/FBSdkDemo/blob/dev_demo_login_sdk/app/src/main/res/layout/activity_main.xml)

Implement view, callback vào file class, cụ thể có thể xem trong [MainActivity.class](https://github.com/tranlinh265/FBSdkDemo/blob/dev_demo_login_sdk/app/src/main/java/com/bk/tranlinh/fbsdkdemo/MainActivity.java)

Lưu ý: nếu implement trong fragment thì nên có thêm setFragment() cho login button

## Chạy thử và hưởng thụ thành quả

## Custom nút login

Sử dụng 1 số thuộc tính cơ bản để thay đổi background hay testSize của nút
```java
<style name="FacebookLoginButton">
    <item name="android:textSize">@dimen/fbBtnTextSize</item>
    <item name="android:background">@dimen/fbBtnBackgroundColor</item>
</style>
```
Hoặc thay đổi login_text, logout_text
```java
<com.facebook.login.widget.LoginButton 
    ...
    style="@style/FacebookLoginButton"
    xmlns:facebook="http://schemas.android.com/apk/res-auto"
    facebook:com_facebook_login_text="hihih "
    facebook:com_facebook_logout_text="haha" />
```

Hoặc đơn giản nhất, tạo 1 Custom Button và xử lý trong sự kiện onClick của nó
```java
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:id="@+id/custom_login_btn"/>
```

```java
protected void onCreate(Bundle savedInstanceState) {
    ...
    Button customBtn = (Button) findViewById(R.id.custom_login_btn);
    customBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            mBtnLoginFacebook.callOnClick();
        }
    });
    ...
}
```
