---
title: 初识Typescript
date: 2021-01-20 14:57:37
tags:
---
## 前言
这是一篇学习Ts的学习笔记，是片段式的，弱逻辑连接的草稿内容，如果你想通过这篇blog学习Ts的知识，那应该是走错地方了。

## 过程
#### Typescript 究竟是什么？
+ 动态类型语言（Dynamically Typed Language）:在运行时做变量类型检查的语言，编写代码时不用进行类型设置，Js python ruby
+ 静态类型语言（Statically Typed Language）:在编译时做类型检查，编写代码时需要设置变量的类型，C C++ java

Typescript: 将不关注变量类型的 动态类型语言，变成关注类型的静态类型语言。
Javascript that scales(JS的超集合)，给JS提供静态类型风格的类型系统，从es6到es10甚至esnext的语法支持。

#### Ts 优势
+ 方法或者模块增加输入输出的类型，外部条件，来增加代码可读性，减少代码bug, 省去一些原本需要手动调试的问题(动态语言约束)
+ 包容性： 完全兼容 Js ，第三方库不是必须使用TS编写也可以使用，可以单独编写定义的类型文件来读取
+ 流行库都支持TS，Typescript-React Vue Angular AntD等

#### 安装和初始 Typescript
`npm install -g typescript` 全局安装 TS, `tsc -v` 查看ts版本, (tsc: Tpye Script Compiler)
新建hellow.ts文件，内容如下：
``````
const hello = (name: string) => {
    return `hello ${name}`
};

hello('wild Lei') 
``````
tsc hellow.ts 可以得到编译后的Js文件，因为规定传入的变量的类型为`string`, 倘若`hello`函数执行的时候，传入是个number，代码编辑器(Vscode)会报错，正常情况下编译也会报错(和配置有关)

#### 基础类型
``````
// 布尔
let isDone: boolean = false;

// number
let age: number = 20;

// string, 模板字符串
let firstName: string = 'lei';
let message: string = `Hellow, ${firstName}, age is ${age}`;

// undefined, null
let u: undefined = undefined;
let n: null = null;

// undefined, null是其他类型的子类型
let num: number = undefined;
``````

#### any类型和联合类型
当面对用户输入，或者通过API输入的数据无法确定类型时，就引入了any类型
``````
let notSure: any = 4;

// 赋值成其他类型
notSure = 'maybe it is a string';
notSure = true;
// 即便是调用方法和属性也可以
notSure.myName
notSure.getName()
``````

虽然宽泛了约束条件，但是一定程度上失去了Ts的功能，当不想如此宽泛且又不针对一种类型时，就可以使用联合类型
``````
let numberOrString: number | string = 234;
numberOrString = '321';
``````

#### Array 和 Tuple
定义一个只包含数字的数组
``````
let arrOfNumbers: number[] = [1, 2, 3, 4]
``````
如何在这个数组中加入string类型，或者其他类型的变量，编辑器会飘红
Array的push方法也会做相应的类型检查
``````
arrOfNumbers.push(5);  // 正常
arrOfNumbers.push('5'); // 飘红
``````

类数组: 例如函数里面的内置变量 arguments, 如果用数组类型来接收， 会报错，需要使用其他特定的类型(HtmlCollection, NodeList等)，后续跟进
``````
function test() {
    console.log(arguments); // arguments 是 类数组
    /* 类数组 具备一定数组的属性，但是很多方法也没有 */
    /* 例如length方法可以使用， 下标查询可以使用， 而 forEach 和 map 方法不可使用*/
    
    let arr: any[] = arguments; // 会报错
}
``````

当数组内元素类型不确定时，会使用any，而当有一定约束的时候，例如我希望第一项是 number, 第二项是 string，目前的知识就很难办到, 需要引入 Tuple 元组
``````
let user: [string, number] = ['viking', 1];
``````

#### interface 
变量、数组之后，剩下了对象，interface可以用来约束对象的属性，对类(class)进行抽象
+ todo: Duck Typing(鸭子类型) 和 interface 有何种关系呢？

