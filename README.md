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
