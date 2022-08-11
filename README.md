# Laravel 9 with ViteでVue.jsを使う下準備
最新版のLaravelがlaravel-mixからviteになったので、それを使って手軽にVueコンポーネントをLaravelのbladeから呼び出して使うメモ

## 最新版Laravelを入れよう（2022.7.20 Vite3対応）
```
composer global update
```

## 新規Laravelプロジェクト作成
```
laravel new laravel-vite
cd laravel-vite
```

## まずはBreezeを入れる（何故ならTailwindcssが使いやすいから）
```
composer require laravel/breeze --dev
php artisan breeze:install
npm i
```

## viteのvueプラグインとvue3のインストール
```
npm i @vitejs/plugin-vue
npm i vue@latest
```

## vite.config.jsの書き換え
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
    ],
});
```
↓
```
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

//この下追加
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel({
            input: [
                'resources/css/app.css',
                'resources/js/app.js',
            ],
            refresh: true,
        }),
        
//この下のvue({})追加
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

## resources/js/app.jsの修正
Vue用のコンポーネントとして /resources/js/components/Posts/*.vue を作る例。自分のプロジェクトに合わせて適当に変えてね。
```
import './bootstrap';

import Alpine from 'alpinejs';

window.Alpine = Alpine;

Alpine.start();
```
↓
```
import './bootstrap';

// cssの読み込み追加
import '../css/app.css';

import Alpine from 'alpinejs';

window.Alpine = Alpine;

Alpine.start();

// Vue用の設定追加
import { createApp } from 'vue/dist/vue.esm-bundler'

import PostIndex from './components/Posts/index.vue'

const app = createApp({})
app.component('posts-index', PostIndex)

app.mount('#app')
```
↑ちなみにLaravelでvueを使う時の import { createApp } from 'vue'  なのはvue2までで、vue3からは vue/dist/vue-.esm-bundler を指定。[詳しくはオフィシャルドキュメント参照](https://v3.ja.vuejs.org/guide/installation.html#%E3%83%8F%E3%82%99%E3%83%B3%E3%83%88%E3%82%99%E3%83%A9%E3%83%BC%E3%82%92%E4%BD%BF%E3%81%86%E5%A0%B4%E5%90%88 "インストール|Vue.js")。

## tailwind.config.jsの修正（これしないとVueにTailwindcssのスタイルが反映されない）
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
↓
```
const defaultTheme = require('tailwindcss/defaultTheme');

/** @type {import('tailwindcss').Config} */
module.exports = {
    content: [
        './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
        './storage/framework/views/*.php',
        './resources/views/**/*.blade.php',
        
        // vue用の設定追加
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

## guest.blade.php or app.blade.phpを使うと便利
*.blade.phpはテンプレートとして
```
<x-guest-layout>
</x-guest-layout>

もしくは

<x-app-layout>
</x-app-layout>
```
を使うと便利。
何故なら↓のように<head>タグ内に@viteでスクリプトの読み込み設定が既に記載済みなので
```
<head>
    (省略)
    <!-- Scripts -->
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
```

例えば index.blade.php を以下のようにするとresource/js/components/Posts/index.vueが呼び出せる
```
(welcome.blade.phpを↓置き換えちゃうとテストしやすい)

<x-guest-layout>
    <div id="app">
        <posts-index></posts-index>
    </div>
</x-guest-layout>
```

## 試してみよう (Laravel Valet利用例)
```
npm run dev

を実行後表示される

Laravel v9.20.0 

  > APP_URL: http://laravel-vite.test  ←こちらにアクセス(Valet無しの場合、Localhost or 127.0.0.1)
```
