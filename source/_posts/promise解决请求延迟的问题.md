---
title: promise解决请求延迟的问题
date: 2020-03-05 23:15:43
tags: JavaScript
categories: JavaScript
---
## 前言
利用Promise实现数据多个请求加载完成时执行某个方法  
在实际开发中常常有些业务的数据是来自多个接口的，因为ajax是异步，这样就导致我们需要判断是否请求到了数据然后在做其他的逻辑，在Promise没有出现之前，通常我们的解决方法是，第一粗暴的改异步为同步，但这样会造成阻塞，异步好像又失去了意义，第二也就是大家常用的解决办法用回调既一个异步执行完成后在执行下一个请求，这样看比第一种要好太多了，但是问题又来了，延迟延迟延迟，请求越多最后的那个请求延迟就会越严重，而且这样请求多了之后逻辑就会变得很乱。。。痛苦不堪，还好es6带来的Promise正好能解决这个东西，关于`Promise`具体详情请百度，这里大致只说三个东西，`resolve`, `reject`，`Promise.all` 对应成功执行，失败执行，返回全部状态，实例贴代码 很容易理解。  

然鹅我们async的话，各种await之间也要等到上一个await执行完毕之后，才会执行下一个await，所以还是解决不了延迟的情况，所以我们采用ES6中的promise，来解决并行执行的问题
```
const promist = new Promise((resolve, reject) => {
    this.$http.jsonp(getRegionNameByIdApi, { params: { regionId: dqarr } }).then((res) => {
        this.dqarr = res.body.data
        resolve(res.body.data)
    }, (err) => {
        console.log(err)
    })
})
const promist1 = new Promise((resolve, reject) => {
    this.$http.jsonp(getPartnerNameByIdApi, { params: { partnerId: sharr } }).then((res) => {
        this.sharr = res.body.data
        resolve(res.body.data)
    }, (err) => {
        console.log(err)
    })
})
Promise.all([promist, promist1]).then((resultList) => {
    console.log('results:', resultList)
    resultList[0].map((item) => {
        this.tableData.map((val) => {
            if (item.regionId === val.regionId) {
                val.region_name = item.regionName
            }
            if (item.regionId === val.areaId) {
                val.area_name = item.regionName
            }
            if (item.regionId === val.shopId) {
                val.shopName = item.regionName
            }
            if (resultList[1][val.partnerId]) {
                val.partner_name = resultList[1][val.partnerId]
            }
        })
    })
    this.$set(this.tableData, this.tableData)
})
```


## Promise实现任务顺序执行
在`.then`里要`return`一个`new Promise`，这样后续才能继续使用`.then`执行后续操作：  
```
var result=new Promise(function(resolve,reject){
	setTimeout(function(){
		resolve("one");
	},3000)
}).then(function(data){
	console.log(data);
	return new Promise(function(resolve,reject){
		setTimeout(function(){
			resolve("two");
		},3000)
	})
}).then(function(data){
	console.log(data);
})
```