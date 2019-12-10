# 補充資料:passport導讀

準備好 express-session 之後，接下來我們就可以網頁應用程式添加「登入 \(login\)」功能，這個過程會用到一個叫 Passport 的第三方套件。

在【[6 JavaScript User Authentication Libraries for 2019](https://blog.bitsrc.io/6-javascript-user-authentication-libraries-for-2019-6c7c45fbe458)】這篇文章中，列出了一些 JavaScript 社群內、專精於使用者認證的函式庫，其中 Passport 高居榜首：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8939/_____2019-09-18_16.36.35.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8939/_____2019-09-18_16.36.35.png)

由於 Passport 是應用最廣、功能最強大的函式庫，接下來我們要來使用 Passport 實作使用者認證的功能。提醒各位同學，設定 Passport 的過程，會是你學習 Express.js 以來，前置設定最多、最複雜的過程。接下來你可能會對**大量貼上程式碼**感到不安，以下是 Jack 助教錄製給同學的一支影片，示範了他怎麼從 Passport 官方文件上取得必要資訊，知道如何設定這個套件：

即使看了 Jack 助教特地錄製的影片，第一次使用時，難免還是會有種「除了跟著貼 code 實在是不知道自己在做什麼」的挫折。這裡先和同學預告一下，Passport 是接下來每個專案都會使用到的套件 \(因為都有使用者認證功能\)，現在在 To-do List 專案第一次使用，可能會感到自己理解不夠深入、無法駕馭 Passport，即使認真地打開文件，好像也只有字面上的理解 \(甚至有多太「生字」讓你連文件都讀不下去\)，這個不安全感的來源，一部分是不夠了解 Passport 這個第三方套件，另一部分是來自對後端專案的運作、對 HTTP 的機制還不夠熟悉，之後搭配著其他的內容，會漸漸補足更多的知識，也會在未來的專案中多次用到 Passport，漸漸摸到 Passport 的其他功能 \(在本章節的後半，我們也會運用 Passport 來整合 Facebook Login\)，等到對整個 Express.js 有深入的理解時，你會專案中用到的套件，產生不同層次的感受，這段過程，需要花點時間才能見樹又見林。

有了心理準備之後，讓我們來看看這個 Passport 究竟是什麼。

#### 導讀官方設定教學

接下來我們來讀讀看官方的設定教學。

在這裡先說明一下，在查看套件的官方文件時，你通常會在三個不同的地方，找到官方提供的文件：

* GitHub 的 README：[https://github.com/jaredhanson/passport](https://github.com/jaredhanson/passport)
* NPM 的 README：[https://www.npmjs.com/package/passport](https://www.npmjs.com/package/passport)
* 官網：[http://www.passportjs.org/](http://www.passportjs.org/)

GitHub 和 NPM 的資訊是同步連動的，所以兩份文件內容一致，通常會很聚焦在安裝與初始化的設定，如果是第一次使用套件，通常會建議先讀 GitHub 或 NPM 的文件。

至於各家廠牌的官方網站，會有什麼內容就不一定，用什麼風格來撰寫也很難說。以 Passport 的例子，由於 Passport 的內容非常豐富，對目前的同學來說，反而難以聚焦到我們需要的部分。建議大家可以先從 GitHub 或 NPM 上的資訊開始，反而會比較容易入門。

接下來我們會導讀官方的設定教學，底下的說明以 [NPM 文件](https://www.npmjs.com/package/passport)為主，[官網的設定教學](http://www.passportjs.org/docs/configure/)為輔，同學可以將導讀的內容可以和官方文件的說明互相參看，下一個單元會直接進到專案做設定。

在快速看過文件的大標題之後，我們大略掌握到等下需要完成的幾件事情：

* 安裝 \(Install\)
* 使用 \(Usage\)
  * Strategies
  * Sessions
  * Middlewave
  * Authenticate Requests

**Passport Strategies**

Passport 需要配合「[認證策略 \(strategies\)](http://www.passportjs.org/packages/)」模組來使用。Passport 目前提供超過 500 種認證策略，讓開發者可以很容易地將自己的產品跟 Facebook、Google、Twitter 等服務整合。不同的策略模組，提供不同的認證機制或模式。例如：使用 Facebook 登入是一種策略、使用「本地 \(local\)」資料庫認證則是另一種策略。[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8791/ExportedContentImage_00.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8791/ExportedContentImage_00.png)

因為這次我們是在本地端的資料庫開發，所以在這個單元時我們會使用「本地策略 \(passport-local\)」這個策略模組。

在課程後半，我們會用到另一個 passport-facebook 來整合臉書帳號，未來的課程還會再遇到其他的策略。

先來看看官方文件上，有關如何設定策略的講解：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8792/ExportedContentImage_01.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8792/ExportedContentImage_01.png)

讓我們參考這段範例，來解釋一下如何使用 Passport：

1. 透過 `passport.use` 來套用認證策略模組；這邊我們使用 `LocalStrategy`
2. `LocalStrategy` 的 callback 中有 `username`、`password`、`done` 這三個參數。`done` 是用來告訴 Passport 登入結果的 callback function，接收的參數依序為 錯誤訊息、用戶物件 及 額外資訊物件。如果登入成功，`done` 執行後會將用戶物件 `user`加到 `req` 物件中，以便我們後續使用。而失敗的話，這裡會被設定為 `false`。
3. 得到使用者輸入的 `username` 後，用 `User.findOne` 來檢查並取得資料庫中名為 `username` 的使用者資料
4. 判別三種不同的錯誤情況。三種不同的錯誤情況分別是：
   1. 資料庫讀取失敗，done 接收錯誤訊息
   2. 找不到使用者輸入的 `username`，用戶物件為 false
   3. 找到使用者，但發現密碼錯誤，用戶物件為 false
5. 如果能取得密碼並認證成功，把 user 傳入 done 中，完成認證流程，`done` 就會回傳 `user`

這裡要注意的是 `LocalStrategy` 預設是以 `req.body` 和 `req.query` 中的 `username` 與 `password` 來認證。但我們也可以[更換其他參數名稱](https://github.com/jaredhanson/passport-local#parameters)，但需要做額外的設定，這個部分在之後的實作中會提到。

**Passport 如何支援 session 功能**

Passport 只提供認證，但它可以配合如 Express-session 的 middleware 來實現 session 功能。我們看看在 [Passport 的官方文件](http://www.passportjs.org/docs/configure/)裡有關 session 的部分：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8793/ExportedContentImage_02.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8793/ExportedContentImage_02.png)

在這裏我們引用 [PJ 老師在他部落格中的翻譯](https://pjchender.github.io/2017/09/26/node-passport-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98%EF%BC%88learn-to-use-passport-js%EF%BC%89/)：

「在一個典型的 web 應用中，驗證訊息 \(credentials\) 只有在登入的時候會被傳送，如果驗證成功，會在 session 中記錄並儲存 cookies 在瀏覽器中。後續的 request 都不會再帶有 credentials，而是透過 cookie 來辨認 session ，為了要支援 login sessions，在 Passport 中會序列化 \(serialize\) 和反序列化 \(deserialize\) 在 session 中的使用者實例 \(instance\)。」

「序列化」和「反序列化」都是 Passport 轉換資料格式的過程。簡單來說，當使用者成功登入後，應用程式會為這個使用者開啟一個 login session，讓他不需要重複登入。程式碼會為每位使用者創建專屬的記號，儲存在瀏覽器的 cookie 中。（還記得我們飲料店會員卡的比喻嗎？）

這個記號是由 express-session 隨機產生的。

為了支援 login session 功能，Passport 的「序列化」功能會把「使用者實例 \(user instance\)」轉化為字串存在伺服器端的 session 資料。至於「反序列化」就是透過 session 資料，取回擁有這個 login session 的使用者實例。

如果你覺得目前的解釋有點抽象，那接下來請看看我們如何透過程式碼實際應用 serialize 與 deserialize！

Passport 直接提供了 serialize 與 deserialize 的程式碼給我們使用：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8794/ExportedContentImage_03.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8794/ExportedContentImage_03.png)

從這段程式碼你可以看到，Passport 提供的「序列化」和「反序列化」不會把整個 user instance 放在 session 資料了，而只是放進 `user.id`。這樣是為了節省存放 session 資料的空間。

我們稍後會直接使用這段 Passport 提供的「序列化」和「反序列化」程式（直接複製在我們程式碼裡）。

好的！一切準備好了，我們就來用 Passport 實作我們的登入功能。

#### Middleware

這一段談的是在 app.js 裡，如何使用 `app.use` 來掛載 Passport 並開始使用。[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8795/ExportedContentImage_04.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8795/ExportedContentImage_04.png)

加入這兩行之後就可以使用。

**Authenticate Requests**

掛載了 Passport 之後，接下來會跑到路由設定裡使用 middleware，從下例中，你會看到如何真正的運用 Passport 來做到身份驗證：[![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8796/ExportedContentImage_05.png)](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/8796/ExportedContentImage_05.png)

直接把 middleware 設定加到路由裡，就能在 function 被執行之前，先檢查身份。

以上是我們讀過官方文件之後，瞭解到的整體概念，現在就讓我們進入專案，把這些設定整合到自己的專案裡。

