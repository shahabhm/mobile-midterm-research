<p align="center">
    <img src="https://blog.theodo.com/static/a7a48712e6f25b8f1f78ffc12ccd8413/46604/parse-1.png" height="250px" alt="parse server logo">
    <br/>
Parse Android SDK
    <br/>
برنامه سازی موبایل، دانشگاه صنعتی شریف
    <br/>
ارائه دهنده درس: جناب آقای امید جعفری نژاد
    <br/>
نویسندگان: آرین احدی نیا، شهاب حسینی مقدم، محمدحسین کاشانی جباری
</p>

<div dir="rtl">

## Parse Server چیست؟
پارس سرور یک Back-end آماده است که بسیاری از موارد پرکاربرد در یک وب سرویس را مانند ساز و کار های مربوط به حساب کاربری را پیاده‌سازی کرده است.
در عین حال پارس سرور قابلیت پیاده‌سازی منطق ویژه سازی شده را برای توسعه دهندگان محفوظ میدارد و توسعه دهندگان میتوانند منطق دلخواه خود را برای Web-Server در بستر پارس سرور پیاده‌سازی کنند.
برای اطلاع بیشتر، خوب است بدانید که Parse Server در بستر Node.JS پیاده‌سازی شده است.

## Parse Android SDK چیست؟
برای وصل شدن به Server های پیاده‌سازی شده با Parse، میتوانیم از راهکار های متفاوتی استفاده کنیم.
میتوانیم از HTTP request به صورت خام بهره بگیریم. یکی از مزایای Parse این است که برای اتصال به سرور، SDK هایی را در بستر های مختلف مانند Android در اختیار توسعه دهندگان قرار میدهد.
وجود چنین SDK هایی ارتباط بین Client و Server را بسیار ساده می کند.
در این نوشته، تمرکز ما بر روی Parse Android SDK و چگونگی به کار گرفتن آن در پروژه های اندرویدی است.

## ساختار نوشته 
در این نوشته سعی می کنیم به ساختار Documentation اصلی Parse Android SDK وفادار بمانیم تا ادامه مطلب در Document اصلی برای مخاطب امکان‌پذیر باشد.
تلاش بر این است که افراد بتوانند با خواندن این نوشته، Parse Android SDK را درک و در بتوانند در پروژه های خود استفاده اولیه ای از آن داشته باشند.
میتوانید از طریق
<a href="https://docs.parseplatform.org/parse-server/guide/">
این لینک
</a>
به مستندات Parse Server و از طریق
<a href="https://docs.parseplatform.org/android/guide/#relations">
این لینک
</a>
به مستندات Parse Android SDK دسترسی پیدا کنید.

## مزایای استفاده از Parse Server و SDK های آن چیست؟ 
در Parse Platform، یعنی Parse Server و SDK های آن، بسیاری از مباحث پیچیده مانند مسائل امنیتی، Caching، ارسال پیام از Server به کلاینت و ... در نظر گرفته شده است.
استفاده از این پلتفرم میتواند برنامه نویس را از پیاده‌سازی این موارد رها کند.
در مقایسه با رقبا، Parse Server از طرف شرکت بزرگی مانند Facebook حمایت میشود.
همچنین Parse Platform، متن-باز و به سادگی میتوان از طریق مخازن این پروژه در
<a href="https://github.com/parse-community">
GitHub
</a>
به Source Code پارس سرور و SDK ها دسترسی پیدا کنید.
همچنین Parse Platform دارای Community نسبتا قوی است که برای چنین پروژه ای مزیت مهمی محسوب میشود.

## چگونه Parse Android SDK را به پروژه خود اضافه کنیم؟

### مرحله اول

ابتدا اطمینان حاصل کنید که در مخزن jitpack در build.gradle پروژه (و نه ماژول) قرار دارد.

<div dir="ltr">

```
allprojects {
	repositories {
		...
		maven { url "https://jitpack.io" }
	}
}
```
</div>

### مرحله دوم 
وابستگی (dependency) مربوط به parse android sdk را به build.gradle ماژول اضافه کنید.

<div dir="ltr">

```
dependencies {
    implementation "com.github.parse-community.Parse-SDK-Android:parse:latest.version.here"
}
```
</div>

### مرحله سوم 
کلاس Application را در `AndroidManifest.xml` پروژه تعریف کنید.

<div dir="ltr">

```xml
 <application 
        android:name=".App">
   ...
 </application>
```
</div>

### مرحله چهارم 
کلاس Application را پیاده‌سازی کنید و آن تابع `onCreate` را پیاده‌سازی کنید و در آن، در آن Parse را با استفاده از Context برنامه initialize کنید.
منظور از initialize کردن، مشخص کردن آدرس سرور هدف (Target Server) و مشخص کردن مقادیر لازم برای احراز هویت Application به Server است.

<div dir="ltr">

```java
import com.parse.Parse;
import android.app.Application;

public class App extends Application { 
    @Override 
    public void onCreate() { 
        super.onCreate();
        Parse.initialize(new Parse.Configuration.Builder(this)
                .applicationId("YOUR_APP_ID")
                .clientKey("YOUR_CLIENT_KEY")
                .server("http://localhost:1337/parse/")
                .build()
        ); 
    }
}
```
</div>

اکنون Application در شروع برنامه، Parse را initial می کند و در ادامه در هر کجای برنامه میتوانید از طریق متود های static کلاس Parse، ارتباطات لازم را برقرار کنید.

