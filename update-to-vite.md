# Mix to Vite ファイルの変えどころ

## まず初めに

最初はLaravelの[オフィシャルドキュメント](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite)に従って必要なファイルを修正しましょう

以下は躓きやすい(忘れがちな)箇所です<br>
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

