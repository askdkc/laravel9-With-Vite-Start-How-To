# Mix to Vite ファイルの変えどころ

## まず初めに

最初はLaravelの[オフィシャルドキュメント](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite)に従って必要なファイルを修正しましょう

### 基本的には

- viteプラグインインストール
```bash
npm install --save-dev vite laravel-vite-plugin
```

VueかReactに応じて追加インストール
- Vue
```bash
npm install --save-dev @vitejs/plugin-vue
```
- React
```bash
npm install --save-dev @vitejs/plugin-react
```
- `vite.config.js`の作成
```vim
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
// import react from '@vitejs/plugin-react';
// import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel([
            'resources/css/app.css',
            'resources/js/app.js',
        ]),
        // react(),
        // vue({
        //     template: {
        //         transformAssetUrls: {
        //             base: null,
        //             includeAbsolute: false,
        //         },
        //     },
        // }),
    ],
});
```

- NPMスクリプト`package.json`の更新

before
```javascript
  "scripts": {
     "dev": "npm run development",
     "development": "mix",
     "watch": "mix watch",
     "watch-poll": "mix watch -- --watch-options-poll=1000",
     "hot": "mix watch --hot",
     "prod": "npm run production",
     "production": "mix --production"
  }
```
↓
after
```javascript
  "scripts": {
     "dev": "vite",
     "build": "vite build"
  }
```

- メインテンプレートのリソース読み込み修正

before
```php
<link rel="stylesheet" href="{{ mix('css/app.css') }}">
<script src="{{ mix('js/app.js') }}" defer></script>
```
↓
after
```php
@vite(['resources/css/app.css', 'resources/js/app.js'])
```

> **細かいところは**
> 
> [オフィシャルドキュメント](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite)をちゃんと見てね

## 以下は躓きやすい(忘れがちな)箇所です<br>
👇

## `resources/js/app.js` の例

`mix時代`

```vim
require('./bootstrap');

window.Vue = require('vue');

import * as Vue from 'vue';

const app = Vue.createApp({});

app.mount('#app');
```

↓ window.vue(使ってた人)は消して良い

`vite way`

```vim
import './bootstrap';

import * as Vue from 'vue/dist/vue.esm-bundler'

const app = Vue.createApp({});

app.mount('#app');
```


## `resources/js/bootstrap.js` の例

`mix時代`

```vim
window._ = require('lodash');

try {
    require('bootstrap');
} catch (e) {}

window.axios = require('axios');

window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';
```

↓ requireは殺す(importに換えるのです)

`vite way`

```vim
import _ from 'lodash';
window._ = _;

import 'bootstrap';

import axios from 'axios';
window.axios = axios;

window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';
```