## Object در Parse
برای ذخیره‌سازی اطلاعات در بستر Parse باید از ParseObject استفاده کنیم.
هر ParseObject، جفت هایی از Key-Value ها را شامل میشود.
توجه کنید که این Key-Value ها باید سازگار با JSON باشند.
بنابرین Key ها تنها میتوانند String باشند.
اما Value ها مقداری متفاوتی میتوانند داشته باشند مانند عدد، رشته، مقدار منطقی و حتی آبجکت و یا آرایه.
به شرط اینکه از شرایط JSON تبعیت کنند. مثلا آبجکت دوری نباشد.
هر ParseObject شامل یک ClassName نیز میشود تا Object ها به صورت دسته ای از هم تمییز یابند.

طبق استاندازد های جاوا و پارس، بهتر است که اسامی کلاس ها از استاندارد UpperCamelCase و کلید ها از استاندارد lowerCamelCase پیروی کنند.

### ذخیره object ها 
به شکل زیر میتوانیم یک ParseObject بسازیم.

<div dir="ltr">

```java
ParseObject gameScore = new ParseObject("GameScore");
```
</div>

و همچنین میتوانیم مقادیری دلخواهی را به عنوان argument به شکل زیر به آن نسبت بدهیم.

<div dir="ltr">

```java
gameScore.put("score", 1337);
gameScore.put("playerName", "Sean Plott");
gameScore.put("cheatMode", false);
```
</div>

در نهایت با صدا کردن تابع `saveInBackground` میتوانیم آن را به سرور ارسال و در سرور ذخیره کنیم.

<div dir="ltr">

```java
gameScore.saveInBackground();
```
</div>

در نهایت در سرور، ابجکتی مشابه شکل زیر ذخیره خواهد شد.

<div dir="ltr">

```java
objectId: "xWMyZ4YEGZ",
score: 1337,
playerName: "Sean Plott",
cheatMode: false,
createdAt:"2011-06-10T18:33:42Z",
updatedAt:"2011-06-10T18:33:42Z"
```
</div>

### دریافت object ها 
همان گونه که Object را در سرور ذخیره کردیم، با داشتن objectId یک ابجکت و دانستن اینکه متغیر مورد نظر از کدام کلاس است، میتوانیم آن متغیر را از سرور دریافت کنیم.

<div dir="ltr">

```java
ParseQuery<ParseObject> query = ParseQuery.getQuery("GameScore");
query.getInBackground("xWMyZ4YEGZ", new GetCallback<ParseObject>() {
    public void done(ParseObject object, ParseException e) {
        if (e == null) {
            // object will be your game score
        } else {
            // something went wrong
        }
    }
});
```
</div>

همچنین به سادگی میتوانیم، Value یک Key مورد نظر را در type دلخواه دریافت کنیم.

<div dir="ltr">

```java
int score = gameScore.getInt("score");
String playerName = gameScore.getString("playerName");
boolean cheatMode = gameScore.getBoolean("cheatMode");
```
</div>

جدا از Key-Value ها، 4 getter برای Field های ویژه تعریف شده اند.

<div dir="ltr">

```java
String objectId = gameScore.getObjectId();
Date updatedAt = gameScore.getUpdatedAt();
Date createdAt = gameScore.getCreatedAt();
ParseACL acl = gameScore.getACL();
```
</div>

### بروز رسانی object ها 
توجه کنید که ممکن لازم شود ک object را بروزرسانی کنیم تا بروزترین اطلاعات در سرور را به دست آوریم.
به جای آنکه دوباره آن آبجکت را با کوئری مورد نظر از سرور دریافت کنیم، میتوانیم به سادگی از متد `fetchInBackground` استفاده کنیم و آن را روی آبجکت مورد نظر صدا کنیم.

<div dir="ltr">

```java
myObject.fetchInBackground(new GetCallback<ParseObject>() {
    public void done(ParseObject object, ParseException e) {
        if (e == null) {
            // Success!
        } else {
            // Failure!
        }
    }
});
```
</div>

توجه کنید که علی الرعم معماری انطباق پذیر با آسنکرون بودن تابع و استفاده از Callback، کد درون Callback روی ترد صدا کننده اجرا میشود.

### استفاده از حافظه محلی 
در این SDK، یک local datastore تعبیه شده است که در آن اطلاعات روی خود دستگاه ذخیره می شود.
با ذخیره داده روی local datastore، چیزی به سرور ارسال نمیشود اما این datastore به طور ویژه برای ذخیره موقتی اطلاعاتی که بعدا به سرور ارسال خواهند شد مفید است.

اگر بخواهیم از این local datastore استفاده کنیم، باید که در زمان initialize کردن پارس، آن را فعال کنیم. به این صورت که پیش از صدا کردن تابع
`initialize`
در کلاس Application، تابع
`enableLocalDatastore`
را صدا بزنیم.

به سادگی و مشابه آنچه پیشتر دیدیم، میتوانیم ParseObject را روی Local Datastore ذخیره کنیم یا از Object های ذخیره شده روی آن، Query بگیریم.

<div dir="ltr">

```java
ParseObject gameScore = new ParseObject("GameScore");
gameScore.put("score", 1337);
gameScore.put("playerName", "Sean Plott");
gameScore.put("cheatMode", false);
gameScore.pinInBackground();
```
</div>

<div dir="ltr">

