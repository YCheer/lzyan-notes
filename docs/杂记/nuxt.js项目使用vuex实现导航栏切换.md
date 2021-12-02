## Vuex是什么

有关Vuex用法和详情可以去nuxt官网https://zh.nuxtjs.org/guide/vuex-store/，或者vue官网https://vuex.vuejs.org/zh/详细阅读。

## 记录使用

在store目录下创建一个 `index.js` 文件

![image-20200829104533996.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832924202171367424.png)

添加代码

```javascript
export const state = () => ({
    currentPage: 'index'
})

export const mutations = {
    setCurrentValue(state, value) {
        state.currentPage = value
    }
}
```

配置完成，然后通过 `$store` 可以直接调用，在这里进行状态的判断

![image-20200829104839062.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832924278952296448.png)

然后当每个页面加载的时候都会调用 `mounted` 方法，所以就在每个页面 `mounted` 的时候去改变一下 `currentPage` 的状态，调用方法参考文档中所描述的

![image-20200829105131602.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832924350079303680.png)

![image-20200829105240544.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832924416760348672.png)

完成
