## Tao-them-table-thanh-vien-laravel
Cách tạo thêm tabale thành viên
Để tạo thêm table thành vi nữa trong laravel ta làm như sau: Ví dụ mặc định trong laravel có table là users nhưng ta muốn tạo thêm table khác nữa là members thì làm như sau:

**1. Ta nhân bản table users ra thành members**

**2. Ta tạo model cho table member như sau: **

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
Ok giờ ta vào ```config/auth.php``` để thêm phần sau:
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
