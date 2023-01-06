# laravel
ساختن یک روت (مسیر)
توی فریم‌ورک لاراول روت‌ها رو توی مسیر routes و فایل‌های web.php و api.php مشخص می‌کنیم. روت‌هایی که توی فایل web.php مشخص میشن بطور خودکار روی اونها میدل‌ورهایی مثل CSRF و Session اعمال میشه. روت‌هایی که توی فایل api.php مشخص میشن این میدل‌ورها رو ندارن. چون این فایل برای مشخص کردن روت‌های api های برنامه هست و ما توی چنین درخواست‌هایی (درخواست‌های Stateless) نیازی به CSRF و سشن نداریم.

کد زیر ساده‌ترین حالت یک روت هست:

Route::get('/home', function () {
    return 'Hello World';
});
اگه کد بالا رو توی فایل web.php قرار بدیم و توی مرورگر مسیر /homeexample.com رو باز کنیم، متن Hello World رو خواهیم دید. و اگه توی فایل api.php قرار بدیم، باید مسیر /api/homeexample.com رو باز کنیم.

همونطور که می‌بینیم کلاس Route یک متد داره به اسم get که نوع روت ما رو مشخص می‌کنه. یعنی اگه درخواستی بصورت GET و به مسیر /home اومد، Action رو اجرا کن. Action چیه؟ به پارامتر دوم متد get می‌گن Action. چیزی که روتر بعد از پیدا کردن این روت اجرا می‌کنه. که می‌تونه یک کلوژر باشه و یا آدرس یک کنترلر:

Route::get('/home', 'HomeController@index');
برای بقیه درخواست‌ها مثل POST هم متدهایی وجود داره:

Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
یادتون باشه برای درخواست‌ها به روت‌هایی که توی فایل web.php از متد POST, PUT و DELETE استفاده می‌کنن، توکن CSRF رو بفرستین:

<form method="POST" action="/users">
    @csrf
    ...
</form>
اگه متد یک روت delete باشه، باید بدونیم که فرم‌های HTML از متدهای PUT, PATCH یا DELETE پشتیبانی نمی‌کنن. برای حل این مشکل توی فرم از متد POST استفاده می‌کنیم به اضافه یک فیلد به اسم _method که بصورت زیر تعریف میشه:

<form action="/posts/1/delete" method="POST">
    <input type="hidden" name="_method" value="DELETE">
    <!-- or in blade files -->
    @method('DELETE')

    @csrf
</form>
 
یک روت با چند متد
اگه یک روت داشته باشیم که می‌خوایم برای چند درخواست مثل GET و POST قابل دسترسی باشه می‌تونیم از متد match مثل زیر استفاده کنیم:

Route::match(['get', 'post'], '/', function () {
    //
});
و اگه نوع درخواست برامون مهم نیست از any استفاده می‌کنیم:

Route::any('/', function () {
    //
});
 

روت‌هایی که Redirect می‌کنن
اگه می‌خوایم آدرس (URI) درخواست شده رو به یک آدرس دیگه هدایت کنیم از متد redirect استفاده می‌کنیم:

Route::redirect('/from', '/to');
این هدایت بصورت پیشفرض با کد HTTP 302 انجام می‌گیره. اگه کد دیگه‌ای مد نظرمون هست توی پارامتر سوم شماره کد رو مشخص می‌کنیم:

Route::redirect('/from', '/to', 301);
با کدهای ۳۰۱ و ۳۰۲ توی این پست آشنا بشین. همونطور که می‌دونیم هدایت با کد ۳۰۱ یک هدایت دائمی هست. اگه یک هدایت دائمی داریم می‌تونیم بجای استفاده از کد بالا، از کد زیر استفاده کنیم:

Route::permanentRedirect('/from', '/to');
 

فقط نمایش یک View
اگه یک آدرس هیچ کاری غیر از نمایش یک ‌View نداره، می‌تونیم از متد view استفاده کنیم:

Route::view('/about', 'pages.about');
که کد بالا خلاصه شده‌ی کد زیر هست:

Route::get('/about', function() {
    return view('pages.about');
});
می‌تونیم با پارامتر سوم متد view، به ویویی که فراخونی میشه دیتا هم پاس بدیم:

Route::view('/about', 'pages.about', [
    'staff' => 'You'
]);
 

روت‌های با پارامتر
تا الان روت‌های ما استاتیک بودن. یعنی یک مسیر ثابت و صریح رو تعریف می‌کردیم. روت‌های زیر رو در نظر بگیرین:

