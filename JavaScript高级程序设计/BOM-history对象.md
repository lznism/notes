处于安全性的考虑，开发人员无法获取用户浏览过的URL,但是可以实现前进和后退
```js
history.go(-1); // 后退一页
history.go(1);  // 前进一页
history.go(2);  // 前进两页
```
同样也可以接收一个字符串，此时浏览器会跳转到历史记录中该字符串的第一个位置
```js
history.go('lznism.com'); // 跳转到最近的lznism.com页面
```
两个直观的写法
```js
- history.back();   // 后退一页
- history.foward(); // 前进一页
```
`history.length`保存在历史记录的数量
