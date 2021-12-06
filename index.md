# 枚举 enum
```
enum CardSuit {
  Clubs,
  Diamonds,
  Hearts,
  Spades
}
enum EvidenceTypeEnum {
  UNKNOWN = "as",
  PASSPORT_VISA = "passport_visa",
  PASSPORT = "passport",
  SIGHTED_STUDENT_CARD = "sighted_tertiary_edu_id",
  SIGHTED_KEYPASS_CARD = "sighted_keypass_card",
  SIGHTED_PROOF_OF_AGE_CARD = "sighted_proof_of_age_card"
}

// 数字枚举   可以反向映射
console.log(CardSuit); // {0: "Clubs", 1: "Diamonds", 2: "Hearts", 3: "Spades", Clubs: 0…}
console.log(CardSuit[0]); // Clubs
console.log(CardSuit.Clubs); // 0
console.log(CardSuit[CardSuit.Spades]); //  Spades

//  字符串枚举   不可以反向映射
console.log(EvidenceTypeEnum); // {UNKNOWN: "as", PASSPORT_VISA: "passport_visa", PASSPORT: "passport", SIGHTED_STUDENT_CARD: "sighted_tertiary_edu_id", SIGHTED_KEYPASS_CARD: "sighted_keypass_card"…}
console.log(EvidenceTypeEnum.PASSPORT_VISA); //  passport_visa
console.log(EvidenceTypeEnum.as); // undefined error:不存在此属性
```

## TypeScirpt学习笔记
# TypeScript学习笔记
TypeScript是JavaScript的超集，添加了静态类型检查和OOP特性。
TypeScript的云环境：https://www.typescriptlang.org/zh/play
[TOC]
基础类型
```
let str: string = "jimmy";
let num: number = 24;
let bool: boolean = false;
let u: undefined = undefined;
let n: null = null;
let sym: symbol = Symbol("me"); 
```

默认情况下 null 和 undefined 是所有类型的子类型，即可以把 null 和 undefined 赋值给其他类型。
```
let str:string = "666";
// strict模式下会报错，不建议使用
str = null;
str= undefined;
```

原始类型 number、string、boolean、symbol 混淆的首字母大写的 Number、String、Boolean、Symbol 类型，后者是相应原始类型的包装对象。在声明类型时，不要使用包装对象来注释值的类型。
```
let Num: Number = 2;
let num:number = Num;  // Type 'Number' is not assignable to type 'number'.

```
引用类型
# 对象
```
// 对象
// 使用1
let obj: object = {x: 1,y:'2'};
obj.x = 2; // 报错：Property 'x' does not exist on type 'object'.

//使用2：interface/type
interface Person { // 使用type也可以
  name: string;
  age?: number; // age属性可以有，可以没有
  say(): string; // say函数返回值为string
  [propname: string]: any; // 当前Hero可定义任意字符串类型的key
}
const student:Person = {
    name:'zhangsan',
    say(){	// 使用this引用student属性时，必须要用function函数，不能用箭头函数，因为箭头函数的this指向student的父级作用域
        return `his name is a ${this.name}`
    },
    kill:'study'  // 新属性
}
student.name = 'lisi'
student.father = 'Jock' // 新属性

// 使用3:继承 extends可以扩展属性
interface Person { 
  name: string;
  age?: number; 
  say(): string; 
}

interface Student extends Person {
  kill: 'learning'; // kill定义为了常量
  teacher:string
}
const stu1:Student = {
    name:'wanger',
    kill:'learning',
    teacher:'Jock',
    say(){
        return '60'
    }
}

// 使用4:
interface AnyObject {
  [key: string]: any;
}

小 object 代表的是所有非原始类型，也就是说我们不能把 number、string、boolean、symbol等 原始类型赋值给 object。在严格模式下，null 和 undefined 类型也不能赋给 object。
let obj:object;
obj = 1; // error
obj = {}; // ok
obj = {x:1} // ok

大Object 和{}代表所有拥有 toString、hasOwnProperty 方法的类型，所以所有原始类型、非原始类型都可以赋给 Object。同样，在严格模式下，null 和 undefined 类型也不能赋给 Object。
let obj: Object; // let obj:{};
obj = 1; // ok
obj = {}; // ok
obj = null; // strict:error
```


# 数组
```
// 数组
let arr:string[] = ["1","2"]; // 常用
let arr:Array<string> = ["1","2"]；
let arr = new Array<any>();
```

