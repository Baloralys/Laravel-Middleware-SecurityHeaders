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
// Multiple Domains are separated with one spacer
// Example: YOUR-DOMAIN-1.XYZ YOUR-DOMAIN-2.XYZ YOUR-DOMAIN-3.XYZ
    protected $csp = "default-src 'self' YOUR-DOMAIN.XYZ; script-src 'self' YOUR-DOMAIN.XYZ; style-src 'self' YOUR-DOMAIN.XYZ; img-src 'self' YOUR-DOMAIN.XYZ; frame-src YOUR-DOMAIN.XYZ; font-src 'self' YOUR-DOMAIN.XYZ";

    /*
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
        $response->header("Strict-Transport-Security","max-age=31536000; includeSubDomains");
// Prevent Clickjacking
        $response->header("X-Frame-Options","DENY");
// Block Access If XSS Attack Is Suspected
        $response->header("X-XSS-Protection","1; mode=block");
// Prevent MIME-Type Sniffing
        $response->header("X-Content-Type-Options","nosniff");
// Referrer Policy
        $response->header("Referrer-Policy","no-referrer-when-downgrade");
// Content-Security-Policy
        //$response->header('Content-Security-Policy','default-src https: data: "unsafe-inline" "unsafe-eval"');
        $response->header('Content-Security-Policy',''.$this->csp.'');
        $response->header('X-Content-Security-Policy',''.$this->csp.'');
        //$response->header('X-WebKit-CSP',''.$this->csp.'');
// Feature-Policy
        $response->header("Feature-Policy","microphone 'none'; payment 'none'; sync-xhr 'self' ".env('APP_URL')."");
// Expect-CT-Header
        $response->header('Expect-CT', 'enforce, max-age=21600');
        // ACAH - Access Control Allow Headers/Origin
        $response->header("Access-Control-Allow-Origin", url('/'));
        $response->header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
// Remove Headers
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

Terminal: using command in Project root Folder
```cmd
php artisan optimize
```

# That's all... have fun and feel free to use it.
