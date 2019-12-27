# ES6_Javascript_最全最新讲解

## 数组方法(forEach、map、filter、find、every、some)
### forEach 遍历数组
```
colors.forEach((color)=>{
	console.log(color)
});
```
### map 遍历返回数组
```
var doubled = numbers.map((number)=>{
	return number*2
})
```
### filter 筛选返回数组
```
var filtered2 = products.filter((product)=>{
	return product.type === 'fruit';
})
```
### find 找到符合条件的对象
```
var use = users.find((user)=>{
	return user.name === 'Alex';
})
```
### every 一假即假
```
var every = computers.every((computer)=>{
	return computer.ram > 16
})
```
### some 一真即真
```
var some = computers.some((computer)=>{
	return computer.ram > 16;
})
```
### reduce 初始值, 或者计算结束后的返回值、当前元素）
```
var colors = primaryColors.reduce((previous,primaryColor)=>{
	previous.push(primaryColor.color)
	return previous
},[])
```

## 增强对象字面量

## 模板字符串

## 箭头函数

## 函数参数默认值

## 展开运算符

## 解构（对象,数组，对象数组）

## Class(万事皆对象)

## generator生成器

## 新数据结构(map,set)

## Promise

## fetch Apis

## async await

## async封装fetch