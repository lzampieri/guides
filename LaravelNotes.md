## Important things to do in laravel

### Include .js and .css in view

- Add `'mix_url' => env('MIX_ASSET_URL', null),` after     `'asset_url' => env('ASSET_URL', null),` in `config/app.php`
- Add `APP_URL`, `ASSETS_URL`, `MIX_ASSET_URL` in `.env`
- Add `.setResourceRoot( process.env.MIX_ASSET_URL )` just after first call to `mix` in `webpack.mix.js`
- Add
    ```html
    <link href="{{ mix('/css/app.css') }}" rel="stylesheet" />
    <script src="{{ mix('/js/app.js') }}" defer></script>
    ```
    in each view
(if using inertia, an example of the `php` is reported in `InstallLaravelAndInertia.md`)

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

### Remove useless packages
- Execute `composer remove laravel/sanctum`

### Redirect to public
Create a file called `index.php` in the root of the project and add the following content:
```
<?php
header('Location: public/');
```

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

### Send all request to public folder
In the main folder of Laravel app, add an `.htaccess` file and write:
```
RewriteEngine on
RewriteCond %{REQUEST_URI} !^public
RewriteRule ^(.*)$ public/$1 [L]
```

### Manage environment
Install `custom-env`:
```
npm install custom-env
```
and add, on the top of `webpack.mix.js`
```
require('custom-env').env()
```

This way, you can use a `.env` file for local deployment and a `.env.production` file which is automatically used in production environment

