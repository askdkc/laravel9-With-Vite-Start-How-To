# How to use Vue.js in Laravel 9 with Vite
Recently laravel-mix has been replaced with Vite. This README guides you through setting up
your Laravel project with vite in order to support Vue.js in your blade files.

## Create new Laravel Project
```
laravel new laravel-vite
cd laravel-vite
```

## Install Breeze
```
composer require laravel/breeze --dev
php artisan breeze:install
npm i
```

## Install vite vue plugin and Vue3
```
npm i @vitejs/plugin-vue
npm i vue@next
```

## Modify "vite.config.js"
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel([
            'resources/css/app.css',
            'resources/js/app.js',
        ]),
    ],
});
```
↓ change like this
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

// down below
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel([
            'resources/css/app.css',
            'resources/js/app.js',
        ]),
        
        // add vue({}) setting down below
        vue({
           template: {
               transformAssetUrls: {
                  base: null,
                  includeAbsolute: false,
              },
            },
        }),

    ],
});
```

## Modify "resources/js/app.js"
```
import './bootstrap';

import Alpine from 'alpinejs';

window.Alpine = Alpine;

Alpine.start();
```
↓ change like this
```
import './bootstrap';

// include css file
import '../css/app.css';

// For Vue.js
import { createApp } from 'vue/dist/vue.esm-bundler'

import PostIndex from './components/Posts/index.vue'

const app = createApp({})
app.component('posts-index', PostIndex)

app.mount('#app')
```

## Modify "tailwind.config.js"
```
const defaultTheme = require('tailwindcss/defaultTheme');

/** @type {import('tailwindcss').Config} */
module.exports = {
    content: [
        './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
        './storage/framework/views/*.php',
        './resources/views/**/*.blade.php',
    ],

    theme: {
        extend: {
            fontFamily: {
                sans: ['Nunito', ...defaultTheme.fontFamily.sans],
            },
        },
    },

    plugins: [require('@tailwindcss/forms')],
};
```
↓ change like this
```
const defaultTheme = require('tailwindcss/defaultTheme');

/** @type {import('tailwindcss').Config} */
module.exports = {
    content: [
        './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
        './storage/framework/views/*.php',
        './resources/views/**/*.blade.php',
        
        // add vue files
        './resources/js/**/*.vue',
    ],

    theme: {
        extend: {
            fontFamily: {
                sans: ['Nunito', ...defaultTheme.fontFamily.sans],
            },
        },
    },

    plugins: [require('@tailwindcss/forms')],
};
```

## Now you can use your vue components from your blade file.
```
<x-guest-layout>
    <div id="app">
        <posts-index></posts-index>
    </div>
</x-guest-layout>
```
