# dotenv使用環境變數隱藏敏感資料

在上個單元，我們已經完成 Facebook 登入的功能。但這個功能的程式碼中包含「應用程式編號 \(App ID\)」和「應用程式密鑰 \(App Secret\)」等敏感資訊。透過 App Secret，外人就能獲得所有 App 的訪問權限，如果我們把程式碼連帶這些資訊直接 push/commit 到公開的 GitHub repository 上，就會產生資安的疑慮，所以我們要學習如何將敏感資訊隱藏起來。  
要將兩個資訊從你的 GitHub repository 中隱藏，則要看我們的程式碼是運行在本機的「開發模式 \(development mode\)」，或是已經佈署在例如 Heroku 這種平台的「正式上線模式 \(production mode\)」。  
現在，我們會先學習在 development mode 的處理方法。等到下一個章節我們學習 Deployment 的部分時，會再來使用 Heroku 本身的設定去隱藏資訊。

在 Node.js 底下，我們可以透過 `process.env` 來讀取電腦系統中的環境變數，並在執行過程中新增、修改變數內容，而不影響到作業系統中的環境變數。  
認識了 `process.env`，那到底該如何安全的存取敏感資訊呢？我們可以把要隱藏的敏感資訊，寫進一個名為 `.env` 的獨立檔案中，接著，再用 .gitignore 把這個檔案從你的 repository 排除。最後透過 [dotenv](https://github.com/motdotla/dotenv#readme) 這個套件，讀取 `.env` 檔，把剛剛設定的敏感資訊暫時存放到 Node.js 的執行環境，之後在程式中，我們就可以透過 `process.env` 來存取敏感資訊了。



## 安裝 dotenv

```text
npm install dotenv
```

## 把要隱藏的資訊寫進 `.env` 

成功安裝 dotenv 後，請在專案的「根目錄」新增 `.env` 這個檔案，並把 Facebook 提供的 App ID、App Secret、與你設定的 callback URL 放到變數裡：

```text
// .env

FACEBOOK_ID=xxxxxxxx
FACEBOOK_SECRET=xxxxxxxx
FACEBOOK_CALLBACK=http://localhost:3000/auth/facebook/callback
```

加進 `.env` 之後，dotenv 就會把這些變數加進環境變數裡，而我們就會得到長這樣的兩個環境變數：

* `process.env.FACEBOOK_ID`
* `process.env.FACEBOOK_SECRET`

在這個單元的延伸閱讀裡，我們提供了一些 Node.js 的環境變數 `process.env` 的參考資料。

## 在 `config/passport.js`用同樣的`process.env.變數名`取代要隱藏的敏感資訊

我們再把寫在 `config/passport.js` 中的資訊，用環境變數來取代：

```javascript
// config/passport.js
// ..
passport.use(
  new FacebookStrategy(
  {
    clientID: process.env.FACEBOOK_ID,
    clientSecret: process.env.FACEBOOK_SECRET,
    callbackURL: process.env.FACEBOOK_CALLBACK,
    profileFields: ['email', 'displayName']
  }
）
```

## 在 `app.js` 加上分辨應用程式執行模式（development 或是 production mode）的邏輯

接下來，我們要修改 `app.js` 來加上一個判別邏輯：如果應用程式不是在「正式上線模式 \(production mode\)」中執行，就透過 dotenv 去讀取在 `env` 檔案裡的資訊。

由於在 production mode 中，`process.env` 裡通常會自動新增 `NODE_ENV`，並將值設定成 "production"；也因此我們可以透過判斷 `process.env.NODE_ENV` 的值來判斷執行環境。

如果執行環境真的是在 production mode 上，那麼就會用別的方法設定環境變數。我們會在下一個章節《Deployment》有詳細的介紹。

```text
// app.js
// ..
const app = express()
// 判別開發環境
if (process.env.NODE_ENV !== 'production') {      // 如果不是 production 模式
  require('dotenv').config()                      // 使用 dotenv 讀取 .env 檔案
}
```

## 把 `.env` 加進 `.gitignore`

 接下來，我們把 `.env` 加進 `.gitignore` 裡，讓它從我們的 repository 中排除：

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6706/ExportedContentImage_01.png)

## 檢測 Facebook 登入是否能正常運作



