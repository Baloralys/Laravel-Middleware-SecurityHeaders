# Laravel-Middleware-SecurityHeaders
### (Laravel 5.8.*)
1. Step

Terminal: using command in Project root Folder
```cmd
php artisan make:middleware SecurityHeaders
```
2. Step

app/Http/Middleware/SecurityHeaders.php

```php
<?php

namespace App\Http\Middleware;

use Closure;

class SecurityHeaders
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $response = $next($request);
// Enforce the use of HTTPS
        $response->header('Strict-Transport-Security','max-age=31536000; includeSubDomains');
// Prevent Clickjacking
        $response->header('X-Frame-Options','SAMEORIGIN');
// Block Access If XSS Attack Is Suspected
        $response->header('X-XSS-Protection','1; mode=block');
// Prevent MIME-Type Sniffing
        $response->header('X-Content-Type-Options','nosniff');
// Referrer Policy
        $response->header('Referrer-Policy','no-referrer-when-downgrade');
// Content-Security-Policy
        //$response->header('Content-Security-Policy','default-src https: data: "unsafe-inline" "unsafe-eval"');
        //$response->header('Content-Security-Policy','default-src "self"; script-src "self"');
        //$response->header('X-Content-Security-Policy','default-src "self"; script-src "self"');
        //$response->header('X-WebKit-CSP','default-src "self"; script-src "self"');
// Feature-Policy
        $response->header('Feature-Policy','vibrate "none"; geolocation "none"');
        // Remove X-Powered Header
        //$response->header('X-Powered-By', 'Whatever you want!');
        $response->headers->remove('x-powered-by');
        return $response;
    }
}
```
3. Step

app/Http/Kernel.php adding these line
```php
protected $middleware = [
//....
\App\Http\Middleware\SecurityHeaders::class,
];
```

4. Step

edit routes/web.php, use the group function to all your defined Routes like this
```php
Route::group(['middleware' => 'SecurityHeaders'], function() {

// all your Routes that you defined
// Example

Route::get('/', function() {
return view('welcome');
});

});
```

That's all... have fun and feel free to use it.
