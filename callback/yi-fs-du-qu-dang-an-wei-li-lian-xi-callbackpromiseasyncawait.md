# 以fs讀取檔案為例，練習callback、promise、async/await

## callback寫法

```javascript
let fs = require('fs');
let path = require('path');
const basePath = './';

fs.readdir(basePath, {encoding: 'utf8', withFileTypes: true}, (err, dirs) => {
  console.log(dirs)// 掃瞄 dirs 判斷哪些是資料夾
  dirs.map((dir) => {
    // 取得資料夾路徑，存入 subPath
    let subPath = path.join(basePath, dir.name || dir);
    // 用 stat 檢查 subPath 狀態，將結果放進 pathResult
    fs.stat(subPath, (error, pathResult) => {
      // 檢查 pathResult 是否為資料夾
      if (pathResult.isDirectory()) {
        // 若是資料夾，用 readdir 印出下一層的檔案目錄
        fs.readdir(subPath, (error, fileName) => {
          console.log('file in dir,', fileName);
        })
      }
    });
  });
  console.log(dirs);
});
```

## promise寫法

1. 前置作業
   1. 要先有個new promise實例，裡面放入你的非同步程式
   2. 在promise實例裡寫下return resolve\(要回傳的值\)
   3. 在promise實例裡寫下return reject\(失敗要回傳的值\)，這一項也是可以不寫
2. 執行階段
   1. 用.then\(\)去接return resolve的值
   2. 用.catch\(\)去接return reject\(\)的值

```javascript
let fs = require('fs');
let path = require('path');
const basePath = './';

let dirs = new Promise((resolve, reject)=>{
  fs.readdir(basePath, {encoding: 'utf8', withFileTypes: true}, (err, dirs) => {
    return resolve(dirs)
  })
})

const isDir = (subPath) => {
  return new Promise((resolve, reject) => {
    fs.stat(subPath, (error, pathResult) => {
      if (pathResult.isDirectory()) {
        return resolve(subPath)
      }
      return reject(`[ERROR]: ${subPath}`)
    })
  })
}

const readDir = (subPath) => {
  return new Promise((resolve, reject)=>{
    fs.readdir(subPath, (error, fileName) => {
      return resolve(console.log('file in dir,', fileName))
    })
  })
}

dirs
.then((dirs) => {
  console.log(dirs)
  let fullPaths = []
  dirs.map((dir) => {
    let subPath = path.join(basePath, dir.name || dir)
    fullPaths.push(subPath)
  })
  return fullPaths
})
.then((dirs) => {
  dirs.map((dir) => {
    isDir(dir)
    .then((subPath) => {
      readDir(subPath)
      .then()
    })
    .catch((error) => {
      console.warn('ERROR', error)
    })
  })
})
```

## async/await寫法

1. 前置作業
   1. 要先準備好promise實例，這部分跟前面的promise寫法的promise實例一樣，差別只在後面執行。
2. 執行階段
   1. 外層一定要有個async 

      1. 如果接在新命名的函式，就如async function start\(\) {}
      2. 如果接在callback function裡，那就直接在callback前面接async 就如async \(\) =&gt; {}

   2. 承上，如果是接在新命名的函式，就要在最後面接上執行函式的命令
   3. async裡面ㄧ定會有try{} 跟 catch{}
   4. try裡面ㄧ定會有await

```javascript
let fs = require('fs');
let path = require('path');
const basePath = './';

let dirs = new Promise((resolve, reject)=>{
  fs.readdir(basePath, {encoding: 'utf8', withFileTypes: true}, (err, dirs) => {
    return resolve(dirs)
  })
})

const isDir = (subPath) => {
  return new Promise((resolve, reject) => {
    fs.stat(subPath, (error, pathResult) => {
      if (pathResult.isDirectory()) {
        return resolve(subPath)
      }
      return reject(`[ERROR]: ${subPath}`)
    })
  })
}

const readDir = (subPath) => {
  return new Promise((resolve, reject)=>{
    fs.readdir(subPath, (error, fileName) => {
      return resolve(fileName)
    })
  })
}


async function start() {
    try {
        let folders = await dirs
        let fullPaths = []
        folders.map((dir) => {
            let subPath = path.join(basePath, dir.name || dir)
            console.log('dir在這',dir)
            console.log('subPath在這',subPath)
            fullPaths.push(subPath)
        })
        fullPaths.map(async (dir) => {
            try {
                console.log('第二個dir',dir)
                let subPath = await isDir(dir.name || dir)
                    async function second(subPath)  {
                        try {
                            let fileName = await readDir(subPath)
                            console.log('SUCCESS in dir,' , fileName)
                        }
                        catch(error) {
                            console.warn(error)
                        }
                    }
                    second(subPath)
            } catch(error) {
                
                console.warn(error)
            }
        })
    } catch(error) {
        console.warn(error)
    } 
}
start()
```

{% hint style="info" %}
這邊可以觀察到，非同步程式有三支，所以等於是有三組的try及catch。  
在第三組的部分，因為沒有callback可以使用async，所以我建立了一個新的函式，後面再用執行函式去跑。

```javascript
second(subPath)
```
{% endhint %}

{% hint style="info" %}
但最後才發現，助教說我不用為了第三個非同步程式接async，因為一個async可以放下多組的try及catch。

```javascript
fullPaths.map(async (dir) => {
  try {
       let subPath = await isDir(dir.name || dir)
       async (subPath) => {
         try {
              let fileName = await readDir(subPath)
              console.log('SUCCESS in dir,' , fileName)
          }
          catch(error) {
              console.warn(error)
           }
       }
 } catch(error) {           
    console.warn(error)
 }
}) 
```

調整提示: 在定義 async 的函式裡面可以用複數個 await

```javascript
// 上面的 wait 執行完才執行下一個 wait
async function series() {
  await wait(500);
  await wait(500);
  return "done!";
}

// 兩個 wait 會同時被執行
async function parallel() {
  const wait1 = wait(500);
  const wait2 = wait(500);
  await wait1;
  await wait2;
  return "done!";
}
```
{% endhint %}



