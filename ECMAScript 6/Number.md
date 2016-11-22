#### Number.isSafeInteger()
JS能够准确表示整数范围在`-2^53`到`2^53`之间（不含两个断点），超过这个范围无法精确表示这个值
```js
Math.pow(2, 53) === Math.pow(2, 53) + 1;
```
ES6引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限
```js
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
```
`Number.isSafeInteger()`是用来判断一个整数是否落在这个范围内，非数字和小数直接返回`false`
实际使用时，不仅仅要验证结果是否在安全数值的范围内，还要验算参与运算的数值是否也在范围内

#### Math扩展
Math.trunc() 用于清除一个数的小数部分，对于非数值要将其转化为数值，对于空值和无法截取整数的值，返回NaN
Math.sign() 用来判断一个数到底是正数负数还是0（注意会返回+0，-0，NaN）
Math.cbrt() 用来计算一个值的立方根
Math.clz32() 这个方法返回一个数的32位无符号整数形式，`Math.clz32()`方法返回一个数的32位无符号整数形式有多少个前导0
Math.imul() Math.imul方法返回两个数以32位带符号整数形式相乘的结果，返回的也是一个32位的带符号整数。
Math.fround() 	返回一个数的单精度浮点数形式。
Math.hypot() 	返回所有参数的平方和的平方根
Math.expm1() 	返回ex - 1，即Math.exp(x) - 1
Math.log1p() 	返回1 + x的自然对数
Math.log10()	返回以10为底的x的对数
ES7新增了一个指数运算符（**）
Math.sinh(x) 返回x的双曲正弦（hyperbolic sine）
Math.cosh(x) 返回x的双曲余弦（hyperbolic cosine）
Math.tanh(x) 返回x的双曲正切（hyperbolic tangent）
Math.asinh(x) 返回x的反双曲正弦（inverse hyperbolic sine）
Math.acosh(x) 返回x的反双曲余弦（inverse hyperbolic cosine）
Math.atanh(x) 返回x的反双曲正切（inverse hyperbolic tangent）
Math.log2() 返回以2为底的x的对数。如果x小于0，则返回NaN。