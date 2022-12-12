## Important things to do in laravel

### Include .js and .css in view

- Add `'mix_url' => env('MIX_ASSET_URL', null),` after     `'asset_url' => env('ASSET_URL', null),` in `config/app.php`
- Add `APP_URL`, `ASSETS_URL`, `MIX_ASSET_URL` in `.env`
- Add
    ```html
    <link href="{{ mix('/css/app.css') }}" rel="stylesheet" />
    <script src="{{ mix('/js/app.js') }}" defer></script>
    ```
    in each view, 

### Manage versioning and notification
- Add, in the end of `webpack.mix.js`:
    ```js
    if (mix.inProduction()) {
        mix.version();
    } else {
        mix.disableNotifications();
    }
    ```

### Manage db tables
- Add `DB_TABLES_PREFIX='...'` in `.env`
- Set `'prefix' => env('DB_TABLES_PREFIX', ''),` in `config/database.php` in the `mysql` section (around row 50)

### Manage protocol
- Add `\URL::forceScheme('https');` in `app\Providers\AppServiceProvider.php` inside the `boot()` method
- Create the file `App\Http\Middleware\ForceHttps.php` with the code:
```
<?php
namespace App\Http\Middleware;
use Closure;
class ForceHttps
{
    public static function isHTTPS()
    {
        // Checking the port
        if ($_SERVER['SERVER_PORT'] == 443) {
            return true;
        }
        // Checking the direct protocol
        if (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] == 'on') {
            return true;
        }
        // Checkinf the forwarded protocol
        if (!empty($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https' || !empty($_SERVER['HTTP_X_FORWARDED_SSL']) && $_SERVER['HTTP_X_FORWARDED_SSL'] == 'on') {
            return true;
        }
        return false;
    }
    public function handle($request, Closure $next)
    {
        if (!ForceHttps::isHTTPS()) {
            return redirect(env('APP_URL'));
        }
        return $next($request);
    }
}
```
and add the row `\App\Http\Middleware\ForceHttps::class` in the `$middleware` array in `App\Http\Kernel.php`