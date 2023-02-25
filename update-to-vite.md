# Mix to Vite ファイルの変えどころ

## `resources/js/app.js` の例

`mix時代`

```vim
require('./bootstrap');

window.Vue = require('vue').default;

import * as Vue from 'vue';

const app = Vue.createApp({});

app.mount('#app');
```

↓ window.vueは消して良い

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

↓ requireは殺す

`vite way`

```vim
import _ from 'lodash';
window._ = _;

import 'bootstrap';

import axios from 'axios';
window.axios = axios;

window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';
```