Route::view('/pages/about', 'pages.about');
Route::view('/pages/team', 'pages.team');
Route::view('/pages/contact', 'pages.contact');
Route::view('/pages/faq', 'pages.faq');
Route::view('/pages/rules', 'pages.rules');
Route::view('/pages/privacy', 'pages.privacy');
Route::view('/pages/blahblahblah', 'pages.blahblahblah');
همه‌ی روت‌های بالا استاتیک هستن و مسیرها بطور صریح مشخص شدن. یک راه برای مرتب کردن و خلاصه‌تر کردن کد بالا اینه که روت‌هامون رو با پارامتر تعریف کردن، داینامیک کنیم. زمانی پارامتر تعریف می‌کنیم که بخوایم یک قسمت از URL متغیر باشه و اون رو توی کد داشته باشیم. توی یک روت پارامترها رو توی {  } تعریف می‌کنیم. به شکل زیر:

Route::get('pages/{page}', function($page) {
    $view = "pages.$page";
    
    return View::exists($view) ? view($view) : abort(404);
});
به پارامتر {page} دقت کنید. این مقدار داینامیک هست و توسط روتر از URL خونده و به اون کلوژر پاس داده میشه. پس مقدارش هر چیزی می‌تونه باشه. اینطوری فقط و فقط یک روت ساختیم.

 
یک روت با چند پارامتر
یک روت می‌تونه چند پارامتر داشته باشه:

Route::get('users/{userId}/posts/{postId}', function ($userId, $postId) {
    $user = User::findOrFail($userId);
    $posts = Post::findOrFail($postId);
});
پارامتر‌ها به ترتیب به کلوژر پاس داده میشن.

 

پارامترهای اختیاری
URL های زیر رو در نظر بگیرید:

// http://www.example.com/posts/1010/how-to-do-that
// http://www.example.com/posts/1010
می‌خوایم یک روت داشته باشیم که وقتی قسمت آخر این URL هم وجود نداشت هم کار کنه. مثل URL دوم. اینجا بجای اینکه دو تا روت بسازیم، کافیه یک روت بسازیم و برای قسمت آخر از پارامتر اختیاری استفاده کنیم. پارامتر اختیاری با قرار دادن علامت سوال (?) بعد از اسم پارامتر ساخته میشه:

Route::get('/posts/{postId}/{slug?}', function($postId, $slug = null) {

});
توی روت بالا پارامتر slug یک پارامتر اختیاری هست که بود و نبودش فرقی نمی‌کنه. البته به $slug توی پارامترهای کلوژر دقت کنین که مقدار پیشفرض باید داده بشه تا وقتی این پارامتر حضور نداشت به خطا برنخوریم.

 

محدودیت گذاری پارامترها با RegEx
برای اینکه فرمت پارامتری که پاس داده میشه رو کنترل کنیم می‌تونیم از متد where استفاده کنیم که پارامتر اول اون اسم پارامتر روت و پارامتر دوم RegExی هست که قراره فرمت این پارامتر رو مشخص کنه:

Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');

