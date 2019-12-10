# Mongoose Queries為什麼有then?

之前提到，使用 async/await 的前提，是程式碼已經採用 promise-based。

若是自己撰寫的程式碼，自己當然知道前後文有沒有採用 Promise，但如果是第三方函式庫，要怎麼確認原始碼是否採用 promise based 呢？

#### 正常情況

首先，如果原始碼就是用 Promise 編寫的，通常在標題或官方文件上，能看到明顯的關鍵字，點出「Promise-based」的資訊：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6747/ExportedContentImage_00.png)

 但之前有眼尖的同學注意到，我們使用過的 Mongoose queries，它可以使用 `then`，看起來很像 Promise 物件，但官方文件上聲明了「Queries are not promises」：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6748/ExportedContentImage_01.png)

## **簡單版結論：是的，它不是 Promise**

從結論來說，Mongoose queries 可以使用 `then` 主要是因應 co / promise 的需求，他並不是透過 Promise 延伸出來的，而是一個完完全全仿生 Promise .thenable 的物件。

因此他才會說自己是「not promise, but they have a .then\(\)」。

## 要使用.then\(\)就要先用.exec\(\)

但是，官方文件中又提到「如果需要完整使用 Promise，需要使用 `.exec()` 函式」，這又是什麼呢？

從 [Mongoose `exec()` 程式原始碼](https://github.com/Automattic/mongoose/blob/1474fe8ab90d06dd38e82154894d855a21c88b6b/lib/query.js#L4167)可以看到 exec 函式的內容主要是進行 callback 和 promise 物件同步相容的處理方式，意味使用者可以用兩種方式調用 exec。

Callback 的方式：

```text
Model.findOne().exec(function (model) {
  return model
})
```

或者 Promise 的方式：

```text
Model.findOne().exec().then((function (model) {
  return model
}))
```

 乍看一下很像，如果只是單純的呼叫 `exec` 而沒有傳入任何 callback 的話，其實他會直接回傳 promise object 回來，意味著我們就可以用 async/await：  


```text
let model = await Model.findOne().exec()
```

這部分主要是為了兼容 callback mode 和 Promise mode 兩種模式可以在同一種 function 進行使用。

如果再對照 [Mongoose 測試原始碼](https://github.com/Automattic/mongoose/blob/aa80c6e72a7a5c92060dcc3515c646b01dda2129/test/model.querying.test.js#L108)，可以看出若沒有使用 `exec` 的話，就只會是原本的 Query 物件。

## 為什麼這麼複雜?

因為 mongoose 專案在純 callback 年代就開始有了 \(大約五年多前，甚至更久，那個時候 Promise 還沒有開始定案\)。

在那個時期，大部分的開發都是採用 callback 的方式來進行 JavaScript 程式編寫，因此為了向下相容，就出現這樣的結果。

在許多 node project 這還蠻常見的做法。

因此，你不一定能單純從語法風格來判斷這個專案是否為 Promise，你需要從官方文件的說明上來判斷，而萬無一失的方法是看它的原始碼運作方式。

