#### Install tailwind
```shell
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
#### Configure tailwind
Replace the content in `tailwind.config.js` with
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
    content: [
        "./resources/**/*.blade.php",
        "./resources/**/*.js",
        "./resources/**/*.vue",
    ],
    theme: require('./resources/js/theme.js'),
    plugins: [],
}
```
and create the file `resources/js/theme.js` with content `module.exports = {}`
#### Add base directories
Replace `resources/css/app.css` content with
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```