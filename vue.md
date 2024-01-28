# vue

## vue cli 2.0

```bash
# install
npm install vue
npm install -g @vue/cli-init
```

## vue cli 3.0

```bash
# install
npm install -g @vue/cli-service-global

# create project
vue create my_project

# ui
cd my_project
vue ui

# install dep
vue add @vue/eslint
vue add router
vue add vuex
vue add element

# run dev
npm run serve

# build
npm run build
```

## component structure

```js
// import public library
import { mapGetters, mapMutations } from 'vuex'

// import components
import Loading from './components/Loading'

// import api
import { auth } from './api'
import { env } from './api/config'

// import others

export default {
  name: 'app',
  // ui
  components: {
    Loading,
  },
	// data
  data () {
    return {
      login: false
    }
  },
  computed: {
    ...mapGetters(['loading', ])
  },
  // methods
  created () {
  },
  mounted () {
  },
  methods: {
    ...mapMutations(['SAVEUSER'])
    ...
  }
}
```

## main.js

```js
import Vue from "vue";
import ElementUI from "element-ui";
import locale from "element-ui/lib/locale/lang/en";

// element-ui
Vue.use(ElementUI, {
  locale,
});

// rem 比例适配
Vue.prototype.j = (num) => {
  const w = window.innerWidth / 1350;
  const h = window.innerHeight / 759;
  const scale = Math.min(w, h);
  return num * scale;
};

// theme
Vue.prototype.themed = (key) => {
  const theme = document.body.getAttribute("data-theme");
  return {
    light: {},
    dark: {
      background: "#001622",
      "background-opaque": "#00162266",
      midground: "#011d32",
      "midground-border": "#003E52",
      foreground: "#00263C",
      "foreground-border": "#9B9DA9",
      hover: "#004B64",
      "out-range": "#004B64",
      "in-range": "#021E33",

      color: "#f0f1f1",
      color2: "E6FAFA",
      primary: "#00bcbc",
      secondary: "#009292",
      tertiary: "#B7E8EA",
      warning: "#fff794",
      serious: "#FF6347",
      error: "#ff231e",
      placeholder: "#9B9DA9",
      border: "#9B9DA9",
      scrollbar: "#1B3B52",
      button: "#001622",
      disabled: "#001622",
      "disabled-background": "#004B64",
    },
  }[theme][key];
};
```
