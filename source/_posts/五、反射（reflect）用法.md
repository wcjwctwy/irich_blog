---
title: 五、反射（reflect）用法
date: 2018-11-30 19:47:03
tags: [Golang]
categories: [语言基础]
---

### 基本用法及API
- func ValueOf
```
func ValueOf(i interface{}) Value
```
ValueOf返回一个初始化为i接口保管的具体值的Value，ValueOf(nil)返回Value零值。  

-----
- func TypeOf

```
func TypeOf(i interface{}) Type
```
TypeOf返回接口中保存的值的类型，TypeOf(nil)会返回nil。  

-----

- func (Value) Type

```
func (v Value) Type() Type
```
返回v持有的值的类型的Type表示。   
==注意：== 其中Value可以获取到Type ,value操作的是指定的数据值其中包含数据的类型和具体的值，Type操作的是数据的类型结构，如：属性。。

---  
- func (Value) Elem  

```
func (v Value) Elem() Value
```
Elem返回v持有的接口保管的值的Value封装，或者v持有的指针指向的值的Value封装。如果v的Kind不是Interface或Ptr会panic；如果v持有的值为nil，会返回Value零值。  
==注意：== 这个是Value的Elem方法，通常用这个方法获取数值类型的指针指向的数值

---  
- func (Type) Field

```
func (Type) Field(i int) StructField
```
// 返回索引序列指定的嵌套字段的类型，  
// 等价于用索引中每个值链式调用本方法，如非结构体将会panic

```
type StructField struct {
    // Name是字段的名字。PkgPath是非导出字段的包路径，对导出字段该字段为""。
    // 参见http://golang.org/ref/spec#Uniqueness_of_identifiers
    Name    string
    PkgPath string
    Type      Type      // 字段的类型
    Tag       StructTag // 字段的标签
    Offset    uintptr   // 字段在结构体中的字节偏移量
    Index     []int     // 用于Type.FieldByIndex时的索引切片
    Anonymous bool      // 是否匿名字段
}
```


### 数值类型反射使用
只能通过指针才能更改反射后的值，否则传值是复制
#### 基本类型的值修改  
代码
```
package main

import (
	"fmt"
	"reflect"
)

func testBasic(i interface{}){
	//i实际是一个指针
	ptrValueOfI := reflect.ValueOf(i)
	fmt.Println(ptrValueOfI.Kind())
	//获取指针指向的真正的值
	valueOfI := ptrValueOfI.Elem()
	//为其更改值
	valueOfI.SetInt(1000)
}

func main() {
	n:=1
	testBasic(&n)
	fmt.Println(n)

}
```
结果：  

```
ptr
1000
```
#### struct类型的属性修改  

代码：
```
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name string
	Age int
}

func test(i interface{}){
	//获取指针指向的真正的数值Value
	valueOfI := reflect.ValueOf(i).Elem()
	//获取对应的Type这个是用来获取属性方法的
	typeOfI := valueOfI.Type()
	//判断是否是struct
	if typeOfI.Kind()!=reflect.Struct{
		fmt.Println("except struct")
		return
	}
	//获取属性的数量
	numField := typeOfI.NumField()
	//遍历属性，找到特定的属性进行操作
	for i:=0;i< numField;i++{
		//获得属性的StructField，次方法不同于Value中的Filed（这个返回的是Field）
		field := typeOfI.Field(i)
		//获取属性名称
		fieldName := field.Name
		fmt.Println(fieldName)
		//找到名为Name的属性进行修改值
		if fieldName=="Name"{
			//改变他的值为jack
			valueOfI.Field(i).SetString("jack")
		}
	}
}

func main() {
	stu:=Student{Name:"susan",Age:58}
	test(&stu)
	fmt.Println(stu.Name)
}
```
结果：

```
Name
Age
jack
```

### 引用类型修改
##### slice
代码：

```
package main

import (
	"fmt"
	"reflect"
)

func testSilce(i interface{}){
	//获取对应的Value
	valueOfI := reflect.ValueOf(i)
	//获取Kind
	fmt.Println("kind = ",valueOfI.Kind())
	//获取slice长度
	fmt.Println("len = ",valueOfI.Len())
	//获取slice容量
	fmt.Println("cap = ",valueOfI.Cap())
	//获取指定位置的元素
	indexValue := valueOfI.Index(0)
	fmt.Println("index 0 = ",indexValue)
	//为指定位置的元素赋值
	indexValue.SetInt(300)
}

func main() {
	s:=make([]int,10,20)
	s[0]=200
	//无需传入指针  因为其本身就是引用类型（slice map channel）
	testSilce(s)
	fmt.Println(s[0])

}
```
结果：

```
kind =  slice
len =  10
cap =  20
index 0 =  200
300

```
##### map
代码：

```
package main

import (
	"fmt"
	"reflect"
)

func testMap(i interface{}){
	valueOfI := reflect.ValueOf(i)
	//获取长度
	lenOfI := valueOfI.Len()
	fmt.Println("len = ",lenOfI)
	//获取所有的keys
	keys := valueOfI.MapKeys()
	for i:=0;i<len(keys);i++{
		key := keys[i]
		//获取对应的value
		value := valueOfI.MapIndex(key)
		fmt.Println("key=",key,"value=",value)
		num := value.Int()
		//更改数值
		//value.SetInt(num*num) 这种不行
		valueOfI.SetMapIndex(key,reflect.ValueOf(int(num*num)))
	}

}

func main() {
	m:=make(map[string]int,10)
	m["a"]=1
	m["b"]=2
	m["c"]=3
	testMap(m)
	fmt.Println(m)
}

```
结果：

```
len =  3
key= a value= 1
key= b value= 2
key= c value= 3
map[a:1 b:4 c:9]
```
### 结构体反射执行方法
代码：

```
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	Name string
	Age  int
}

func (s Student) Print() {
	fmt.Println("name=", s.Name, ";age=", s.Age)
}

func (Student) Plus(a int, b int) int {
	return a + b
}

func testMethod(i interface{}){
	//获取指针指向的值
	valueOfI := reflect.ValueOf(i).Elem()
	//获取对应的Type
	typeOfI := valueOfI.Type()
	//获取方法的数量
	methodNum:= typeOfI.NumMethod()
	fmt.Println("methodNum=",methodNum)
	for i:=0;i<methodNum;i++{
		//获取对应位置的方法
		method := typeOfI.Method(i)
		methodName := method.Name
		fmt.Println(methodName)
		if methodName =="Plus"{
			//调用Plus
			m := valueOfI.MethodByName(methodName)
			//声明一个切片
			args:=make([]reflect.Value,2)
			args[0]=reflect.ValueOf(78)
			args[1]=reflect.ValueOf(22)
			res := m.Call(args)
			//遍历结果
			for j:=0;j<len(res);j++ {
				fmt.Println(methodName," ivoke result",j+1," : ",res[j].Int())
			}

		}
	}
}

func main() {
	stu := Student{Name: "susan", Age: 58}
	testMethod(&stu)
}

```

结果：

```
methodNum= 2
Plus
Plus  ivoke result 1  :  100
Print
```