let arr:(number | string)[] = [1,'2'] // 联合类型

# 元组
```
// 元组
// 使用1:基本使用
let x: [string, number]; 
// 类型必须按顺序匹配且个数必须为2
x = ['hello', 10]; // OK 
x = ['hello', 10,10]; // Error 
x = [10, 'hello']; // Error

let x: [string, number?]; 
x = ['hello']
x = ['hi', 2]

```
// 使用2:解构赋值
let employee: [number, string] = [1, "Semlinker"];
let [id, username] = employee;

// 使用3:元组类型的剩余元素
type RestTupleType = [number, ...string[]];
let restTuple: RestTupleType = [666, "Semlinker", "Kakuqo", "Lolo"];
console.log(restTuple[0]);
console.log(restTuple[1]);

// 使用4：只读的元组类型
const point: readonly [number, number] = [10, 20];

枚举
// 使用1:普通枚举 初始值默认为 0 其余的成员按顺序每次增长1
enum Color {
  RED, //0
  PINK,//1
  BLUE,//2
}

// 使用2:设置初始值
enum Color {
  RED = 10,
  PINK,  //11
  BLUE,  //12
}

// 使用3；每个都需要定义
enum Color {
  RED = "红色",
  PINK = "粉色",
  BLUE = "蓝色",
}

const pink: Color = Color.PINK;
console.log(pink); // 粉色

// 使用4:枚举本质上相当于联合类型
// 相当于 'mac' | 'android' | 'iphone'
enum DeviceType {
  MAC = 'mac',
  ANDROID = 'android',
  IPHONE = 'iphone',
}

const deviceTypeConfig: Record<DeviceDataType, string> = {
  [DeviceDataType.MAC]: 'SuiteAdmin_View_Graph_Mac',
  [DeviceDataType.IPHONE]: 'SuiteAdmin_View_Graph_iPhone',
  [DeviceDataType.ANDROID]: 'SuiteAdmin_View_Graph_Android',
};

const DeviceMap = [{type:'mac',count:30},{type:'android',count:50},{type:'iphone',count:40}]
DeviceMap.map(item => ({
  ...item,
  i18n: deviceTypeConfig[item.type as DeviceType] // DeviceMap的type值可能是DeviceType中的值
}))

any/unknown/never/void
any
任何类型都可以归类于any类型。
let x:any = 1
// 任何类型都可以赋值给any
x = '1'
x = {}
// any可以赋值给任何类型
let y:string = x;
console.log(y, typeof y) //{},  "object"

unknown
any权限太大，因此引入了unknown。
let x:unknown = 1
// 任何类型都可以赋值给unknown
x = '1'
// unknown只可以赋值给any、unknown
let y:string = x; // error
let z:any = x;

never
never类型表示那些用不存在的值的类型。
// 使用1:异常
function err(msg: string): never { // OK
  throw new Error(msg); 
}

// 使用2:死循环
function loopForever(): never { // OK
  while (true) {};
}

// 使用3:类型的全面检查
type Foo = string | number;

function controlFlowAnalysisWithNever(foo: Foo) {
  if (typeof foo === "string") {
    // 这里 foo 被收窄为 string 类型
  } else if (typeof foo === "number") {
    // 这里 foo 被收窄为 number 类型
  } else {
    // foo 在这里是 never
    const check: never = foo;
  }
}

void
void表示没有任何类型，只能赋值null和undefined。
// 使用1
let x:void;
x = null;
x = undefined;

// 使用2
function fun(a:number,b:string):void{
  console.log(a+b)
}

类型推断/类型断言/非空断言/字面量类型
类型推断
在 TypeScript 中，具有初始化值的变量、有默认值的函数参数、函数返回的类型都可以根据上下文推断出来。
let a  = 'hello'; // 推断为string

function add(a:number,b:number){
  return a + b  // 推断返回值为number
}

类型断言
// as 语法
let a: any = "this is a string";
let len: number = (a as string).length; // a.length

// 尖括号：不推荐，和react的jsx语法冲突
let a: any = "this is a string";
let len: number = (<string>a).length;

非空断言
let flag: null | undefined | string;
flag!.toString(); // ok
flag.toString(); // error

字面量类型
let str: "hello" = "hello"; // 相当于只读
str = 'hi' // error

