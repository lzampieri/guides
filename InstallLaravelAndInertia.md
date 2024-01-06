## Full process to install a Laravel-React-Inertia working website

### Install laravel
```
composer create-project laravel/laravel alumnisgss_members_portal "8.*"
```

### Update laravel
Apply all the suggestions which are in `LaravelNotes.md`

### Install inertia and some useful laravel libraries
```
composer require inertiajs/inertia-laravel rap2hpoutre/laravel-log-viewer tightenco/ziggy
```

### Install default npm packages
```
npm i
```

### Install inertia (client side)
```
npm install -D @inertiajs/inertia @inertiajs/inertia-react @inertiajs/progress react react-dom
```
and add `.react()` after `.postCss(...)` in `webpack.mix.js`

Run `npm run dev`: it will install some other useful libraries. Then, run it again to compile.

### Install inertia (server stuff)
Add `@inertiaHead` and `@routes` in the view before `</head>`, and `@inertia` as unique `<body>` child.

Publish inertia middleware:
```
php artisan inertia:middleware
```
and add `\App\Http\Middleware\HandleInertiaRequests::class` as last element in 'web' array in `App\Http\Kernel.php`

Rename the main view as `app.blade.php`

### Setup app.js
Replace the content in `resouces/js/app.js` with:
```js
require('./bootstrap');

import React from 'react'
import { render } from 'react-dom'
import { createInertiaApp } from '@inertiajs/inertia-react'
import { InertiaProgress } from '@inertiajs/progress'

createInertiaApp({
    resolve: name => {
        const page = require(`./Pages/${name}`).default
        return page
    },
    setup({ el, App, props }) {
        render(<App {...props} />, el)
    },
})

InertiaProgress.init( { delay: 0, showSpinner: true } )
```

### Correct inertia bug
Replace
```php
'url' => $request->getBaseUrl().$request->getRequestUri(),
```
with
```php
'url' => $request->getRequestUri(),
```
at line 102 in `vendor\inertiajs\inertia-laravel\src\Response.php`
