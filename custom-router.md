`App\Providers\RouteServiceProvider.php`

##Mình tìm 

```PHP
public function map()
    {
        $this->mapApiRoutes();
        
        $this->mapWebRoutes();

    
    }
```

 ##Bổ xung thêm

 ```$this->mapAdminRoutes();```

 ##như vậy ta được đầy đủ như sau
 ```PHP
 public function map()
    {
        $this->mapApiRoutes();
        $this->mapAdminRoutes();
        $this->mapWebRoutes();

    
    }
```
##Thêm đoạn code này xuống dưới
 ```PHP
 protected function mapAdminRoutes()
    {
        Route::prefix("quantri")
            ->middleware("web")
            ->namespace($this->namespace)
            ->group(base_path('routes/admin.php'));
    }
```