联合类型和交叉类型
联合类型表示取值可以为多种类型中的一种，使用 | 分隔每个类型。
let a: string | number;
a = 'hello'; // OK
a = 1; // OK

交叉类型是将多个类型合并为一个类型，它包含了所需的所有类型的特性，使用&定义交叉类型。
// type
type Flag1 = { x: number };
type Flag2 = Flag1 & { y: string };

let a: Flag2 = {
  x: 1,
  y: "hello",
};

// interface
interface IPerson {
  id: string;
  age: number;
}

interface IWorker {
  companyId: string;
}

type IStaff = IPerson & IWorker;

const staff: IStaff = {
  id: 'E1006',
  age: 33,
  companyId: 'EFT'
};

类型别名和接口
// type：类型别名用来给一个类型起个新名字
// 使用1:基本使用
type Message = string | string[];

let greet = (message: Message) => {
  // ...
};
// 使用2:readonly和?
type Person1 = {
    readonly name:string,
    age?:number
}
let joke:Person1 = {
    name:'joke'
}
// 使用3:类型别名扩展
type PointX = {
    x: number
}
type Point = PointX & {
    y: number
}

// interface：接口是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）
// 使用1:使用readonly和?
interface Person2 {
    readonly name: string; // 只读
    age?: number; // 属性可以有，可以没有
}
let tom: Person2 = {
    name: 'Tom',
    age: 25
};
// 使用2：任意属性
interface Person3 {
    name: string;
    age?: number;
    [propName: string]: any; // 可以添加任何的属性
}
// 使用3:接口扩展
interface PointX {
    x: number
}
interface Point extends PointX {
    y: number
}

泛型
多个类型参数
function func <T, U>(value: T, message: U) : T {
  console.log(message);
  return value;
}
console.log(func<number, string>(68, "Semlinker"));
// 也可以写成：类型推断
console.log(func(68, "Semlinker"));

泛型约束
interface Lengthwise {
  length: number;
}

function identity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

泛型别名和泛型接口
// 泛型别名
type Cart<T> = { list: T[] };
let cart: Cart<string> = { list: ["1"] };

// 泛型接口
interface Cart<T> {
  list: T[]
}
let cart: Cart<{ name: string; price: number }> = {
  list: [{ name: "hello", price: 10 }],
};

泛型类
class MyArray<T> {
  private list: T[] = [];
  add(value: T) {
    this.list.push(value);
  }
  getMax(): T {
    let result = this.list[0];
    for (let i = 0; i < this.list.length; i++) {
      if (this.list[i] > result) {
        result = this.list[i];
      }
    }
    return result;
  }
}
let arr = new MyArray<number>();
arr.add(1);
arr.add(2);
arr.add(3);
let res = arr.getMax();
console.log(res);

函数
函数定义
// 指定参数类型和返回值类型
function hello(name: string): void {
  console.log("hello", name);
}
hello("hahaha");

函数表达式
type SumFunc = (x: number, y: number) => number;

let sum: SumFunc = function (a, b) {
  return a + b;
};

可选参数
// 可以1个或多个，必须相连且最后一个是可选参数
function print(name: string, age?: number): void {
  console.log(name, age);
}
print("hahaha");

默认参数
// 可以1个或多个，必须相连且最后一个是可选参数
function print(name: string, age?:number=18): void {
  console.log(name, age);
}
print("hahaha");

剩余参数
function push(arr: any[], ...items: any[]) {
    arr.push(...items)
}
let a:any = [];
push(a, 1, 2, 3);
console.log(a)

函数重载
let obj: any = {}; // 不可以是 let obj:object = {}，不然obj.name访问会报错
function attr(val: string): void;
function attr(val: number): void;
function attr(val: any): void {
  if (typeof val === "string") {
    obj.name = val;
  } else if(typeof val === "number") {
    obj.age = val;
  }
}
attr("hahaha");
attr(9);
attr(true); // error
console.log(obj);

类
成员属性和成员方法
成员属性不加修饰符默认为public，可以访问和修改。public表示类对象、子类、类本身可以访问和修改；protected表示子类、类本身可以访问和修改；private表示类本身可以访问和修改。
readonly修饰符表示只读，此属性无setter；类中的成员属性可以通过getter和setter来实现数据的封装，但是其命名不能和成员属性相同。
!表示对象实例化时未赋值。
class Person {
  private readonly _name: string; 
  protected _age:number;
  // constructor未赋值
  // 方法1:非空断言（不推荐）
  // public height!:string;
  // 方法2: ？(推荐)
  public height?:string;
  // 方法3: 直接赋值
  // public height ='';
  weight = '100斤'   // 设置初始值
  constructor(_name: string,age:number) {
    this._name = _name;
    this._age = age;
  }

