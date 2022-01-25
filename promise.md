# Promise

用來改善 JavaScript 非同步的語法結構


&nbsp;
## 狀態

在非同步的過程中，有著不同的進度狀態，可以藉由 Promise 看到以下狀態：

* pending：事件已經運行中，尚未取得結果
* resolved：事件已經執行完畢且成功操作，回傳 resolve 的結果（該承諾已經被實現 fulfilled）
* rejected：事件已經執行完畢但操作失敗，回傳 rejected 的結果

![image](https://github.com/chin1023/javascript/blob/main/promise.png)


&nbsp;
## 特性

* 當進入 fulfilled 或 rejected，狀態不會再改變
* 如果要判斷 Promise 是否完成，可依據 Promise 事件中的 resolve 及 reject 是否有被調用
* 在 Promise 的執行函式中，可以看到以下兩個屬性：`[[PromiseStatus]]`, `[[PromiseValue]]`


&nbsp;
創建Promise

```javascript
const promise = new Promise(function(resolve, reject) {
  
  if (非同步操作成功) {
    resolve(value);
  } else {
    reject(error);
  }

});
```


&nbsp;
* `Promise.prototype.then(onFulfilled, onRejected)`  
  Promise 物件生成後，可以用 then() 方法來綁定當 fulfilled 或 rejected 狀態時，進行分別要執行的函數，並回傳新的 Promise
  * 第一個函數是當 Promise 狀態變為成功時會被調用
  * 第二個函數是當 Promise 狀態變為失敗時會被調用，這個參數是選擇性的不一定需要

```javascript
//promise.then(onFulfilled, onRejected);

promise.then(function(value) {
  // 當狀態是 fulfilled (成功) 時，執行這個函數
  // value 是透過 resolve() 傳進來的參數
  
}, function(reason) {
  // 當狀態是 rejected (失敗) 時，執行這個函數
  // reason 是透過 reject() 傳進來的參數

});
```


&nbsp;
* `Promise.prototype.catch(onRejected)`  
  Promise 物件生成後，可以用 catch() 方法來綁定當 rejected 狀態時，以執行的函數，並回傳新的 Promise

```javascript
// promise.then(onFulfilled);
// promise.catch(onRejected);

promise.then(function(value) {
  // 當狀態是 fulfilled (成功) 時，執行這個函數
})

promise.catch(function(reason) {
  // 當狀態是 rejected (失敗) 時，執行這個函數
});
```


&nbsp;
* `then() vs catch() 的失敗回呼差異`  
  當遇到 reject 時，接下來會直接跳到 catch，在其後的 then 都不會執行, 故使用 then 接收失敗, 可以用此方式確保所有的鏈接都能夠被執行。

```javascript
promise(1)
  .then(success => {
    console.log(success);
    return promise(2);
  })
  .then(success => {
    console.log(success);
    return promise(0); // 這個階段會進入 catch
  })
  .then(success => {   // 由於上一個階段結果是 reject，所以此段不執行
    console.log(success);
    return promise(3);
  })
  .catch(fail => {
    console.log(fail);
  })
```


&nbsp;
* `Promise.all()`  
  透過陣列的形式傳入多個 promise 函式，在全部執行完成後回傳陣列結果，陣列的結果順序與一開始傳入的一致
  * 若所有 Promise 物件狀態都變為 fulfilled，而所有 Promise 物件，會被組成一個陣列回傳
  * 若其中一個 Promise 物件狀態變為 rejected，第一個被 reject 的值會被回傳

```javascript
var p1 = Promise.resolve(3);
var p2 = 1337;
var p3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
}); 

Promise.all([p1, p2, p3]).then(function(values) { 
  console.log(values);
});

// 會顯示 [3, 1337, "foo"]
```


&nbsp;
* `Promise.race()`  
  透過陣列的形式傳入多個 promise 函式，在全部執行完成後回傳單一結果，回傳結果為第一個執行完成的

```javascript
var p1 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 500, 'one'); 
});
var p2 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 100, 'two'); 
});

Promise.race([p1, p2]).then(function(value) {
  console.log(value);
});

// 會顯示 "two"，因為 p2 比較快被 resolve
```


&nbsp;
* `Promise.resolve()`  
  將一個物件轉型為 Promise (如果它不是一個 Promise 物件)，然後立刻 resolve

```javascript
Promise.resolve('Success').then(function(value) {
  console.log(value);
}, function(value) {
  console.log('Fail');
});

// 輸出 "Success"
```


&nbsp;
* `Promise.reject()`  
  將一個物件轉型為 Promise (如果它不是一個 Promise 物件)，然後立刻 reject

```javascript
Promise.reject(new Error('Fail')).then(function(error) {
  console.log('Success');
}, function(error) {
  console.log('Fail');
});

// 輸出 "Fail"
```


&nbsp;
* `Promise.finally()`  
  可以使用 finally 來確認工作結束，finally 不帶有任何參數

```javascript
Promise.resolve('OK')
  .then(result => {
    console.log(result);
    return Promise.resolve('Hi')
  })
  .then(result => {
    console.log(result);
    return Promise.reject('Oops');
  })
  .catch(error => {
    console.log(error);
  })
  .finally(() => {
    console.log('finally');
  });

// OK
// Hi
// Oops
// finally
```


&nbsp;
## Promise 優缺點

優點:
1. 統一非同步 API 
2. 解決 callback hell 的問題，將非同步操作以同步操作的流程表達出來
3. 程式碼易讀，更好的進行錯誤處理

缺點:
1. 無法取消Promise，一旦新建它就會立即執行，無法中途取消
2. 如果不設置回調函數，Promise內部拋出的錯誤，不會反應到外部
3. 當處於Pending狀態時，無法得知目前進展到哪一個階段



