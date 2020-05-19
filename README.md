# vue-module

## 脚手架搭建一个 vue 项目

```
vue create vue-module
```

### 添加 vue.config.js 并添加 svg-sprite-loader

```
const port = 7070;
const title = "vue项目最佳实践";

const path = require("path");
// 将传入的相对路径转换为绝对路径
function resolve(dir) {
    return path.join(__dirname, dir);
}

module.exports = {
    publicPath: "./",
    devServer: {
        port
    },
    configureWebpack: {
        name: title
    },
    chainWebpack(config) {
        config.module.rule("svg").exclude.add(resolve("src/icons"));

        // 添加svg-sprite-loader
        config.module
            .rule("icons")
            .test(/\.svg$/) //设置test
            .include.add(resolve("src/icons")) //加入include
            .end() // add完上下文进入了数组，使用end回退
            .use("svg-sprite-loader") // 添加loader
            .loader("svg-sprite-loader") // 切换上下文到loader
            .options({
                symbolId: "icon-[name]"
            }) //指定选项
            .end();
    }
};
```

### components 中创建 Icon.vue 文件

```
<template>
    <svg :class="svgClass" aria-hidden="true" v-on="$listeners">
        <use :xlink:href="iconName" />
    </svg>
</template>

<script>
export default {
    name: "Icon",
    props: {
        iconClass: {
            type: String,
            required: true
        },
        className: {
            type: String,
            default: ""
        }
    },
    computed: {
        iconName() {
            return `#icon-${this.iconClass}`;
        },
        svgClass() {
            if (this.className) {
                return "svg-icon " + this.className;
            } else {
                return "svg-icon";
            }
        }
    }
};
</script>

<style scoped>
.svg-icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
}
</style>
```

### src 目录下创建 icons 模块 index.js

```
import Vue from "vue";
import Icon from "@/components/Icon/Icon.vue";

console.log(Icon);

// 图标自动导入
// 利用webpack 的require.context自动导入
// 返回的req是只去加载svg目录中的模块的函数
const req = require.context("./svg", false, /\.svg$/);
console.log(req.keys());
req.keys().map(req);

// Icon组件全局注册一下
Vue.component("Icon", Icon);
```

### 页面直接使用

```
<Icon icon-class="wx" class-name="myicon"></Icon>
```

### 上面案例可直接运行测试