  get name():string{  // _name只读
      return this._name
  }

  get age():number{
      return this.age
  }

  set age(value:number){ // set不能设置返回值类型为void
      this._age = value
  }

  printnfo(): void {
    console.log(this._name);
  }
}

const p = new Person('zhangsan',18)
console.log(p)
p.height = '170mm'
p.age = 20
p.weight = '120斤'
console.log(p)
// p.name = 'lisi' // error

静态属性和静态方法
静态属性和静态方法时属于类本身的属性和方法，不属于对象。
class Person {
  static count = 0;
  name:string;
  age:number;
  constructor(name: string,age:number) {
    this.name = name;
    this.age = age;
    Person.count ++;
  }
  static printCount(){
      console.log(this.count) // 静态方法中的this指向类本身
  }
}

const p1 = new Person('zhangsan',18)
const p2 = new Person('zhangsan',18)
Person.printCount()

装饰器
类装饰器
// 当装饰器作为修饰类的时候，会把构造器传递进去
function addNameEat(constructor: Function) {
  constructor.prototype.name = "zhangsan";
  constructor.prototype.eat = function () {
    console.log(`${constructor.prototype.name} is eating`);
  };
}

@addNameEat
class Person {
  name!: string;
  eat!: Function;
  constructor() { }
}

let p: Person = new Person();
console.log(p.name);
p.eat();

// 使用装饰器工厂，可以传递额外参数
function addNameEatFactory(name: string) {
  return function (constructor: Function) {
    constructor.prototype.name = name;
    constructor.prototype.eat = function () {
      console.log(`${constructor.prototype.name} is eating`);
    };
  };
}
@addNameEatFactory("lisi")
class Person {
  name!: string;
  eat!: Function;
  constructor() { }
}
let p: Person = new Person();
console.log(p.name);
p.eat();

属性装饰器
// 属性装饰器：修饰类的属性，传入2个参数，第一个参数对于静态成员来说是类的构造函数，对于实例成员是类的原型对象；第二个参数是属性的名称
//修饰实例属性
function upperCase(target: any, propertyKey: string) {
  let value = target[propertyKey];
  const getter = function () {
    return value;
  };
  // 用来替换的setter
  const setter = function (newVal: string) {
    value = newVal.toUpperCase();
  };
  // 替换属性，先删除原先的属性，再重新定义属性
  if (delete target[propertyKey]) {
    Object.defineProperty(target, propertyKey, {
      get: getter,
      set: setter,
      enumerable: true,
      configurable: true,
    });
  }
}

class Person {
  @upperCase
  name!: string;
}
let p: Person = new Person();
p.name = "world";
console.log(p.name);

方法装饰器
// 装饰类的方法。它接收三个参数：
//   target: Object - 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
//   propertyKey: string | symbol - 方法名
//   descriptor: TypePropertyDescript - 属性描述符

function LogOutput(tarage: any, key: string, descriptor: PropertyDescriptor) {
  // descriptor:  {"writable": true,"enumerable": false", configurable": true }
  // oldMethod: double(num) {return num * 2;}
  let oldMethod = descriptor.value;
  tarage.name = 'hello';
  if(!tarage.loggedOutput) {
    tarage.loggedOutput = new Array<any>();
  }
  descriptor.value = function(...args: any[]): any {
    let result: any = oldMethod.apply(this, args);
    tarage.loggedOutput.push({
      method: key,
      parameters: args,
      output: result,
      timestamp: new Date()
    });
    return result;
  };
}

class Calculator {
  @LogOutput
   double (num: number): number {
    return num * 2;
  }
}

let calc:any = new Calculator(); // 必须设置为any，不然原型上的扩展属性无法访问到
console.log(calc.double(10));
console.log(calc.double(11));
console.log(calc.loggedOutput); 
// 打印结果：
[{
  "method": "double",
  "parameters": [
    10
  ],
  "output": 20,
  "timestamp": "2021-11-30T03:05:20.516Z"
}, {
  "method": "double",
  "parameters": [
    11
  ],
  "output": 22,
  "timestamp": "2021-11-30T03:05:20.523Z"
}] 