```java
ParseQuery<ParseObject> query = ParseQuery.getQuery("GameScore");
query.fromLocalDatastore();
query.getInBackground("xWMyZ4YEGZ", new GetCallback<ParseObject>() {
    public void done(ParseObject object, ParseException e) {
        if (e == null) {
            // object will be your game score
        } else {
            // something went wrong
        }
    }
});
```
</div>

به همین سادگی نیز میتوانیم ابجکت ها را از local datastore حذف کنیم.

<div dir="ltr">

```java
gameScore.unpinInBackground();
```
</div>

### ذخیره کردن object ها در حالت offline 
یکی از مزایای Parse، مدیریت کردن شرایط بدون اینترنت است.
ممکن است در مواقعی، بخواهیم که یک object در اولین فرصت که شرایط اینترنت مهیا شد، در سرور ذخیره شود.
در این شرایط میتوانیم از تابع `saveEventually` استفاده کنیم.
این تابع در اولین زمانی که اینترنت مهیا شود، ابجکت مورد نظر را به سرور ارسال میکند.
در این زمان اگر Application باز و بسته شود نیز اشکالی بوجود نمی آید.
اگر local datastore را در برنامه استفاده کنیم، object در زمانی که در انتظار برقراری اتصال اینترنت برای ارسال به سرور است، در local datastore ذخیره میشود.
توجه کنید که مشابه تابغ `saveEventually`، تابع `deleteEventually` نیز وجود دارد.

<div dir="ltr">

```java
ParseObject gameScore = new ParseObject("GameScore");
gameScore.put("score", 1337);
gameScore.put("playerName", "Sean Plott");
gameScore.put("cheatMode", false);
gameScore.saveEventually();
```
</div>

### تغییر object ها 
ممکن است بخواهیم در ویژگی ها یک object تغییری ایجاد کنیم و آن را در سرور ارسال کنیم.
این کار به سادگی انجام میشود.
پس از دریافت object، میتوانیم key های مورد نظر را دوباره مقدار دهی کنیم.
مقدار دهی مجدد موجب تغییر مقدار میشود.
سپس ابجکت به دست آمده را مجددا ذخیره کنیم.

<div dir="ltr">

```java
ParseQuery<ParseObject> query = ParseQuery.getQuery("GameScore");

// Retrieve the object by id
query.getInBackground("xWMyZ4YEGZ", new GetCallback<ParseObject>() {
    public void done(ParseObject gameScore, ParseException e) {
        if (e == null) {
            // Now let's update it with some new data. In this case, only cheatMode and score
            // will get sent to your Parse Server. playerName hasn't changed.
            gameScore.put("score", 1338);
            gameScore.put("cheatMode", true);
            gameScore.saveInBackground();
        }
    }
});
```
</div>

پارس به صورت خودکار، فیلدهایی را که دچار تغییر شده اند، شناسایی میکند و تنها آنها را از طریق شبکه ارسال میکند.
بنابرین نیازی به نگرانی راجب به سربار شبکه نیست.

### شمارنده ها در Parse 
تصور کنید که دو کلاینت، همزمان میخواهند در سرور یک شمارنده را افزایش دهند.
فرض کنید مقدار این متغیر در هر دو کلاینت 100، است.
هر دو پس از یک واحد افزایش یه 101 میرسند.
سپس با ارسال درخواست به سرور مقدار نهایی 101 خواهد شد.
در Parse برای متغیر های عددی، متود increment تعبیه شده است.
با فراخوانی آن برای یک کلید مشخص در یک object، مقدار آن به صورت تضمین شده یک واحد افزایش پیدا می کند.

<div dir="ltr">

```java
gameScore.increment("score");
gameScore.saveInBackground();
```
</div>

همچنین می توانیم از فرم زیر استفاده کنیم تا increment را به مقدار دلخواه انجام دهیم.
توجه کنید که میتوانیم با increment به مقدار منفی، decrement را پیاده‌سازی کنیم.

<div dir="ltr">

```java
increment("key", "amount");
```
</div>

### آرایه ها 


### حذف یک کلید از Object 


### حذف ابجکت از سرور 


### Data Types


### ارث بری از ParseObject 


### Object های مرتبط


## Query در Parse

## کاربر در Parse 
در هسته بسیاری از برنامه ها، ایده ی حساب کاربری وجود دارد که به کاربران اجازه میدهند به شیوه ای ایمن به اطلاعاتشان دسترسی داشته باشند.
ما یک کلاس مخصوص user به نام ParseUser ارائه میدهیم که به صورت خودکار بسیاری از کار های لازم برای مدیریت حساب کاربری را به دست میگیرد.

با این کلاس، شما میتوانید عملکرد حساب کاربری را به برنامه‌تان اضافه کنید.

ParseUser زیرکلاسی از ParseObject است و همان ویژگی ها را دارد، همچون الگوی انعطاف‌پذیر، و یک واسط key value.
تمام متد هایی که در ParseObject هستند در ParseUser نیز وجود دارند.
تفاوتشان در این است که ParseUser چند ویژگی اضافه دارد که مخصوص حساب کاربری است.

### خصوصیات ParseUser 

ParseUser چند خصوصیت دارد که آن را از ParseObject متمایز میسازد:

<space><space><space>- username: نام کاربری برای کاربر (الزامی)

<space><space><space>- password: رمز عبور برای کاربر (مورد نیاز در هنگام ثبت نام)

