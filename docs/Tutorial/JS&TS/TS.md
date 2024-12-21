# <center>TypeScript</center>

## Interface 接口
TypeScript 的接口是一系列抽象方法的声明，是一些方法特征的集合。

下面是接口的定义
```ts
interface interface_name{
    // 方法名 + 类型
}
```

```ts
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 
 
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 

console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi()) 


// 接口与数组
interface namelist{
    [index:number] : string
}

var list2:namelist = ["Google","Runoob","Taobao"];

```

接口也可以继承
```ts
interface Person{
    age:number
}

interface Musician extends Person{
    instrumemt:string
}

```


<style>
img{
    display : block;
    margin-left : auto;
    margin-right : auto;
    width : 85%;
    border-radius : 15px;
}
</style>