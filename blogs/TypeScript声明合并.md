# ts中几种高级的声明合并用法

### 如何在不同的ts文件中对模块声明进行扩展

使用场景:

1. 通过prototype对类进行扩展，需要添加声明
2. 运行时直接对对象赋值进行扩展，需要提前添加声明

```ts
    // module_a.ts

    export default class ModuleA {
        // 实例方法
        public instanceFuncA() {}

        // 静态方法
        public static StaticFuncA() {}
    }

    // ////////////////
    // module_a_ext.ts

    // 注意这里的模块路径
    declare module "./module_a" {
        // 通过interface来扩展实例方法
        interface ModuleA {
            instanceFuncB(): void;
        }

        // 通过namespace来扩展静态方法
        namespace ModuleA {
            StaticFuncB(): void;
        }
    }

    // ///////////////
    // logic.ts

    let inst = new ModuleA();
    // 扩展的实例方法
    inst.instanceFuncB();
    // 扩展的静态方法
    ModuleA.StaticFuncB();
```

### namespace与class合并可以实现内部类

如下：

```ts
// module.ts
export class Module {
    a: number;
}

export namespace Module {
    export class InnerClass {
        b: string;
    }
}

// //////////////////
// logic.ts

let inner = new Module.InnerClass();
console.log(inner.b);
```
