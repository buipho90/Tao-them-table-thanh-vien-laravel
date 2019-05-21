## Tao-them-table-thanh-vien-laravel
Cách tạo thêm tabale thành viên
Để tạo thêm table thành vi nữa trong laravel ta làm như sau: Ví dụ mặc định trong laravel có table là users nhưng ta muốn tạo thêm table khác nữa là members thì làm như sau:

### 1. Ta nhân bản table users ra thành members

### 2. Ta tạo model cho table member như sau: 

*php artisan make:model Thanhvien* 

`Trong code sẽ sửa lại như sau:`


```PHP

namespace App;

use Illuminate\Database\Eloquent\Model;

use Illuminate\Notifications\Notifiable;

use Illuminate\Foundation\Auth\User as Authenticatable;

class Thanhvien extends Authenticatable

{

   use Notifiable;

    protected $table = 'members';

    protected $fillable = ['email',  'password'];

    protected $hidden = ['password',  'remember_token'];

}
```
### 3. Ok giờ ta vào ```config/auth.php``` để thêm phần sau:
```PHP
'defaults' => [
        'guard' => 'web',
        'passwords' => 'users',
    ],
    // sau khi thêm
    'thanhvien'  => [
          'driver'  => 'eloquent',
          'provider' => \App\Thanhvien::class,
    ],
    // kéo xuong sua tiep
    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
        'thanhvien'  => [
            'driver'  => 'session',
             'provider' => 'thanhvien',
        ],


        'api' => [
            'driver' => 'token',
            'provider' => 'users',
        ],
    ],
    
    'providers' => [
        'users' => [
            'driver' => 'eloquent',
            'model' => App\User::class,
        ],
        'thanhvien' => [
            'driver' => 'eloquent',
            'model' => \App\Thanhvien::class,
        ],

        // 'users' => [
        //     'driver' => 'database',
        //     'table' => 'users',
        // ],
    ],
    
    'passwords' => [
        'users' => [
            'provider' => 'users',
            'table' => 'password_resets',
            'expire' => 60,
        ],
        'thanhvien' => [
            'provider' => 'thanhvien',
            'table' => 'password_resets',
            'expire' => 60,
        ],
    ],
```

### 4. Tiếp theo tạo ```middleware``` với câu lệnh như sau: ```php artisan make:middleware RedirectIfNotThanhvien```


Sau tạo xong và ta sửa lại được như thế này

```PHP
<?php

namespace App\Http\Middleware;

use Closure;

class RedirectIfNotThanhvien
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next, $guard="thanhvien")
    {
        if(!auth()->guard($guard)->check()) {
            return redirect(route('frontend.login'));
        }
        return $next($request);
    }
}
```

### 5. Tiếp theo ta vào app/Http/Kernel.php để thêm dòng sau   
```PHP
 protected $routeMiddleware = [
   ......
   'thanhvien' => \App\Http\Middleware\RedirectIfNotThanhvien::class,
 ];
```
### 6. Tiếp theo tạo Controller cho member ví dụ mình tạo như này ```php artisan make:controller MemberControler```

### Trong đó mình khai báo nhu sau
```PHP
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Foundation\Auth\AuthenticatesUsers;
use Illuminate\Support\Facades\Auth;

class MemberLoginControler extends Controller
{
    use AuthenticatesUsers;

    protected $redirectTo = '/profile-thanh-vien';

    
    public function __construct()
    {
      $this->middleware('guest')->except('logout');
    }
    
    public function guard()
    {
     return Auth::guard('thanhvien');
    }

    
    public function showLoginForm()
    {
        return view('frontend.login');
    }

}
```

### 7. Ok giờ phần Route mình khai báo như sau
```PHP
Route::get('/thanh-vien/login', 'MemberLoginControler@showLoginForm')->name('thanhvien.login');
Route::post('/thanh-vien/login', 'MemberLoginControler@login')->name('thanhvien.login.post');
Route::post('/thanh-vien/logout', 'MemberLoginControler@logout')->name('thanhvien.logout');


Route::group(['middleware'=>'thanhvien'], function() {
    Route::get('/thanh-vien/home', 'MemberLoginControler@index');
});
```
### 8. Gio tạo form đăng nhap login.blade.php
```PHP
<form class="form-horizontal" method="POST" action="{{ route('thanhvien.login.post') }}">
    {{ csrf_field() }}

    <div class="form-group{{ $errors->has('email') ? ' has-error' : '' }}">
        <label for="email" class="col-md-4 control-label">E-Mail Address</label>

        <div class="col-md-6">
            <input id="email" type="email" class="form-control" name="email" value="{{ old('email') }}" required autofocus>

            @if ($errors->has('email'))
                <span class="help-block">
                    <strong>{{ $errors->first('email') }}</strong>
                </span>
            @endif
        </div>
    </div>

    <div class="form-group{{ $errors->has('password') ? ' has-error' : '' }}">
        <label for="password" class="col-md-4 control-label">Password</label>

        <div class="col-md-6">
            <input id="password" type="password" class="form-control" name="password" required>

            @if ($errors->has('password'))
                <span class="help-block">
                    <strong>{{ $errors->first('password') }}</strong>
                </span>
            @endif
        </div>
    </div>

    <div class="form-group">
        <div class="col-md-6 col-md-offset-4">
            <div class="checkbox">
                <label>
                    <input type="checkbox" name="remember"> Remember Me
                </label>
            </div>
        </div>
    </div>

    <div class="form-group">
        <div class="col-md-8 col-md-offset-4">
            <button type="submit" class="btn btn-primary">
                Login
            </button>

        </div>
    </div>
</form>
```

## Chúc cả nhà thành công
