`JavaScript`中，字符以UTF-16的格式存储，每个字符固定为两个字节。对于那些需要四个字节存储的字符(Unicode码点大于0xFFFF字符)

```js
var s = "𠮷";
s.charCodeAt(0); // 55362
s.charCodeAt(1); // 57271
```

### codePointAt()
此方法可以正确处理四个字节的字符

```js
var s = "𠮷a";
s.codePointAt(0); // 𠮷
s.codePointAt(1); // a
```