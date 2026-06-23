---
title: 'TypeScript 学习笔记'
description: '记录学习 TypeScript 的过程中遇到的问题和解决方法。'
pubDate: 2025-04-20
tags: ['TypeScript']
---

## 基础知识

### 原始数据类型

1. TS 中使用定义变量类型以下这种方式定义
   ```ts
   let u: undefined = undefined
   let n: null = null
   ```
2. `undefined`  和  `null`与  `void`  的区别是，`undefined`  和  `null`  是所有类型的子类型。也就是说  `undefined`  类型的变量，可以赋值给  `number`  类型的变量，而  `void`  类型的变量不能赋值给  `number`  类型的变量。
3. 可以用  `void`  表示没有任何返回值的函数。
4. 任意值（any）用来表示允许赋值为任意类型，在任意值上访问任何属性都是允许的，也允许调用任何方法，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。
5. **变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**
6. 如果没有明确的指定类型，那么 TypeScript 会依照初始值推断出一个类型，**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成  `any`  类型而完全不被类型检查**。
7. 允许多个类型的话用`|` 来分隔每一个类型，如这里的  `let myFavoriteNumber: string | number`  的含义是，允许  `myFavoriteNumber`  的类型是  `string`  或者  `number`，但是不能是其他类型。
8. 当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：
   ```ts
   function getLength(something: string | number): number {
     return something.length
   }
   ```
   上例中，`length`  不是  `string`  和  `number`  的共有属性，所以会报错。访问  `string`  和  `number`  的共有属性是没问题的。
9. 联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型，赋值之后的操作只能访问推断类型已有的属性。

### 对象的类型

1. **赋值的时候，变量的形状必须和接口的形状保持一致**。
2. `?:`代表是可选属性，可选属性的含义是该属性可以不存在。这时**仍然不允许添加未定义的属性**。
3. 使用`[propName: string]: any;` 可以定义一个任意属性，如此例定义了一个任意属性取 `string` 的值。
4. 需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集**。
5. 一个接口中只能定义一个任意属性。如果接口中有多个类型的属性，则可以在任意属性中使用联合类型。
6. 使用 `readonly id: number;` 可以设置某个属性为只读属性，**注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**。即第一个修改的时候，而不是初始赋值的时候。

### 数组的类型

1. 定义方式最简单的是「类型 + 方括号」，也可以用数组泛型 `Array<类型>` 表示。即 `numnber[]` 未定义一个完全由数字组成的数组，不允许出现其他的类型。
2. 也可用用接口 `interface` 来表示数组，可以控制数组的每一项的类型。常用来表示类数组。
3. 类数组不能用普通的数组方式来表示，而应该用接口。常用的类数组都有自己的接口定义，如  `IArguments`, `NodeList`, `HTMLCollection`  等。
4. 用  `any`  表示数组中允许出现任意类型。

### 函数的类型

1. 一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到，其中函数声明的类型定义较简单：
   ```ts
   function sum(x: number, y: number): number {
     return x + y
   }
   ```
   注意多余或者少于要求的参数都是不被允许的。
2. 在 TypeScript 的类型定义中，=>  用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
3. 同样适用`?` 来定义可选参数，**可选参数后面不允许再出现必需参数了**。
4. **TypeScript 会将添加了默认值的参数识别为可选参数**，设置了默认值之后就不受「可选参数必须接在必需参数后面」的限制了。
5. 可以使用  `...rest`  的方式获取函数中的剩余参数（rest 参数），rest 参数是一个数组，可以使用数组定义来定义他。
6. 注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

### 类型断言

1. 类型断言是将一个联合类型断言为其中一个类型。
2. 语法为 `值 as 类型` 或者 `<类型>值`。
3. 类型断言只能解决解析时的错误，无法避免运行时的错误。请勿滥用。
4. 使用类型断言时一定要格外小心，尽量避免断言后调用方法或引用深层属性，以减少不必要的运行时错误。
5. TS 里直接在 window 上赋值的话会报错，需要先将 window 断言给 any，即 `window as any` 。
6. 类型断言主要有以下几个特点：
   - 联合类型可以被断言为其中一个类型
   - 父类可以被断言为子类
   - 任何类型都可以被断言为 any
   - any 可以被断言为任何类型
   - 要使得  `A`  能够被断言为  `B`，只需要  `A`  兼容  `B`  或  `B`  兼容  `A`  即可
