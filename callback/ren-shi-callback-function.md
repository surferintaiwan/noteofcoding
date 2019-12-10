# 認識callback function

```text
// 準備一個有 10000 個項目的陣列
let queue = []
for (let i = 0; i < 9999; i++) {
  queue.push({ key: i })
}

// 非同步處理：單純印出一個字串
setTimeout(() => {
  console.log('hello world')
}, 0)

// 同步處理：用迴圈印出陣列
queue.forEach((item) => {
  console.log(item)
})
```

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6735/settimeout-2.gif)



![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6733/ExportedContentImage_05.png)