// 可以使用装饰器工厂函数，额外传递参数
function LogOutput(params: any) {
  // descriptor:  {"writable": true,"enumerable": false"configurable": true }
  // oldMethod: double(num) {return num * 2;}
  return function (tarage: any, key: string, descriptor: PropertyDescriptor) {
    tarage.name = params;
    if (!tarage.loggedOutput) {
      tarage.loggedOutput = new Array<any>();
    }
    const oldMethod = descriptor.value;
    descriptor.value = function (...args: any[]) {
      let result: any = oldMethod.apply(this, args);
      tarage.loggedOutput.push({
        method: key,
        parameters: args,
        output: result,
        timestamp: new Date()
      });
      return result
    }
  }
}

class Calculator {
  @LogOutput('hello')
   double (num: number): number {
    return num * 2;
  }
}

let calc:any = new Calculator(); // 必须设置为any，不然扩展的属性无法访问到
calc.double(10)
calc.double(11)
console.log(calc.name);  // hello
console.log(calc.loggedOutput); 

ts工具类
Partial/Required
// Partial<T> 将类型的属性变成可选
// keyof T 拿到 T 的所有属性名，然后使用 in 进行遍历，将值赋给 P，最后通过 T[P] 取得相应的属性值的类。中间的 ? 号，用于将所有属性变为可选
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// Required将类型的属性变成必选
// 其中 -? 是代表移除 ? 这个 modifier 的标识。
type Required<T> = { 
    [P in keyof T]-?: T[P] 
};

// 使用
interface UserInfo {
    id: string;
    name: string;
}
type NewUserInfo = Partial<UserInfo>;
const zhangSan: NewUserInfo = {
    name: 'zhangsan'
}

Record
// Record<K extends keyof any, T> 的作用是将 K 中所有的属性的值转化为 T 类型

type Record<K extends keyof any, T> = {
    [P in K]: T;
};

interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const x: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};


// 实际使用
// 相当于 'mac' | 'android' | 'iphone'
enum DeviceType {
  MAC = 'mac',
  ANDROID = 'android',
  IPHONE = 'iphone',
}

const deviceTypeConfig: Record<DeviceDataType, string> = {
  [DeviceDataType.MAC]: 'SuiteAdmin_View_Graph_Mac',
  [DeviceDataType.IPHONE]: 'SuiteAdmin_View_Graph_iPhone',
  [DeviceDataType.ANDROID]: 'SuiteAdmin_View_Graph_Android',
};

const DeviceMap = [{type:'mac',count:30},{type:'android',count:50},{type:'iphone',count:40}]
DeviceMap.map(item => ({
  ...item,
  i18n: deviceTypeConfig[item.type as DeviceType] // DeviceMap的type值只可能是DeviceType中的值
}))

# keyof
```
  // keyof 与 Object.keys 略有相似，只不过 keyof 取 interface 的键
interface Person {
  name: string;
  age: number;
  gender: "male" | "female";
}
//type PersonKey = 'name'|'age'|'gender';
type PersonKey = keyof Person;


function get<T extends object, K extends keyof T>(o: T, name: K): T[K] {
  return o[name]
}
const obj = {
  name:'zhangsan',
  age:12
}
console.log(get(obj,'name'))
  ```

# typeof
```
  // typeof 关键词除了做类型保护 还可以从实现推出类型
interface Hero {
  name: string;
  skill: string;
}

const zed: Hero = { name: "影流之主", skill: "影子" };
type LOL = typeof zed; // type LOL = Hero
const ahri: LOL = { name: "阿狸", skill: "魅惑" };

  ```
tsconfig.js
- files - 设置要编译的文件的名称；
- include - 设置需要进行编译的文件，支持路径模式匹配；
- exclude - 设置无需进行编译的文件，支持路径模式匹配；
- compilerOptions - 设置与编译流程相关的选项。
```
  {
  "compilerOptions": {
  
    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件做为单独的模块 （与 'ts.transpileModule' 类似）.

    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'

    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）

    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。

    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性

    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  }
}
  ```

参考
https://juejin.cn/post/6981728323051192357#heading-13
https://juejin.cn/post/7031787942691471396#heading-6
https://juejin.cn/post/7018805943710253086#heading-31
https://juejin.cn/post/6844904182843965453#heading-37