Route::get('user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
اگه روت ما چند پارامتر داره می‌تونیم مثل آیتم آخر مثال بالا به where یک آرایه پاس بدیم. روت‌های بالا زمانی اجرا میشن که پارامتر URI با RegEx هم‌خوانی داشته باشه. در غیر این صورت روتر میره سراغ روت‌های بعدی.

 

محدودیت‌گذاری سراسری
اگه می‌خوایم همه‌ی روت‌هایی که یک پارامتر مثل id دارن فقط بصورت عددی باشن، بجای اینکه توی هر روت یک where تعریف کنیم، می‌تونیم این محدودیت رو بصورت سراسری تعریف و برای همه‌ی روت‌هایی که پارامتر id دارن اعمال کنیم. این محدودیت رو توی RouteServiceProvider و متد boot تعریف می‌کنیم:

public function boot()
{
    parent::boot();

    Route::pattern('id', '[0-9]+');
    Route::pattern('username', '[a-z0-9]+');
}
بعد از این همه‌ی روت‌هایی که پارامتری به اسم id یا username دارن این محدودیت روی اونها اعمال میشه:

Route::get('post/{id}', '...'); // only numeric id

Route::get('user/{id}', '...');  // only numeric id
Route::get('user/{username}', '...');  // only alphabetical usernames
فرض کنیم URL هایی داریم بصورت زیر که تعداد نامشخصی اسلش / دارن:

// http://example.com/download/path/to/file/foo/bar
// http://example.com/download/path/to/file/foo/bar/baz
// http://example.com/download/path/to/file/blah
// http://example.com/download/path/to/file/foo/bar/baz/bak/ban/bag
بطور پیشفرض روتر قسمت‌های یک روت و URL رو با اسلش جدا و قسمت‌بندی می‌کنه. پس برای چنین آدرس‌هایی باید تعداد اسلش‌های ما مشخص باشه. اما اگه یک آدرسی وجود داشته باشه که تعداد اسلش اون مشخص نیست و یا قسمتی از اون شامل کارکترهایی مثل اسلش هست که مثلا کاربر اون رو وارد کرده، باید چکار کنیم؟ باید به روتر بگیم که از یک قسمتی به بعد، اسلش‌ها رو نادیده بگیر و یا با اسلش‌ها مثل بقیه کارکترها رفتار کن. این کار رو با نوشتن یک where با یک فرمت خاص مثل زیر انجام می‌دیم:

Route::get('download/{file}', function ($file) {
    return $file;
})->where('file', '.*');
الان روت بالا برای همه‌ی آدرس‌هایی که با download شروع بشن و شامل اسلش هم باشن معتبر هست. دقت کنین با وجود چنین محدودیتی، پارامتری مثل {file} باید توی آخرین قسمت تعریف شده باشه.

 

نام‌گذاری روت‌ها
می‌تونیم با متد name یک روت رو نام‌گذاری کنیم:

Route::get('posts', '...')->name('posts');
و به صورت زیر با تابع route() توی برنامه بهش دسترسی پیدا می‌کنیم:

public function update()
{
    $url = route('posts');
}
نام‌گذاری روت‌ها برای زمانی خوبه که یک آدرسی داریم که خیلی طولانیه و نوشتن و به یاد آوردن اون کار سختی باشه و یا همچنین آدرسی که بعدا ممکنه عوض بشه. مثلا توی روت بالا ممکنه مسیر posts رو بعدا بخوایم به all-posts تغییر بدیم.

 

روت نام‌گذاری شده دارای پارامتر
اگه بخوایم از روت نام‌گذاری شده‌ای استفاده کنیم که دارای پارامتر هست، آرگومان دوم تابع route یک آرایه می‌گیره که برای پاس دادن پارامترها به این روت هست:

Route::get('user/{id}', '...')->name('user');

// ...

$url = route('user', ['id' => 1]);
اگه توی لیست پارامترهای پاس داده شده به تابع route، مقداری پاس داده بشه که توی پارامترهای روت تعریف نشده، این مقدار به عنوان Query String به آخر URL چسبیده میشه:

Route::get('user/{id}', '...')->name('user');

// ...

$url = route('user', [
    'id' => 1,
    'posts' => 'yes',
    'info' => 'no',
]);

// example.com/user/1?posts=yes&info=no
 

گروه‌بندی روت‌ها
خیلی وقتا یک سری ویژگی‌هایی توی روت‌ها وجود داره که با هم مشترک هستن. مثلا قسمتی از URL خیلی از روت‌ها با هم برابر هستن و یا میدل‌ور هایی یکسان دارن. مثل روت‌های زیر:

Route::get('/admin/users', '...')->middleware('auth');
Route::get('/admin/posts', '...')->middleware('auth');
Route::get('/admin/comments', '...')->middleware('auth');
همونطور که می‌بینیم مسیر /admin/ و میدل‌ور auth توی همه‌ی روت‌ها تکرار شده. خب برای جلوگیری از تکرار، لاراول امکان گروه‌بندی روت‌های مشابه رو در اختیار ما گذاشته. این کار با متد group انجام میشه. پارامتر اول این متد برای مشخص کردن ویژگی‌های مشترک روت‌ها هست. پارامتر دوم یک کلوژر هست که توی اون روت‌ها رو مشخص می‌کنیم:

Route::group(['prefix' => 'admin', 'middleware' => 'auth'], function() {
    Route::get('/users', '...');
    Route::get('/posts', '...');
    Route::get('/comments', '...');
});
به آرایه آرگومان اول دقت کنین. توی این آرایه ما برای مسیرهایی که شروع مشترکی دارن از کلید prefix استفاده کردیم. اینطوری admin یک پیشوند برای همه‌ی روت‌های این گروه خواهد بود. همچنین برای میدل‌ورها از کلید middleware استفاده کردیم. که این میدل‌ور روی همه‌ی این روت‌ها اعمال میشه.

 

Namespace مشترک کنترلرها
درست مثل prefix ممکنه کنترلرهای ما هم Namespaceهایی با شروع مشابه داشته باشن:

Route::get('/admin/users', 'Api\v1\Admin\UsersController');
Route::get('/admin/posts', 'Api\v1\Admin\PostsController');
Route::get('/admin/comments', 'Api\v1\Admin\CommentsController');
همونطور که می‌بینیم Api\v1\Admin\ برای همه‌ی کنترلرهای روت‌های بالا تکرار شده. برای جلوگیری از این تکرار، از کلید namespace توی آرگومان اول متد group استفاده می‌کنیم:

Route::group([
    'namespace' => 'Api\v1\Admin'
], function() {
    Route::get('/users', 'UsersController');
    Route::get('/posts', 'PostsController');
    Route::get('/comments', 'CommentsController');
});
 

روت‌های یک زیر دامنه
روت‌های یک subdomain رو میشه با کلید domain گروه‌بندی کرد:

Route::group([
    'domain' => 'sub.example.com'
], function() {
    Route::get('/users', '...'); // sub.example.com/users
});
دقت کنین که روت‌های یک زیر دامنه باید قبل از روت‌های دامنه اصلی ثبت شده باشن. در غیر این صورت روت‌های مشابه که توی دامنه اصلی وجود دارن رونوشت (overwrite) و در نظر گرفته میشن.

 
پیشوند برای اسم روت‌ها
فرض کنیم روت‌هایی داریم که شروع name مشابهی دارن. مثل روت‌های زیر که اسم اونها با admin. شروع میشه:

Route::get('/users', 'UsersController')->name('admin.users');
Route::get('/posts', 'PostsController')->name('admin.posts');
Route::get('/comments', 'CommentsController')->name('admin.comments');
می‌تونیم توی یک گروه، برای روت‌هایی که شروع name مشابهی دارن، از کلید name استفاده کنیم تا کدها باز هم خلاصه‌تر بشن:

Route::group([
    'name' => 'admin.'
], function() {
    Route::get('/users', 'UsersController')->name('users');
    Route::get('/posts', 'PostsController')->name('posts');
    Route::get('/comments', 'CommentsController')->name('comments');
});
بجای اینکه ویژگی‌های مشترک رو توی آرایه تعریف کنیم، می‌تونیم از متدهای اختصاصی برای هر کدوم از این ویژگی‌ها استفاده کنیم:

Route::name('admin.')
->prefix('admin')
->domain('...')
->namespace('Api\v1\Admin')
->group(function() {
    Route::get('/users', 'UsersController')->name('users');
    Route::get('/posts', 'PostsController')->name('posts');
    Route::get('/comments', 'CommentsController')->name('comments');
});
 

گروه‌های تو در تو
گروه‌ها می‌تونن توی هم دیگه هم تعریف بشن. اینطوری گروه داخلی ویژگی‌های گروه‌های والدش رو به ارث می‌بره:

Route::group(['middleware' => 'auth'], function() {
    Route::get('/posts', '...');

    Route::group(['middleware' => 'is_admin'], function() {
        Route::get('/users', '...');
    });
});
 

Route Model Binding چیه؟
کد زیر رو در نظر بگیرین:

Route::get('user/{id}', function($userId) {
    $user = User::findOrFail($userId);
});
خیلی وقت‌ها پارامتر روت‌ها، یک شناسه رکورد توی دیتابیس هست. مثل id توی کد بالا. که در حالت عادی ما اون پارامتر رو می‌گیریم و بلافاصله با یک کوئری اون رکورد رو فراخونی می‌کنیم. لاراول یک ویژگی رو در اختیار ما گذاشته تا "عملیات خوندن پارامتر و فراخونی رکورد از دیتابیس" بصورت خودکار انجام بگیره. به این ویژگی میگن Route Model Binding که نوشتن اون بصورت زیر هست:

Route::get('user/{user}', function(App\User $user) {
    echo $user->id;
    echo $user->name;
});
برای اینکار ما از تکنیک type-hint استفاده می‌کنیم. type-hint در حالت کلی یعنی مشخص کردن نوع ورودی یک تابع. توی مثال بالا با مشخص کردن پارامتر $user از نوع مدل User، لاراول بصورت خودکار یک درخواست به دیتابیس میزنه، رکورد رو پیدا می‌کنه و اطلاعات رو می‌ریزه توی متغیر $user. یعنی یک درخواست خودکار توسط فریم‌ورک بصورت زیر:

$userId = request()->route('user');

$user = User::findOrFail($userId);
اگه رکورد مورد نظر ما توی دیتابیس پیدا نشه ۴۰۴ می‌گیریم. دقت کنین که اسم پارامتر روت، با پارامتر کلوژر یکی باشه. یعنی {user} و $user یا {id} و $id

 

مشخص کردن ستون
برای Route Model Binding لاراول بطور پیشفرض از ستون id برای جستجو توی دیتابیس استفاده می‌کنه. اگه ستونی غیر از id مدنظر ما هست اون رو به این صورت توی پارامتر روت تعریف می‌کنیم:

Route::get('user/{user:username}', function(App\User $user) {
    echo $user->id;
    echo $user->name;
});
همچنین اگه مدل ما ستونی به اسم id نداره (ستون Primary Key چیزی غیر از id هست) اون رو توی مدل مشخص می‌کنیم:

class User extends Model
{
    public function getRouteKeyName()
    {
        return 'username';
    }
}
اینطوری دیگه لازم نیست توی تعریف روت، ستون رو مشخص کنیم و بصورت خودکار ستون username در نظر گرفته میشه.

حتی باز هم میشه کوئری که به دیتابیس زده میشه رو بیشتر شخصی‌سازی کرد. کافیه توی مدل مورد نظرمون متد resolveRouteBinding رو رونوشت کنیم:

public function resolveRouteBinding($value)
{
    return $this->where('username', $value)
        ->where('status', 1)
        ->firstOrFail();
}
 

Explicit Binding
اگه بخوایم با حضور داشتن پارامتری مثل {user} همیشه Route Model Binding انجام بشه کافیه از کد زیر توی RouteServiceProvider و توی متد boot استفاده کنیم:

public function boot()
{
    parent::boot();
    
    Route::model('user', \App\User::class);
}
با این کار دیگه لازم نیست توی پارامتر کلوژرها یا کنترلرها خودمون اسم کلاس رو Type-hint کنیم:

Route::get('/users/{user}', function ($user) {
    echo $user->email;
})
می‌تونیم کاری که Explicit Binding انجام میده رو هم شخصی‌سازی کنیم. برای یک پارامتر به اسم user توی RouteServiceProvider می‌نویسیم:

public function boot()
{
    parent::boot();

    Route::bind('user', function ($value) {
        return \App\User::where('username', $value)
            ->where('status', 1)
            ->firstOrFail();
    });
}
توی آرگومان اول متد bind اسم پارامتر رو می‌نویسیم و داخل آرگومان دوم که یک کلوژر هست می‌تونیم کاملا این کوئری رو شخصی‌سازی کنیم.

اگه با URI فعلی، روتر نتونه روت مناسب رو پیدا کنه، بصورت خودکار صفحه و خطای ۴۰۴ به کاربر نشون داده میشه. اگه بخوایم یه سری عملیات قبل از نمایش ۴۰۴ انجام بدیم کافیه از متد fallback به عنوان آخرین آیتم توی فایل web.php استفاده کرد:

Route::get('/home', '...');
Route::get('/posts', '...');
Route::get('/users', '...');

Route::fallback(function() {
    // do something
    
    abort(404);
});
دقت کنین برای زمانی که روتر یک روت مناسب رو پیدا کرده باشه اما مرحله Route Model Binding موفقیت‌آمیز نبوده باشه، متد fallback اجرا نخواهد شد.

متدهای کاربردی 👌
چند روش و متد پرکاربرد که فراوون توی برنامه استفاده میشن رو الان بررسی می‌کنیم.

گرفتن روت فعلی توی برنامه
برای گرفتن ویژگی‌های روت فعلی توی برنامه مثلا توی یک کنترلر از کد زیر استفاده می‌کنیم:

$route = request()->route();
// or
$route = Route::current();
 

بررسی وجود یک پارامتر
اگه یک آدرس داشته باشیم به صورت example.com/users/1 و بخوایم بررسی کنیم آیا یک پارامتر خاص توی آدرس وجود داره از متد hasParameter استفاده می‌کنیم:

Route::get('/users/{username?}/{postId?}', function (Request $request) {
    $route = $request->route();

    dump($route->hasParameter('username')); // true
    dump($route->hasParameter('postId')); // false
});
 

گرفتن مقدار یک پارامتر
برای این کار از متد parameter یا روشی که توی خط ۴ هست استفاده می‌کنیم:

Route::get('/users/{id}', function (Request $request) {
    echo $request->route()->parameter('id');
    // or
    echo $request->route()->id;
});
گرفتن اسم روت
می‌تونیم از یکی از دو روش زیر استفاده کنیم:

Route::get('/home', function (Request $request) {
    dump($request->route()->getName()); // home
})->name('home');

// or

$name = Route::currentRouteName();
 

گرفتن میدل‌ورهای نسبت داده شده به روت
برای این کار از متد gatherMiddleware استفاده می‌کنیم:

Route::get('/home/', function (Request $request) {
    dump($request->route()->gatherMiddleware());
});
 

بررسی وجود یک روت با name خاص
از متد has مثل زیر استفاده می‌کنیم:

Route::get('/home', '...')->name('home');

dump(Route::has('home')); // true
