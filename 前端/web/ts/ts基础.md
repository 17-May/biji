# ts基础

# 基本类型

- number：`变量:number` 2^53-1
- string：`变量:string`
- boolean：`变量:boolean`
- bigint：`变量:bigint` es2020以上
- symbol：`变量：symbol`
- 数组
  - `变量:类型[]` 建议使用
  - `变量:Array<number>`
- object：`变量:object`
- null和undefined
  - 是所有类型的子类型，可以赋值给其他类型
    - let num:number = null
  - `"strictNullChecks": true` 配置更加严格的类型检查，只能赋值给自身
    - let num:null = null

### 其他类型

- 联合类型：`类型1 | 类型`
  - 控制流分析：判断类型一次，后面的类型就少一个
- 交叉类型：`类型1 & 类型`
- void：通常用于约束函数的返回值，表示无返回
- never：通常用于约束函数的返回值，表示该函数永远不会结束。一般在抛出错误的函数/死循环函数使用
- 字面量类型：`let a:'A';  let gender: "男" | "女";`
- 元组（Tuple）：一个固定长度的数组，并且数组中每一项的类型确定
  - `变量:[类型1,类型2]`=`[值1 , 值2]`
- any：任意类型，绕过类型检查。可以赋值给其他类型
- unKnown：任意类型，不可以赋值给其他类型，也不能进行不同类型运算
  - `let c: unknown = 0; let d: number = c; // Error: Type 'unknown' is not assignable to type 'number'.`

### 类型断言

类型检测,只会编译阶段起作用

- `值 as 类型` 不建议使用
- `<类型>值` 推荐使用

```ts
let someValue: string = "Hello World";
let someValue2: number = (someValue as string).length;
let someValue3: number = (<string>someValue).length;
```

### satisfies

类型检测，比类型断言更安全

```ts
interface ITest {
  test: string;
}
let test = {} as ITest; //不报错
let test = {} satisfies ITest;//报错
```

### 协变和逆变

- 协变：子类型可以赋值给父类型
- 逆变：父类型可以赋值给子类型
  User是UserS的父类型，UserS是User的子类型。

```ts
type User = {
  name: string;
  age: number;
};
type UserS = {
  name: string;
  age: number;
  sex: "男" | "女";
};
```

### 多余属性检查

- 字面量赋值会触发属性检查
- 变量赋值不会触发，所以属性符合即可

```ts
type User = {
  name: string;
  age: number;
};
type UserS = {
  name: string;
  age: number;
  sex: "男" | "女";
};
const a1: User = {
  name: "1",
  age: 1,
  sex:1,//报错
};
const a2: UserS = {
  name: "1",
  age: 1,
  sex: "男",
};
const a3: User = a2;
```

### readonly 修饰符

- 只读 `readonly 变量:类型`

# 函数相关约束

- 函数重载：在函数实现前，对函数调用的多种情况进行声明

```ts
function add(a: number, b: number) : number;
function add(a: string, b: string) : string;
function add(a: number | string, b: number | string): number | string {
  if (typeof a === "number" && typeof b === "number") {
    return a * b;
  } else if (typeof a === "string" && typeof b === "string") {
    return a + b;
  }
  throw new Error("a和b必须是相同的类型");
}
```

- 可选参数：可以在某些参数名后加上问好，表示该参数可以不用传递。可选参数必须在参数列表的末尾

```ts
function add(a: number, b?: number, c?: number): number {
  return a + (b || 0) + (c || 0);
}
```

# 拆箱和装箱

- 装箱：基本类型转换引用类型
- 拆箱：引用类型转换基本类型

# 字面量类型的问题

- 在类型约束位置，会产生重复代码。可以使用类型别名解决
- 逻辑含义和真实的值产生了混淆，会导致当修改真实值的时候，产生大量的修改
- 字面量类型不会进入编译结果

# 扩展类型-类型别名

- `type 名称（最好首字母大写） = 类型`
- 类继承写法(交叉类型)：`type x = {} & x & x`

# 扩展类型-枚举

- 通常用于约束每个变量的取值范围
- `enum 枚举名{枚举字段=值}`

##### 规则

- 枚举的字段值可以是字符串或者数字
- 数字枚举的值会自动自增
- 被数字枚举约束的变量，可以直接赋值为数字
- 数字枚举的编译结果和字符串枚举有差异

##### 最佳实践

- 尽量不要在一个枚举中既出现字符串字段，又出现数字字段
- 使用枚举时，尽量使用枚举字段的名称，而不适应真实的值

##### 枚举位运算

- 或运算（|）：有1取1，无1取0
  - `0011 | 0001 = 0011`
- 且运算（&）: 相同取1，不同取0
  - `0011 & 0001 = 0001`
- 异或（^）：不同取1，相同取0
  - `0011 ^ 0010 = 0001`

```ts
enum Permission {
  Read = 1, // 0001
  Write = 2, // 0010
  Execute = 4, // 0100
  Delete = 8, // 1000
}
//组合权限
// 0001 | 0010 = 0011
let p: Permission = Permission.Read | Permission.Write;
//判断是否有某个权限
// 0011 & 0001 = 0001
function hasPermission(target: Permission, per: Permission) {
  return (target & per) === per;
}
console.log(hasPermission(p, Permission.Read)); //true
//删除某个权限
// 0011 ^ 0010 = 0001
p = p ^ Permission.Write; //0011 ^ 0010 = 0001
console.log(p)
```

# 类型声明文件（x.d.ts）

用来做类型声明，没有逻辑代码
`"declaration": true //生成相应的.d.ts文件`

# 类型兼容性

B->A，如果能完成赋值，则B和A类型兼容

鸭子辩型法（子结构辩型法）：目标类型需要某一些特征，赋值的类型只要满足该特征即可

- 基本类型：完全匹配
- 对象类型：鸭子辩型法
  - 当直接使用对象字面量赋值的时候，会进行更加严格的类型判断
- 函数类型
  - 参数：传递给目标函数的参数可以少，不可以多
  - 返回值：要求返回必须返回且类型要匹配。不要求返回，随意。

# as const修饰符

将数组和对象改成只读属性，readonly

# 清除文件命令

- package.json：`build:rd /s（删除） /q（确认） dist（指定文件夹） &（执行前面再执行后面） tsc`

# 泛型

为什么需要：有时，书写某个函数时，会丢失一些类型信息（多个位置的类型应该保持一致或者又关联的信息）
含义：是指附属于函数、类、接口、类型别名之上的类型。

- 泛型相当于一个类型变量，在定义时，无法预先知道具体的类型，可以用该变量来代替，只有在调用时，才确定它的类型
- 很多时候，TS会智能的根据传递的参数，推导出泛型的具体类型
- `<泛型名称（一般使用T代替）>`
- 默认值设置：`<T = xx>`

```TS
function take<T>(arr: T[], n: number): T[] {
  if (arr.length <= n) {
    return arr;
  }
  let newArr: T[] = [];
  for (let i = 0; i < n; i++) {
    newArr.push(arr[i]);
  }
  return newArr;
}
let newArr = take<string>(['1, 23, 4, 4, 43','12','123'], 2);
```

### 关联泛型

```ts
type User = {
  name: string;
  age: number;
  tel: string;
};
//需要从User中选取name和tel两个属性
type MyPick<T, K extends keyof T> = {
  [key in K]: T[key];
};
const user: MyPick<User, "name" | "tel"> = {
  name: "Alice",
  tel: "1234567890",
};

```