<space><space><space>- email: آدرس ایمیل برای کاربر (اختیاری)

بعدا هنگامی که وارد موارد استفاده مختلف برای کاربران شدیم، هر یک از این ها را به تفصیل شرح میدهیم.
به خاطر داشته باشید که اگر شما username و email را با استفاده از setter ها ثبت کنید، دیگر نیازی نیست که با متد put آنها را ثبت کنید.

### ثبت نام
اولین کاری که برنامه شما احتمالا انجام میدهد درخواست ثبت نام از کاربر است.
کدی که در ادامه آمده یک ثبت نام معمولی را نشان میدهد:

<div dir="ltr">

```java
ParseUser user = new ParseUser();
user.setUsername("my name");
user.setPassword("my pass");
user.setEmail("email@example.com");

// other fields can be set just like with ParseObject
user.put("phone", "650-253-0000");

user.signUpInBackground(new SignUpCallback() {
    public void done(ParseException e) {
        if (e == null) {
            // Hooray! Let them use the app now.
        } else {
            // Sign up didn't succeed. Look at the ParseException
            // to figure out what went wrong
        }
    }
});
```
</div>
این درخواست به صورت ناهمگام یک کاربر جدید در برنامه parse شما ایجاد خواهد کرد.
قبل از این که این کار را انجام دهد، چک میکند که مطمئن شود که هم username و هم email یکتا باشند.
همچنین به صورت ایمن پسورد را با استفاده از bcrypt در فضای ابری hash میکند. 
رمز عبور ها هرگز به صورت متن ساده ذخیره نمی شوند و همینطور هرگز رمز به صورت متن ساده به کلاینت مخابره نمیشود.

به خاطر داشته باشید که ما از متد signUpInBackground استفاده کردیم، نه از متد saveInBackground.
همیشه باید ParseUser های جدید با متد signUpInBackground(یا signUp) ساخته شوند.
بروزرسانی های بعدی برای یک کاربر میتواند با فراخوانی save انجام شود.

