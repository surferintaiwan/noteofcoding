# util.promisify套件

幫你自動包裝 Promise 物件 \(此動作稱為 **promisify**\)， node v8.x.x 之後的版本支援 [promisify](https://nodejs.org/dist/latest-v8.x/docs/api/util.html)。

以下示範一個使用 util.promisify\(\) 的範例，後續流程控制沒有改變，但 Promise 物件宣告的地方變簡短了：

```javascript
const {promisify} = require('util');
let fs = require('fs');
let path = require('path');
const basePath = './';

let readdir = promisify(fs.readdir);
let stat = promisify(fs.stat);

const isDir = async (subPath) => {
 let pathResult = await stat(subPath);
 if (pathResult.isDirectory()) {
   return subPath;
 } else {
   throw('data is not a directory')
 }
};

let dirs = async () => {
 return await readdir(basePath, {encoding: 'utf8', withFileTypes: true})
};

async function start () {
 try {
   let folders = await dirs();
   let fullPaths = [];
  
   folders.map(async (dir) => {
     let subPath = path.join(basePath, dir);
     fullPaths.push(subPath);
   });

   folders.map(async (dir) => {
    
     try {
       let subPath = await isDir(dir);
       fs.readdir(subPath, (error, fileName) => {
         console.log('SUCCESS in dir,', fileName);
       });
     } catch (e) {
       console.warn(e);   
     }
   });

 } catch (e) {
   console.warn(e);
 }
}

start();
```