``````
interface Person {
    name: string; // 注意分割用分号
    age: number;
}

let lei: Person = {
    name: 'lei',
    age: 20
} // 无论多余或者缺少的属性，都会引起报错

``````
如果一个属性是可选项，可以在定义interface时，在该属性名后跟 `?`
``````
interface Person {
    name: string;
    age?: number;
}

let lei: Person = {
    name: 'lei',
}
``````
也可以设置只读属性
interface Person {
    readonly id: number;
    name: string;
    age: number;
}

let lei: Person = {
    id: 123,
    name: 'lei',
    age: 20
}
lei.id = 321 // 报错
除此之外 interface 也可以用来描述类，函数，数组等

#### 函数和类型推断
+ 函数声明
``````
// x, y都为number 返回也为number
function add(x: number, y: number): number {
    return x + y;
}
``````

+ 参数可选
``````
// z为可选
function add(x: number, y: number, z?: number): number {
     if (typeof z === 'number') {
        return x + y + z;
    }
    return x + y;
}

let result = add(2, 3);
``````

+ 函数表达式, 以及赋值
``````
const add = function (x: number, y: number, z?: number): number {
    if (typeof z === 'number') {
        return x + y + z;
    }
    return x + y;
}
// 此时的 add 是函数类型, 且 add2 和 add 的函数输入输出类型一致
const add2: (x: number, y: number, z?:number) => number = add
``````

+ 上面的 `add` 是没有定义类型的，而 `TS` 已经做了类型的推断，举一个 `string` 的例子
``````
let str = `str`;
str = 123 // 报错 ts2322
``````

#### Class
``````
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name
    }

    run() {
        return `${this.name} is running`
    }
}

const snake = new Animal('lily')
console.log(snake.run())
``````

+ 继承
``````
class Dog extends Animal {
    bark() {
        return `${this.name} `
    }
}
const xiaobao = new Dog('xiaobao'); // 继承了 name 属性
console.log(xiaobao.run()) // 继承了 run 方法
console.log(xiaobao.bark()) // 自建的 bark 方法
``````

+ 重写
``````
class Cat extends Animal {
    // constructor(name) {
    //     super(name)
    //     console.log(this.name)
    // }
    run() {
       return 'Meow, ' + super.run() 
    } 
}

const maomao = new Cat('maomao')
console.log(maomao.run());
``````

#### Class 属性和方法的约束
+ Ts 提供三种访问修饰符 `public` `private` 
+ 所有属性和方法默认都是 `public`
``````
class Animal {
    public name: string; // 显示的 标明 public
    constructor(name: string) {
        this.name = name
    }
    run() {
        return `${this.name} is running`
    }
}
const snake = new Animal('lili);
snake.name = 'lucy'; // 可写
console.log(snake.name); // lucy 可访问
``````
+ 不想对外的属性可以用 `private` 修饰, 即便是子类也不可以访问
+ 如果想找一种介于 `public` ，`private` 之间的一种约束，可以使用 `protected`修饰符
用 `protected`修饰符 修饰的属性，子类是可以访问的，而外部无法访问(父类，以及子类的实例)

+ 还有一种是只读属性，`readonly` 修饰符修饰

目前为止讨论的是`实例`的方法和属性，对于类本身可以有可访问的属性和方法， 这时候需要使用`静态属性`和`静态方法`(`static` 修饰符)

``````
class Animal {
    protected name: string;
    static categoies: string[] = ['mammal', 'bird']; // 静态属性
    static isAnimal(a) {
        // 静态方法， 判断一个实例是否是 Animal 的子类
        return a instanceof Animal
    }
    constructor(name: string) {
        this.name = name
    }
    run() {
        return `${this.name} is running`
    }
}
console.log(Animal.categoies); // ['mammal', 'bird']
console.log(Animal.isAnimal( new Animal(luby) )); // true
``````

##### 类和接口
##### 枚举
##### 泛型
##### 类型别名和类型断言
##### 声明文件




