متد signUpInBackground به صورت های مختلفی در دسترس است.
با قابلیت بازگردانی خطا ها، و همچنین نسخه های همگام.
طبق معمول، ما به شدت توصیه میکنیم که اگر مقدور است، از نسخه های ناهمگام استفاده کنید، تا UI برنامه شما را متوقف نکند.
شما میتوانید در مورد این متد های خاص در
[داک های API](https://docs.parseplatform.org/android/guide/)
ما اطلاعات بیشتری بدست آورید.

اگر یک ثبت نام موفقیت آمیز نباشد، میتوانید error object ی که بازگردانده میشود را بخوانید.
مورد بسیار محتمل این است که نام کاربری یا ایمیل در حال حاضر توسط کاربر دیگری در حال استفاده است.
شما باید این مورد را به صورت واضح به کاربر مخابره کنید، و از ایشان بخواهید که یک نام کابری متفاوت را امتحان کنند.

شما آزادید که از یک آدرس ایمیل به عنوان نام کاربری استفاده کنید.
به سادگی از کاربران درخواست کنید که ایمیلشان را وارد کنند، ولی آن را در محل username بنویسند - ParseUser به حالت معمول رفتار خواهد کرد.
در خصوص این که این مورد چگونه مدیریت میشود در مبحث بازنشانی رمز عبور صحبت میکنیم.

### ورود

البته، بعد از این که به کاربران اجازه دادید که ثبت نام کنند، شما نیاز دارید به آنها اجازه دهید که در آینده به حساب هایشان وارد شوند.
برای انجام این کار، شما میتوانید از متد logInInBackground استفاده کنید.

<div dir="ltr">

```java
ParseUser.logInInBackground("Jerry", "showmethemoney", new LogInCallback() {
    public void done(ParseUser user, ParseException e) {
        if (user != null) {
            // Hooray! The user is logged in.
        } else {
            // Signup failed. Look at the ParseException to see what happened.
        }
    }
});
```
</div>

### تایید کردن ایمیل 

فعال کردن تایید ایمیل در تنظیمات یک برنامه به برنامه این امکان را میدهد که بخشی از تجربیاتش را به کاربران با ایمیل تایید شده اختصاص دهد.
تایید کردن ایمیل کلید emailVerified را به آبجکت ParseUser اضافه میکند.
وقتی که email یک ParseUser تنظیم یا عوض شود، emailVerified به false تغییر میکند.
سپس پارس یک لینک برای کاربر ارسال میکند که emailVerified را به true تغییر خواهد داد.

سه حالت مورد توجه برای emailVerified به شرح زیر است:

<space><space><space>1-<space><space><space> true:
کاربر ایمیلش را با کلیک کردن روی لینکی که پارس برایش ارسال کرده تایید کرده است.
ParseUser ها در ابتدای ساختن حسابشان نمیتوانند این مقدار را داشته باشند.

<space><space><space>2-<space><space><space> false:
آخرین باری که آبجکت ParseUser گرفته شده، کاربر ایمیلش را تایید نکرده بوده است.
اگر emailVerified مقدار false دارد، فراخوانی
()fetch

[comment]: <> (this might be wrong)
روی ParseUser را در نظر بگیرید.

<space><space><space>3-<space><space><space> missing:
آبجکت ParseUser وقتی ایجاد شده که تایید ایمیل خاموش بوده یا ParseUser ایمیلی ندارد.

### کاربر کنونی

اگر کاربر با هر بار باز کردن برنامه مجبور به ورود باشد باعث مزاحمت است.
شما میتوانید از این امر با استفاده از آبجکت currentUser کش شده اجتناب کنید.

هرگاه که شما از هر روش ثبت نام یا ورود استفاده کنید، کاربر روی حافظه دیسک کش میشود.
شما متیوانید با این کش به عنوان نشست رفتار کنید، و به صورت خودکار تصور کنید که کاربر وارد شده است:

<div dir="ltr">

```java
ParseUser currentUser = ParseUser.getCurrentUser();
if (currentUser != null) {
    // do stuff with the user
} else {
    // show the signup or login screen
}
```
</div>

شما میتوانید کاربر کنونی را با logOut پاک کنید:

<div dir="ltr">

```java
ParseUser.logOut();
ParseUser currentUser = ParseUser.getCurrentUser(); // this will now be null
```
</div>

### کاربران ناشناس 

این که بتوانید داده و آبجکت ها را به کاربران منحصر بفرد پیوند دهید بسیار ارزشمند است، اما گاهی اوفات شما میخواهید که بتوانید این کار را انجام دهید بدون این که کاربر را مجبور به انتخاب نام کاربری و رمز عبور کنید.

یک کاربر ناشناس کاربری است که میتواند بدون نام کاربری و رمز عبور ساخته شود اما همچنان تمام ظرفیت ها را همچون سایر ParseUser ها دارد. 
بعد از خروج از حساب، یک حساب ناشناس ترک میشود، و داده هایش دیگر قابل دسترسی نیستند.

شما میتوانید یک حساب کاربری ناشناس را با استفاده از ParseAnonymousUtils بسازید:

<div dir="ltr">

```java
ParseAnonymousUtils.logIn(new LogInCallback() {
    @Override
    public void done(ParseUser user, ParseException e) {
        if (e != null) {
            Log.d("MyApp", "Anonymous login failed.");
        } else {
            Log.d("MyApp", "Anonymous user logged in.");
        }
    }
});
```
</div>

شما میتوانید یک کاربر ناشناس را با ثبت نام کاربری و رمز عبور و فراخوانی
()signUp
به یک کاربر معمولی تبدیل کنید، یا با ورود یا اتصال به یک سرویس همانند Facebook و Twitter این کار را انجام دهید.
کاربر تبدیل شده میتواند تمام داده هایش را نگه دارد.
برای مشخص کردن این که آیا کاربر کنونی ناشناس است یا خیر میتوانید
()ParseAnonymousUtils.isLinked
را بررسی کنید.

<div dir="ltr">

```java
if (ParseAnonymousUtils.isLinked(ParseUser.getCurrentUser())) {
    enableSignUpButton();
} else {
    enableLogOutButton();
}
```
</div>

کاربران ناشناس همچنین میتوانند به صورت خودکار بدون نیاز به درخواست اینترنت برای شما ساخته شوند،در نتیجه شما میتوانید کارتان را با حسابتان بلافاصله وقتی برنامه آغاز میشود شروع کنید.
وقتی که ساختن خودکار حساب ناشناس را در هنگام آغاز برنامه فعال کنید،
()ParseUser.getCurrentUser
هرگز null نخواهد بود.
حساب کاربری به صورت خودکار در فضای ابری ذخیره خواهد شد در اولین وقتی که کاربر یا هر آبجکت مربوط به آن ذخیره شود.
تا آن نقطه، ID آبجکت حساب کاربری null خواهد بود.
فعال کردن ایجاد حساب خودکار، پیوند داده ها با کاربرانتان را بی دردسر خواهد نمود.
به عنوان مثال، در متد
()Application.onCreate
شما میتوانید بنویسید:

<div dir="ltr">

```java
ParseUser.enableAutomaticUser();
ParseUser.getCurrentUser().increment("RunCount");
ParseUser.getCurrentUser().saveInBackground();
```
</div>

### مشخص کردن کاربر کنونی 

اگر شیوه هویت سنجی خودتان را استفاده میکنید یا میخواهید از طرف سرور وارد یک حساب کاربری شوید، میتوانید session token را به client ارسال کنید و از متد become استفاده کنید.
این متد قبل از تعیین کردن کاربر کنونی، اطمینان حاصل میکند که token معتبر است.

<div dir="ltr">

```java
ParseUser.becomeInBackground("session-token-here", new LogInCallback() {
    public void done(ParseUser user, ParseException e) {
        if (user != null) {
            // The current user is now set to user.
        } else {
            // The token could not be validated.
        }
    }
});
```
</div>

### امنیت برای object های user 
کلاس ParseUser به صورت پیش فرض ایمن است.
داده ذخیره شده در یک ParseUser صرفا توسط همان کاربر قابل تغییر است.
به صورت پیش فرض، داده میتواند توسط هر کلاینتی خوانده شود.
از این روی، برخی آبجکت های ParseUser تصدیق میشوند و میتوان آنها را اصلاح کرد، درحالی که برخی دیگر صرفا قابل خواندن هستند.

به طور ویژه، شما قادر نیستید که هیچ یک از انواع متد های save یا delete را فراخوانی کنید، مگر اینکه ParseUser را با یکی از روش های اعتبارسنجی به دست آورده باشید، همچون logIn یا signUp.
این روش اطمینان حاصل میکند که تنها خود کاربر میتواند اطلاعاتش را تغییر دهد.

کد زیر این سیاست امنیتی را نشان میدهد:

<div dir="ltr">

```java
ParseUser user = ParseUser.logIn("my_username", "my_password");
user.setUsername("my_new_username"); // attempt to change username
user.saveInBackground();// This succeeds, since the user 
// was authenticated on the device

// Get the user from a non-authenticated manner
ParseQuery<ParseUser> query = ParseUser.getQuery();
query.getInBackground(user.getObjectId(), new GetCallback<ParseUser>() {
    public void done(ParseUser object, ParseException e) {
        object.setUsername("another_username");

        // This will throw an exception, since the ParseUser is not authenticated
        object.saveInBackground();
    }
});
```
</div>

ParseUser ای که از ()getCurrentUser گرفته میشود همیشه اعتبارسنجی شده است.

اگر نیاز دارید که چک کنید آیا یک ParseUser اعتبارسنجی شده است یا خیر، میتوانید از متد ()isAuthenticated استفاده کنید.
نیازی نیست که آبجکت های ParseUser ای که از طریق یک روش اعتبارسنجی بدست آمده اند را با این متد بررسی کنید.

### امنیت برای سایر object ها 

همان مدلی که برای ParseUser ها اعمال میشد میتواند برای سایر object ها نیز اعمال شود.
برای هر object ای میتوانید مشخص کنید که کدام کاربران اجازه دارند که آن را بخوانند و کدام کاربران مجازند که آن را اصلاح کنند.
برای پشتیبانی از این نوع امنیت، هر آبجکت یک
[لیست مدیریت دسترسی](https://en.wikipedia.org/wiki/Access_control_list)
دارد، که با ParseACL پیاده سازی شده است.

ساده ترین راه برای استفاده از یک ParseACL مشخص کردن این است که یک object میتواند تنها توسط یک کاربر خوانده یا نوشته شود.
برای ساختن چنین objectی، ابتدا باید یک ParseUser وارد شده باشد.
سپس، new ParseACL(user) یک ParseACL میسازد که دسترسی به آن کاربر را محدود میکند.
لیست مدیریت دسترسی (ACL) یک object وقتی که آن object ذخیره شود به روز رسانی میشود.
از این روی، برای ساختن یک یادداشت خصوصی که تنها توسط کاربر کنونی قابل دسترسی باشد چنین میکنیم:

<div dir="ltr">

```java
ParseObject privateNote = new ParseObject("Note");
privateNote.put("content", "This note is private!");
privateNote.setACL(new ParseACL(ParseUser.getCurrentUser()));
privateNote.saveInBackground();
```
</div>

این یادداشت تنها برای کاربر کنونی در دسترس خواهد بود، همچنین روی تمام دستگاه هایی که این کاربر در آنها وارد حسابش شده است نیز قابل دسترسی است.
این قابلیت برای برنامه هایی که میخواهند قابلیت دسترسی به اطلاعات کاربر را در بین چند دستگاه داشته باشند مفید است، مثل برنامه یادداشت شخصی.

مجوز ها را میتوانید طی یک روال برای هر کاربر بدهید.
میتوانید اجازه ها را تک به تک به یک ParseACL با استفاده از setReadAccess و setWriteAccess بدهید.
برای مثال فرض کنید که یک پیغام دارید که به یک گروه با اعضای متعدد فرستاده خواهد شد و تمام اعضا حق خواندن و پاک کردن آن را دارند:

<div dir="ltr">

```java
ParseObject groupMessage = new ParseObject("Message");
ParseACL groupACL = new ParseACL();

// userList is an Iterable<ParseUser> with the users we are sending this message to.
for (ParseUser user : userList) {
    groupACL.setReadAccess(user, true);
    groupACL.setWriteAccess(user, true);
}

groupMessage.setACL(groupACL);
groupMessage.saveInBackground();
```
</div>

همچنین میتوانید با setPublicReadAccess و setPublicWriteAccess به همه کاربر ها به صورت یکجا اجازه دهید.
این اجازه میدهد الگوهایی مانند ارسال کامنت را برای پیام ها ایجاد کنید.
برای مثال، جهت ایجاد یک پست که تنها میتواند توسط نویسنده اش اصلاح شود، اما همه میتوانند آن را بخوانند داریم:

<div dir="ltr">

```java
ParseObject publicPost = new ParseObject("Post");
ParseACL postACL = new ParseACL(ParseUser.getCurrentUser());
postACL.setPublicReadAccess(true);
publicPost.setACL(postACL);
publicPost.saveInBackground();
```
</div>

برای کمک به این که مطمئن باشید داده ی کاربرانتان به صورت پیش فرض ایمن است، میتوانید یک ACL پیش فرض را به تمام ParseObject های جدید اعمال کنید:

<div dir="ltr">

```java
ParseACL.setDefaultACL(defaultACL, true);
```
</div>

در کد بالا پارامتر دوم تابع به Parse اطلاع میدهد که مطمئن شود که ACL پیش فرض که هنگام ساخته شدن object محول شده است در آن زمان به کاربر کنونی اجازه خواندن و نوشتن میدهد.
بدون این تنظیمات، شما نیاز دارید که ACL پیش فرض را هر دفعه که یک کاربر وارد یا خارج میشود reset کنید تا کاربر کنونی دسترسی های مناسب داشته باشد.
با این تنظیمات، شما میتوانید از تغییرات روی کاربر کنونی چشم پوشی کنید تا وقتی که به طور صریح نیاز داشته باشید که دسترسی های متفاوتی اعطا کنید.

ACL های پیش فرض ساختن برنامه هایی که روال های دسترسی معمول دارند را ساده میکند.
به عنوان مثال برنامه ای چون Twitter، که محتوای کاربر به طور عمومی برای جهان قابل مشاهده است، میتواند یک default ACL را به این صورت تنظیم کند:

<div dir="ltr">

```java
ParseACL defaultACL = new ParseACL();
defaultACL.setPublicReadAccess(true);
ParseACL.setDefaultACL(defaultACL, true);
```
</div>

برای برنامه ای مثل DropBox، که داده ی کاربر تنها برای خودش قابل دسترسی است مگر این که به صراحت اجازه داده شده باشد، شما میتوانید از یک default ACL استفاده کنید که تنها به کاربر کنونی دسترسی دهد:

<div dir="ltr">

```java
ParseACL.setDefaultACL(new ParseACL(), true);
```
</div>

برنامه ای که در Parse، داده را Log میکند ولی به هیچ کاربری اجازه دسترسی به آن داده را نمیدهد میتواند با ارائه ی یک ACL منع کننده، دسترسی کاربر کنونی را منع کند:

<div dir="ltr">

```java
ParseACL.setDefaultACL(new ParseACL(), false);
```
</div>

عملیات هایی که ممنوع هستند، همچون پاک کردن object ی که اجازه نوشتن روی آن را ندارید، منجر به دریافت خطای ParseException.OBJECT_NOT_FOUND میشوند.
برای اهداف امنیتی، این مانع میشود که کاربران تشخیص دهند که کدام object id ها وجود دارند اما ایمن شده اند، و کدام ها اصلا وجود ندارند.

### بازنشانی رمز عبور
کتابخانه ما یک راه برای کاربران ارائه میدهد که رمز عبورشان را به طور امن بازنشانی کنند.

برای شروع جریان بازنشانی رمز عبور، از کاربر بخواهید که آدرس ایمیلش را وارد کند، سپس این کد را فراخوانی کنید:

<div dir="ltr">

```java
ParseUser.requestPasswordResetInBackground("myemail@example.com", new RequestPasswordResetCallback() {
    public void done(ParseException e) {
        if (e == null) {
        // An email was successfully sent with reset instructions.
        } else {
        // Something went wrong. Look at the ParseException to see what's up.
        }
    }
});
```
</div>

این کد در بین نام های کاربری یا ایمیل ها به دنبال ایمیلی که از کاربر دریافت کرده میگردد و برای آن ایمیل بازیابی رمز عبور میفرستد.
با این کار شما میتوانید انتخاب کنید که از ایمیل کاربر به عنوان نام کاربری اش استفاده کنید یا آن را در یک فیلد جدا ذخیره کنید.

جریان بازنشانی کلمه عبور به صورت زیر است:

1- کاربر با دادن ایمیل خود درخواست بازنشانی کلمه عبور را میدهد.

2- Parse یک ایمیل حاوی لینک مخصوص به آدرس ایمیل میفرستد.

3- کاربر بر روی لینک کلیک میکند تا به یک صفحه جدید هدایت شود تا رمز جدید را در آن وارد کند.

4- کلمه عبور بازنشانی شده و به رمز جدید تغییر میکند.

در نظر داشته باشید که در این جریان، برنامه شما با همان نامی که در هنگام ساخت در Parse مشخص کرده اید معرفی میشود.

### Query زدن

جهت ایجاد query برای کاربران، میتوان از query مخصوص کاربر استفاده کرد:

<div dir="ltr">

```java
ParseQuery<ParseUser> query = ParseUser.getQuery();
query.whereEqualTo("gender", "female");
query.findInBackground(new FindCallback<ParseUser>() {
    public void done(List<ParseUser> objects, ParseException e) {
        if (e == null) {
        // The query was successful.
        } else {
        // Something went wrong.
        }
    }
});
```
</div>

همچنین میتوان برای گرفتن ParseUser با استفاده از id آن، از get استفاده کرد.

### وابستگی ها 

وابستگی های مربوط به ParseUser بدون نیاز به تنظیم خاصی قابل ایجاد هستند، به عنوان مثال در یک برنامه بلاگ نویسی برای ایجاد پست جدید و بازیابی پست های قبلی یک کاربر داریم:

<div dir="ltr">

```java
ParseUser user = ParseUser.getCurrentUser();

// Make a new post
 ParseObject post = new ParseObject("Post");
 post.put("title", "My New Post");
 post.put("body", "This is some great content.");
 post.put("user", user);
 post.saveInBackground();

// Find all posts by the current user
ParseQuery<ParseObject> query = ParseQuery.getQuery("Post");
query.whereEqualTo("user", user);
query.findInBackground(new FindCallback<ParseObject>() { ... });
```
</div>


## Role در Parse

## Session در Parse

## File در Parse
ParseFile به کاربر اجازه میدهد که در سرور فایل ذخیره یا از آن فایل دریافت کند.
توجه کنید که محتویات یک فایل را میتوانیم با یک ParseObject بفرستیم اما بسیار سنگین میشود و عملکرد خوبی نخواهد داشت.

ساخت ParseFile بسیار ساده است.
توجه کنید که هر فایل در سیستم، معادل یک رشته از byte است.
بنابرین میتوانیم هر فایلی در سیستم را به یک رشته بایت تبدیل کنیم.
متد سازنده ParseFile نیز اسم فایل و رشته از بایت های سازنده آن فایل را دریافت میکند.

<div dir="ltr">

```java
byte[] data = "This is a new file's contents".getBytes();
ParseFile file = new ParseFile("resume.txt", data);
```
</div>

توجه کنید که در مورد فایل ها دو نکته شایان ذکر است.

- نگرانی بابت تعارض اسم فایل ها با یک دیگر وجود ندارد.
  میتوان چند فایل با یک نام آپلود کرد.
  این موضوع توسط Parse با یک unique identifier که به هر فایل تعلق میگیرد، مدیریت میشود.
  
- اطمینان حاصل کنید که نام فایل دارای پسوند مناسب است تا Parse بتواند رفتار مناسب را با فایل انجام دهد.

مشابه قبل برای ذخیره یک فایل، متودهایی وجود دارد. (پیشتر در بخش ابجکت ها توضیح دادیم.) با توجه به نیاز میتوان از هر یک از متودهای `save` استفاده کرد.

<div dir="ltr">

```java
file.saveInBackground();
```
</div>

پس از آنکه عملیات ذخیره تکمیل شود، میتوان با فایل مانند مشابه یک object عادی برخورد کرد و حتی آن را درون ParseObject قرار داد.

<div dir="ltr">

```java
ParseObject jobApplication = new ParseObject("JobApplication");
jobApplication.put("applicantName", "Joe Smith");
jobApplication.put("applicantResumeFile", file);
jobApplication.saveInBackground();
```
</div>

همچنین مشابه قبل، میتوان با Query، فایل ها را دریافت کنیم.
به عنوان مثال فایل ذخیره شده در کد فوق را میتوانیم به شکل زیر دریافت کنیم.

<div dir="ltr">

```java
ParseFile applicantResume = (ParseFile) anotherApplication.get("applicantResumeFile");
applicantResume.getDataInBackground(new GetDataCallback() {
    public void done(byte[] data, ParseException e) {
        if (e == null) {
            // data has the bytes for the resume
        } else {
            // something went wrong
        }
    }
});
```
</div>

یکی از مزایای Parse این است که موضوع پیچیده ای مانند Progress در آن پیاده‌سازی شده است.
به سادگی در هر دو متود `saveInBackground` و `getDataInBackground` میتوانیم با پیاده‌سازی کلاس `ProccessCallback`، میتوانیم از وضعیت پیشرفت فرآیند آگاه شویم و با این آگاهی می توانیم مواردی مانند spinner و یا ... را بروزرسانی کنیم.

<div dir="ltr">

```java
byte[] data = "Working at Parse is great!".getBytes();
ParseFile file = new ParseFile("resume.txt", data);

file.saveInBackground(new SaveCallback() {
    public void done(ParseException e) {
        // Handle success or failure here ...
    }
}, new ProgressCallback() {
    public void done(Integer percentDone) {
        // Update your progress spinner here. percentDone will be between 0 and 100.
    }
});
```
</div>

## Local Datastore در Parse
Parse Android SDK یک Datastore محلی در اختیار ما می‌گذارد که از آن می‌توان برای ذخیره و بازیابی `ParseObject`ها استفاده کرد، حتی زمانی که اینترنت در دسترس نیست. برای استفاده از این کاربرد، کافی است که `()Parse.enableLocalDatastore` را قبل از صدا کردن `initialize` صدا کنیم.

<div dir="ltr">

```java
import com.parse.Parse;
import android.app.Application;

public class App extends Application {
  @Override
  public void onCreate() {
    super.onCreate();

    Parse.enableLocalDatastore(this);
    Parse.initialize(this);
  }
}
```
</div>

اگر از یک `Parse.Configuration.Builder` استفاده می‌کنید، آن را آنجا فعال کنید:

<div dir="ltr">

```java
Parse.initialize(new Parse.Configuration.Builder(context)
  .server(...)
  .applicationId(...)
  .enableLocalDataStore()
  .build());
```
</div>

فعال کردن Datastore محلی یک سری عوارض جانبی دارد که شما باید از آن‌ها آگاه باشید. وقتی که فعال شود، فقط یک مورد از هر `ParseObject` موجود خواهد بود. برای مثال، تصور کنید که شما یک نمونه از کلاس `"GameScore"` با `"objectId = "xWMyZ4YEGZ` دارید و سپس شما یک `ParseQuery` برای همهٔ موارد `"GameScore"` با همان `objectId` چاپ می‌کنید. نتیجه همواره همان نمونه از شیئی خواهد بود که شما در حافظه از قبل داشتید.

یک اثر جانبی دیگر این است که کاربر فعلی و نسخهٔ نصب‌شده فعلی در حافظه در Datastore محلی ذخیره خواهند شد. پس شما می‌توانید که تغییرات ذخیره‌نشدهٔ این اشیا را بین اجراهای مختلف برنامهٔ‌تان را با کمک توابع زیر نگه‌دارید.

صدا زدن تابع `saveEventually` روی یک `ParseObject` باعث می‌شود که آن شی تا زمانی که ذخیره کردن کامل شود، به Datastore محلی وصل شود. پس الآن، اگر شما `ParseUser` فعلی را عوض کنید و `()ParseUser.getCurrentUser().saveEventually` را صدا بزنید، همواره برنامهٔ شما تغییراتی را که شما اعمال کرده‌اید را می‌بیند.

</div>
