手写Promise源码
<script>
    // 封装成一个类
class Promise{

    // 构造方法
    constructor(executor){
        // 添加属性
        this.PromiseState = 'pending'
        this.PromiseResult = null
        // 声明属性（较安全）
        this.callbacks = []
        const self = this
        function resolve(data){
            //设置状态只能修改一次
            if(self.PromiseState != 'pending') return
            // 1.修改对象状态（promiseState）
            self.PromiseState = 'fulfilled'
            // 2.设置对象结果值
            self.PromiseResult = data
            // 改变状态成功后，调用成功的回调函数
            // if(self.callback.onResolve){
            //     self.callback.onResolve(data) 
            // }
            setTimeout(()=>{
                self.callbacks.forEach(item =>{
                    item.onResolve(data)
                })
            })
            
        }
    
        function reject(data){
    
            if(self.PromiseState != 'pending') return
    
            self.PromiseState = 'reject'
    
            self.PromiseResult = data
            // 改变状态成功后，调用成功的回调函数
            // if(self.callback.onReject){
            //     self.callback.onReject(data) 
            // }
            setTimeout(()=>{
                self.callbacks.forEach(item =>{
                    item.onReject(data)
                })
            })
        }
        try {
            // 同步调用执行器函数
            executor(resolve,reject)
        } catch (error) {
            // 修改 promise 对象状态为失败
            reject(error)
        }
    }
    // then方法
    then(onResolve,onReject){
        const self = this
        // 判断回调函数参数，如果then方法中多重嵌套且缺乏参数可这样解决
        if(typeof onReject !== 'function'){
            onReject = reason =>{
                throw reason
            }
        }
        if(typeof onResolve !== 'function'){
            onResolve = value =>{
            return value
            }
        }
        // then return Promise 对象
        return new Promise((resolve,reject) =>{
            function callback(type){
                try {
                    let result = type(self.PromiseResult)
                    // 判断
                    if(result instanceof Promise){
                        result.then(v=>{
                            resolve(v)
                        },r=>{
                            reject(r)
                        })
                    }else{
    
                        resolve(result)
                    }
                } catch (error) {
                    reject(error)
                }
            }


​            
            // 调用回调函数
            if(this.PromiseState=='fulfilled'){
                setTimeout(()=>{
                    callback(onResolve)
                },0)
                
            }


​            
            if(this.PromiseState=='reject'){
                setTimeout(()=>{
                    callback(onReject)
                },0)
                
            }
            // 判断 pending 状态，假当实例化对象时调用定时器，pending状态不改变则无法调用then函数
            // 当走到 then 这一步 Promise 的状态依然为 pending 时，说明实例化对象时调用了其他异步操作
            // 所以只有当执行到那个异步操作的时候（状态才会改变），才调用 then 中的回调函数（所以将其保存到 p 身上）
            if(this.PromiseState=='pending'){
                // 状态不确定，保存回调函数
                // 用这种方式保存回调，当出现实例调用多个 then 的时候，最后一个会覆盖前面保存的回调 
                // this.callback = {
                //     onResolve : onResolve,
                //     onReject : onReject
                // }
                this.callbacks.push({
                    // onResolve,
                    // onReject
                    // 当异步请求时，根据回调函数改变 then 的值
                    onResolve:function(){
                        callback(onResolve)
                    },
                    onReject:function(){
                        callback(onReject)
                    }  
                })
            }
        })
    }
    // catch方法
    catch(onReject){
        return this.then(undefined,onReject)
    }
    
    // 不属于实例对象上的方法，需要加上static
    static resolve(value){
        return new Promise((resolve,reject) => {
            if(value instanceof Promise){
                value.then(v=>{
                    resolve(v)
                },r=>{
                    reject(r)
                })
            }else{
                resolve(value)
            }
        })
    }
    
    static reject(reason){
        return new Promise((resolve,reject) => {
            reject(reason)
        })
    }
    
    static all(promises){
        return new Promise((resolve,reject)=>{
            let count = 0
            let arr = []
            for(let i = 0;i<promises.length;i++){
                promises[i].then(v=>{
                    count++
                    arr[i] = v
                    if(count==promises.length){
                        resolve(arr)
                    }
                },r =>{
                    reject(r)
                })
            }
        })
    }
    
    static race(promises){
        return new Promise((resolve,reject)=>{
            for(let i = 0;i<promises.length;i++){
                promises[i].then(v=>{
                    resolve(v)
                },r =>{
                    reject(r)
                })
            }
        })
    }
}


// Promise.prototype.then = function(onResolve,onReject){}

// Promise.prototype.catch = function(onReject){}

// Promise.resolve = function(value){}

// Promise.reject = function(reason){}

// Promise.all = function(promises){}

// Promise.race = function(promises){}
</script>
