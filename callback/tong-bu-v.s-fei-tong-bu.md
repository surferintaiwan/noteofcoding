# 同步 v.s 非同步

## **同步處理：寫出來的就是要執行的**

如果用同步程式來讓媽媽用餐，媽媽就會依序吃下餐點，先喝咖啡，然後蛋，然後蛋糕，同時，Energy 也會依序增加：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6723/ExportedContentImage_04.png)

## **非同步處理：有 callback function**

若是採用非同步的方式，需要監聽媽媽的動作並設計 callback，比較一下同步的程式，在形式上有很大的差別：

### **什麼是 Callback Function**

調用 `mother.on()` 的時候，參數裡有一些匿名函式，這些匿名函式並沒有馬上執行，這些就是 callback function：



![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6725/ExportedContentImage_06.png)

### **Callback 執行機制：Event loop**

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6726/ExportedContentImage_07.png)

### \*補充: 在使用for loop記得變數要宣告

{% hint style="info" %}
關於非同步其實在前面一點的作業也有遇到，問助教有了如下紀錄。

有個作業裡面我用了for迴圈要去跑每個使用者資料，把他的密碼透過bcryptjs處理，接著存回DB，可是就算我有很多筆使用者資料，存進去還是最後一個人的資料，如下。

[https://github.com/surferintaiwan/Semester3-A17-recordwebsite/blob/master/models/seeds/seeder.js\#L97](https://github.com/surferintaiwan/Semester3-A17-recordwebsite/blob/master/models/seeds/seeder.js#L97)

答案:

只要把eachUser用let宣告變數就可以了，因為 for 在一個瞬間就做完兩次了，而因為沒有宣告 eachUser 這個變數，就預設修改到全域變數，瞬間執行兩次也讓 eachUser 最終被改成了第二個值，也就是小王，等到下面一系列的非同步操作準備存入資料庫的時候當然也都變成小王了。（你可以把 hash 想像要花五秒才能建好），而 let 就沒問題是因為 let 宣告後值會保留在 scope 內，也就是大括弧內，就算裡面的動作是非同步的還是可以取到當時宣告 let 變數的值而不是找不到東西就取到了全域變數。

eachUser = userData\[0\] // eachUser 變成了全域變數   
我之後要執行 console.log\(eachUser.name\)   
eachUser = userData\[1\]   
我之後要執行 console.log\(eachUser.name\)

開始執行剛剛說好的之後要做的事情，想要印出 eachUser 但沒有這個變數，往大括弧外搜尋，直到看到全域變數，此時 eachUser 已經變成小王。

{   
let eachUser = userData\[0\] // 區域變數 我之後要執行 console.log\(eachUser.name\)   
}   
{   
let eachUser = userData\[1\] // 區域變數 我之後要執行 console.log\(eachUser.name\)   
}

開始執行剛剛說好的之後要做的事情，第一個 console.log 在自己的大括弧內找到了 eachUser 這個區域變數，正確印出，同理第二個也是。
{% endhint %}

### \*補充: 多個同層的非同步程式

{% hint style="info" %}
此時就對於有沒有let宣告為何會有如此大的差異感到疑惑，進而把前面的for loop拆解深入研究，拆解出來後又發現每個非同步程式裡又有非同步程式，此時就又陷入了疑惑中，究竟在Event Loop裡對於這種裡面有非同步程式的同層非同步程式執行順序是甚麼?
{% endhint %}

![](https://assets-lighthouse.s3.amazonaws.com/uploads/attachment/attachment/6201/_________.png)

```text
console.log("Hi!");

setTimeout(function timeout1() {
    console.log("1");
    setTimeout(function timeout11() {
        console.log("1-1");
    }, 2000);
}, 10000);

setTimeout(function timeout2() {
    console.log("2");
     setTimeout(function timeout21() {
        console.log("2-1");
    }, 2000);
}, 5000);


```

### \*補充: 不是先進Event Loop就先執行

{% hint style="warning" %}
最後把上面這段程式丟到latentflip.com去跑，才發現其實在主程序跑的時候，它發現非同步程式雖然會將之登錄進Event Loop，但還是要看這些非同步程式哪一支比較快執行完，接著就會被送到Callback Queue等候執行。  
**所以不是先進去Event Loop的就先執行，一直搞錯了。**

[http://latentflip.com/loupe/?code=Y29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MSgpIHsKICAgIGNvbnNvbGUubG9nKCIxIik7CiAgICBzZXRUaW1lb3V0KGZ1bmN0aW9uIHRpbWVvdXQxMSgpIHsKICAgICAgICBjb25zb2xlLmxvZygiMS0xIik7CiAgICB9LCAyMDAwKTsKfSwgMTAwMDApOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MigpIHsKICAgIGNvbnNvbGUubG9nKCIyIik7CiAgICAgc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MjEoKSB7CiAgICAgICAgY29uc29sZS5sb2coIjItMSIpOwogICAgfSwgMjAwMCk7Cn0sIDUwMDApOwoK!!!](http://latentflip.com/loupe/?code=Y29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MSgpIHsKICAgIGNvbnNvbGUubG9nKCIxIik7CiAgICBzZXRUaW1lb3V0KGZ1bmN0aW9uIHRpbWVvdXQxMSgpIHsKICAgICAgICBjb25zb2xlLmxvZygiMS0xIik7CiAgICB9LCAyMDAwKTsKfSwgMTAwMDApOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MigpIHsKICAgIGNvbnNvbGUubG9nKCIyIik7CiAgICAgc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0MjEoKSB7CiAgICAgICAgY29uc29sZS5sb2coIjItMSIpOwogICAgfSwgMjAwMCk7Cn0sIDUwMDApOwoK!!!)
{% endhint %}

