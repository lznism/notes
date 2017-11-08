### 1.用法
使用`keep-alive`缓存组件时，会缓存不活动的组件实例，而不是销毁他们

```html
<keep-alive>
	<component></component>
</keep-alive>
```

上述做法可以用来缓存组件，避免多次加载，减少组件的性能损耗

如果我们需要缓存一整个页面，可以采用如下的方式

```html
<keep-alive>
	<router-view></router-view>
</keep-alive>
```

上面的这种做法，**如果页面一加载就会触发一个请求**，可以将首次触发请求的写在`created`钩子中，就能实现缓存，这样就不必每次切换页面时都产生这个请求

### 2.缓存部分页面或者组件

使用`router.meta`属性

```html
<keep-alive>
	<router-view v-if="$route.meta.keepAlive"></router-view>
</keep-alive>
```

router里面可以采用如下设置

```js
routes [{
	path: '/',
	component: Home,
	name: 'home',
	meta: {
		keepAlive: true
	}
}]
```

使用`include/exclude`两个属性，可以针对性缓存相应的组件

```html
<keep-alive>
	<component include="a,b"></component>
</keep-alive>
<keep-alive>
	<component exclude="/a|b/"></component>
</keep-alive>
```

上述方式就实现了对于单个组件的缓存