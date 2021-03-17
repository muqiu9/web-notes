# TypeScript

## vsCode中自动编译TypeScript

1. 安装typescript

```
npm install -g typescript
```

安装完成后，可在命令行输入`tsc -Version`查看ts版本

2. 命令行执行命令`tsc -init`会生成`tscconfig.json`配置文件。打开该文件修改： outDir 注释去掉，值为编译文件生成的目录。 
3.  点击菜单 Terminal->Run Build Task（任务->运行任务） 选择 tsc：watch（监视）`-tsconfig.json `然后就可以自动生成代码 

## 基础类型
### 介绍
数据单元：数字，字符串，结构体，布尔值等。Typescript支持与Javascript几乎相同的数据类型，此外还提供了实用的枚举类型方便使用。
### 布尔值
- 最基本的数据类型就是简单的true/false值，在JavaScript里叫做boolean
```javascript
let isDone: boolean = false
```
### 数字
- 和JavaScript一样，TypeScript里的所有数字都是浮点数。这些浮点数的类型是number。除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript2015中引入的二进制和八进制字面量
```javascript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```
### 字符串
- 使用string表示文本数据类型，可以使用' 或 "表示字符串。
- 可以使用模版字符串，它可以定义多行文本和内嵌表达式。这种字符串是被反引号包围（`），并且以 ${ expr } 这种形式嵌入表达式。
```javascript
let namestr: string = 'asasas';
namestr = 'aoa';

let name2: string = 'zhangsan'
let age: number = 23
let sent: string = `Hello, my name is ${ name2 }我即将${ age + 1 }岁了`
```
### 数组
- Typescript像JavaScript一样可以操作数组元素。有两种方式可以定义数组。第一种，可以在元素类型后面接上[]，表示此类型元素组成一个数组；
- 第二种方式是使用数组泛型，Array<元素类型>。
```javascript
// 1.元素类型后加[]
let list1: number[] = [1, 2, 3]
// 2.数组泛型
let list2: Array<number> = [1, 2, 3]
```
### 元组 Tuple
- 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。
```javascript
let x: [string, number]
x = ['hello', 21] // OK
x = [21, 'hello'] // Error
```
### 枚举
- enum类型是对JavaScript标准数据类型的一个补充。使用枚举可以为一组数值赋予友好的名字。
```javascript
enum Color {Red, Green, Blue}
let c: Color = Color.Green
console.log(c)  // 1
// 默认情况下，从0开始为元素编号。也可以手动将上面的例子改成从1开始编号
enum Color {Red = 1, Green, Blue}
let c: Color = Color.Green
console.log(c)  // 2
// 或者，全部都采用手动赋值
enum Color {Red = 1, Green = 3, Blue = 4}
let c: Color = Color.Green
console.log(c)  // 3
// 枚举类型提供的一个遍历是你可以由枚举的值得到它的名字。例如，我们知道数值为3，但是不确定它映射到Color里的哪个名字，我们可以查到相应的名字。
enum Color {Red = 1, Green = 3, Blue = 4}
console.log(Color[3]) // Green，3对应Green
```
### Any
- 有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。这种情况下，我们不希望类型检查器丢这些值进行检查而是直接让它们通过编译阶段的检查。那么我们可以使用any类型来标记这些变量：
```javascript
let notSure: any = 4
notSure = '不知道'
notSure = false
```
- 当只知道一部分数据的类型时，any类型也是有用的。比如，有一个数组，它包含了不同类型的数据：
```javascript
let list: any[] = [1, true, "free"]
console.log(list) // [1, true, "free"]
list[1] = 100
console.log(list) // [1, 100, "free"]
```
### Void
- 某种程度上说，void类型像是与any类型相反，它表示没有任何类型。当一个函数没有返回值时，通常其返回值类型是 void：
```javascript
function user(): void {
  console.log("void 表示没有返回值")
}
```
- 声明一个void类型的变量没有什么大用，因为只能赋予undefined和null
```javascript
let voidable: void = undefined
```
### Null和Undefined
- TypeScript里，undefined和null两者各自有各自的类型分别叫做undefined和null。和void像是，它们的本身的类型用处并不是很大：
```javascript
let u: undefined = undefined;
let n: null = null;
```
- 默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

  然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。 再次说明，稍后我们会介绍联合类型。
### Never
- never类型表示的是那些永不存在的值的类型。例如，never类型是那些总是会抛出异常或根本不会有返回值的函数表达式或箭头函数的返回值类型；变量也可能是never类型，当它们被永不为真的类型保护所约束。
  never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。即使any也不可以赋值给never。
  
  下面是一些返回类型的函数
```javascript
function error (message: string): never {
  throw new Error(message )
}
function fail () {
  return error("Something failed")
}
fail()
function infiniteLoop(): never {
  while (true) {
  }
}
infiniteLoop()
```
### Object
- object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。

  使用object类型，就可以更好的表示像Object.create这样的API。例如：
```javascript
declare function create(o: object | null): void;
create({prop: 0})
create(null)

// create(43) // error
// create("234")  // error
```