7. 类型断言只会影响 TypeScript 编译时的类型，类型断言语句在编译结果中会被删除。

### 声明文件

1. 声明文件必需以  `.d.ts`  为后缀。
2. 新语法索引如下：
   - [`declare var`](https://ts.xcatliu.com/basics/declaration-files.html#declare-var)  声明全局变量
   - [`declare function`](https://ts.xcatliu.com/basics/declaration-files.html#declare-function)  声明全局方法
   - [`declare class`](https://ts.xcatliu.com/basics/declaration-files.html#declare-class)  声明全局类
   - [`declare enum`](https://ts.xcatliu.com/basics/declaration-files.html#declare-enum)  声明全局枚举类型
   - [`declare namespace`](https://ts.xcatliu.com/basics/declaration-files.html#declare-namespace)  声明（含有子属性的）全局对象
   - [`interface`  和  `type`](https://ts.xcatliu.com/basics/declaration-files.html#interface-%E5%92%8C-type)  声明全局类型
   - [`export`](https://ts.xcatliu.com/basics/declaration-files.html#export)  导出变量
   - [`export namespace`](https://ts.xcatliu.com/basics/declaration-files.html#export-namespace)  导出（含有子属性的）对象
   - [`export default`](https://ts.xcatliu.com/basics/declaration-files.html#export-default) ES6 默认导出
   - [`export =`](https://ts.xcatliu.com/basics/declaration-files.html#export-1) commonjs 导出模块
   - [`export as namespace`](https://ts.xcatliu.com/basics/declaration-files.html#export-as-namespace) UMD 库声明全局变量
   - [`declare global`](https://ts.xcatliu.com/basics/declaration-files.html#declare-global)  扩展全局变量
   - [`declare module`](https://ts.xcatliu.com/basics/declaration-files.html#declare-module)  扩展模块
   - [`/// <reference />`](https://ts.xcatliu.com/basics/declaration-files.html#san-xie-xian-zhi-ling)  三斜线指令
   ```ts
   declare var jQuery: (selector: string) => any
   ```
   如上述列子即定义了一个全局变量 JQuery 的类型，仅仅用于编译时的检查。编译结果中会被删除。
3. 声明语句中只能定义类型，不要在声明语句中写具体的实现与值。
4. 暴露在最外层的  `interface`  或  `type`  会作为全局类型作用于整个项目中，我们应该最好将他们放到  `namespace`  下。
5. 声明 `npm` 包时建议 创建一个  `types`  目录，专门用来管理自己写的声明文件，将  `foo`  的声明文件放到  `types/foo/index.d.ts`  中。这种方式需要配置下  `tsconfig.json`  中的  `paths`  和  `baseUrl`  字段。
   ```json
   {
     "compilerOptions": {
       "module": "commonjs",
       "baseUrl": "./",
       "paths": {
         "*": ["types/*"]
       }
     }
   }
   ```
6. 注意，与全局变量的声明文件类似，`interface`  前是不需要  `declare`  的。
7. 注意，只有  `function`、`class`  和  `interface`  可以直接默认导出，其他的变量需要先定义出来，再默认导出。
8. 针对这种先定义的默认导出，我们一般会将导出语句放在整个声明文件的最前面。
9. 针对 commonjs 规范导出的模块，在 ts 中一般有以下几种方式导入：
   1. 第一种方式是  `const ... = require` 即：
      ```js
      // 整体导入
      const foo = require('foo')
      // 单个导入
      const bar = require('foo').bar
      ```
   2. 第二种方式是  `import ... from`，注意针对整体导出，需要使用  `import * as`  来导入：
      ```ts
      // 整体导入
      import * as foo from 'foo'
      // 单个导入
      import { bar } from 'foo'
      ```
   3. 第三种方式是  `import ... require`，这也是 ts 官方推荐的方式：
      ```ts
      // 整体导入
      import foo = require('foo');
      // 单个导入
      import bar = require('foo').bar;
      ```
   4. 对于这种使用 commonjs 规范的库，假如要为它写类型声明文件的话，就需要使用到  `export =`  这种语法了：

      ```ts
      export = foo

      declare function foo(): string
      declare namespace foo {
        const bar: number
      }
      ```

   5. 需要注意的是，上例中使用了  `export =`  之后，就不能再单个导出  `export { bar }`  了。所以我们通过声明合并，使用  `declare namespace foo`  来将  `bar`  合并到  `foo`  里。

## 进阶知识

### 类型别名

```ts
type Name = string
type NameResolver = () => string
type NameOrResolver = Name | NameResolver
```

1. 上例中我们使用 `type` 来定义类型别名，后续的使用中，`Name` 变量跟 `string` 类型是相同的。

### 字符串字面量类型

1. 字符串字面量类型用来约束取值只能是某几个字符串中的一个。

```ts
type EventNames = 'click' | 'scroll' | 'mousemove'
```

注意，**类型别名与字符串字面量类型都是使用  `type`  进行定义。**

### 元组

1. 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

```ts
let tom: [string, number] = ['Tom', 25]
```

2. 直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。
3. 当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型。如上面例子只能在往数组里添加 `string` 或者 `number` 类型的数据。
4. 因此使用元祖可以确定元素数据类型，但不要超出范围，可以把元祖理解为固定长度，超出范围不能保证其类型。

### 枚举

1. 枚举（Enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。
2. 枚举使用  `enum`  关键字来定义：
   ```ts
   enum Days {
     Sun,
     Mon,
     Tue,
     Wed,
     Thu,
     Fri,
     Sat
   }
   ```
   枚举成员会被赋值为从  `0`  开始递增的数字，同时也会对枚举值到枚举名进行反向映射： 如上例中 Day[‘sun’] === 0 和 Day[0] === 'sun' 结果都是 true。
3. 我们也可以给枚举项手动赋值：

   ```ts
   enum Days {
     Sun = 7,
     Mon = 1,
     Tue,
     Wed,
     Thu,
     Fri,
     Sat
   }

   console.log(Days['Sun'] === 7) // true
   console.log(Days['Mon'] === 1) // true
   console.log(Days['Tue'] === 2) // true
   console.log(Days['Sat'] === 6) // true
   ```

   上面的例子中，未手动赋值的枚举项会接着上一个枚举项递增。如果未手动赋值的枚举项与手动赋值的重复了，TS 不会报错。

   ```ts
   enum Days {
     Sun = 3,
     Mon = 1,
     Tue,
     Wed,
     Thu,
     Fri,
     Sat
   }
   console.log(Days['Sun'] === 3) // true
   console.log(Days['Wed'] === 3) // true
   console.log(Days[3] === 'Sun') // false
   console.log(Days[3] === 'Wed') // true
   ```

   所以使用的时候需要注意，最好不要出现这种覆盖的情况。

4. 手动赋值的枚举项可以不是数字，此时需要使用类型断言来让 tsc 无视类型检查。

```ts
enum Days {
  Sun = 7,
  Mon,
  Tue,
  Wed,
  Thu,
  Fri,
  Sat = <any>'S'
}
```

5. 手动赋值的枚举项也可以为小数或负数，此时后续未手动赋值的项的递增步长仍为  `1`

#### 常数项和计算所得项

1. 枚举有两种类型：常数项、计算所得项。前面的例子都是常数项。
   ```ts
   enum Color {
     Red,
     Green,
     Blue = 'blue'.length
   }
   ```
   上面的例子就 `"bule".length` 就是一个计算所得项。上面的例子不会报错，但是**如果紧接在计算所得项后面的是未手动赋值的项，那么它就会因为无法获得初始值
   而报错**
2. 当满足以下条件时，枚举成员被当作是常数：
   - 不具有初始化函数并且之前的枚举成员是常数。在这种情况下，当前枚举成员的值为上一个枚举成员的值加  `1`。但第一个枚举元素是个例外。如果它没有初始化方法，那么它的初始值为  `0`。
   - 枚举成员使用常数枚举表达式初始化。常数枚举表达式是 TypeScript 表达式的子集，它可以在编译阶段求值。当一个表达式满足下面条件之一时，它就是一个常数枚举表达式：
     - 数字字面量
     - 引用之前定义的常数枚举成员（可以是在不同的枚举类型中定义的）如果这个成员是在同一个枚举类型中定义的，可以使用非限定名来引用
     - 带括号的常数枚举表达式
     - `+`, `-`, `~`  一元运算符应用于常数枚举表达式
     - `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^`  二元运算符，常数枚举表达式做为其一个操作对象。若常数枚举表达式求值后为 NaN 或 Infinity，则会在编译阶段报错
3. 所有其它情况的枚举成员被当作是需要计算得出的值。

#### 常数枚举

1. 常数枚举是使用 `const enum` 定义的枚举类型，常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。

#### 外部枚举

1. 外部枚举是使用 `declare enum` 定义的枚举类型。只会在编译时进行检查，编译结果中会删除。

### 类

#### 类的概念

- 类（Class）：定义了一件事物的抽象特点，包含它的属性和方法
- 对象（Object）：类的实例，通过  `new`  生成
- 面向对象（OOP）的三大特性：封装、继承、多态
  - 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
  - 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
  - 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如  `Cat`  和  `Dog`  都继承自  `Animal`，但是分别实现了自己的  `eat`  方法。此时针对某一个实例，我们无需了解它是  `Cat`  还是  `Dog`，就可以直接调用  `eat`  方法，程序会自动判断出来应该如何执行  `eat`
- 存取器（getter & setter）：用以改变属性的读取和赋值行为
- 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如  `public`  表示公有属性或方法
- 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。**一个类只能继承自另一个类，但是可以实现多个接口**。

#### 属性和方法

1. 使用  `class`  定义类，使用  `constructor`  定义构造函数。通过  `new`  生成新实例的时候，会自动调用构造函数。
2. 使用  `extends`  关键字实现继承，子类中使用  `super`  关键字来调用父类的构造函数和方法。即 `class 子 extends 父`
3. 使用  `static`  修饰符修饰的方法称为静态方法，它们不需要实例化，而是直接通过类来调用。
4. ES6 中实例的属性只能通过构造函数中的  `this.xxx`  来定义，ES7 提案中可以直接在类里面定义，可以使用  `static`  定义一个静态属性。

#### TypeScript 中类的用法

1. TypeScript 可以使用三种访问修饰符，分别是  `public`、`private`  和  `protected`。
   - `public`  修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是  `public`  的。
   - `private`  修饰的属性或方法是私有的，不能在声明它的类的外部访问，子类也不能访问。
   - `protected`  修饰的属性或方法是受保护的，它和  `private`  类似，区别是它在子类中也是允许被访问的。
2. `readonly` 只读属性关键字，只允许出现在属性声明或索引签名或构造函数中。
3. **注意如果  `readonly`  和其他访问修饰符同时存在的话，需要写在其后面。**

#### 抽象类

1. `abstract`  用于定义抽象类和其中的抽象方法。
2. 抽象类不允许被实例化。
3. 抽象类中的抽象方法必须被子类实现。

#### 类实现接口

1. _实现_ 是面向对象编程的一个重要概念，一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口。用 `implements` 关键字来实现。

```ts
class 子类 extends 父类 implements 接口实现
```

2. 一个类可以实现多个接口。
3. 接口与接口之间可以是继承关系。

```ts
interface 子接口 extends 父接口
```

4. 常见的面向对象语言中，接口是不能继承类的，但是在 TypeScript 中却是可以的
   ```ts
   interface 接口 extends 类
   ```
   实际上，当我们在声明  `class Point`  时，除了会创建一个名为  `Point`  的类之外，同时也创建了一个名为  `Point`  的类型（实例的类型）。所以我们既可以将  `Point`  当做一个类来用（使用  `new Point`  创建它的实例），也可以将  `Point`  当做一个类型来用（使用  `: Point`  表示参数的类型）
5. 声明类时创建的类型是不包含构造函数和静态属性好静态方法的。只包含其中的实例方法和实例属性。

### 泛型

> 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

1. 泛型通常使用 `<T>` T 可以为任意字母来表示（通常建议单个大写字母）
2. 在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以同样不能随意的操作它的属性或方法。
3. 我们可以使用接口对泛型进行约束，`<T extends 接口（interface）>` 来限制 T 必须符合 interface 的形状。
4. 多个类型参数之间也可以进行约束，如 `<T extends U, U>` 要求  `T`  继承  `U`，这样就保证了  `U`  上不会出现  `T`  中不存在的字段。
5. 型中的类型参数指定默认类型。`<T = string>` 当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。

### 声明合并

1. 如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型
2. **合并的属性的类型必须是唯一的**。
3. 函数的合并，其实就是用重载定义多个函数类型。
4. 接口中如果要合并的属性值重复了，但类型一样的话就不会报错，类型不一样会报错。
5. 接口中方法的合并与函数合并的一样。方法本质就是一个个的函数。
