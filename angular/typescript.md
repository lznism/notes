### 变量声明
普通类型的变量声明如下

```js
let isDone: boolean = false;
let num: number = 1234;
let str: string = 'hello world';
let arr:number[] = [1,123,3];
let x:[string, number] = ['hello', 1234];
```

枚举类型

```js
enum Color {Red, Green, Blue};
let c:Color = Color.Blue;
```

任意值
`any`类型可以调用方法
`Object`类型不能调用方法

```js
let notSure: any = 1234;
let notSureArr: any[] = [1,2,3, 'hello'];
```

空值
函数表现为没有任何返回值

```js
function test(): void {
    alert('this is my waining message');
}
```

类型断言

```js
let someValue: any = 'hello world';
let strLen: number = (<string>someValue).length;

let someValue2: any = 'hello world2';
let strLen2: number = (someValue2 as string).length;
```

`typescript`也可以使用解构赋值

```js
function test({a, b}: {a:number, b?:string}): void {}
```

### 接口

```js
interface LabelledObj {
    label: string
};
function printLabel(labelledObj: LabelledObj) {
    console.log(labelledObj.label);
}
```
代表了LabelledObj中必须要有一个label的属性，且其类型为`string`











































