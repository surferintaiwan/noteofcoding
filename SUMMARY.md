# Table of contents

* [前言](README.md)
* [註冊/登入/登出功能](zhu-ce-deng-ru-deng-chu-gong-neng/README.md)
  * [建立user使用的路由](zhu-ce-deng-ru-deng-chu-gong-neng/jian-li-user-shi-yong-de-lu-you.md)
  * [打造註冊功能](zhu-ce-deng-ru-deng-chu-gong-neng/da-zao-zhu-ce-gong-neng.md)
  * [express-session儲存session](zhu-ce-deng-ru-deng-chu-gong-neng/expresssession-chu-cun-session.md)
  * [passport驗證登入與登出，以及每一頁權限](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/README.md)
    * [passport local驗證登入](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/passport-local-yan-zheng-ben-di-zi-liao-ku-de-shi-yong-zhe-zhang-mi.md)
    * [新增config/auth.js驗證每一頁的權限](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/yan-zheng-mei-yi-ye-de-quan-xian.md)
    * [req.logout\(\)登出](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/deng-chu.md)
    * [req.locals依據登入或登出顯示不同按鈕](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/req.locals-yi-ju-deng-ru-huo-deng-chu-xian-shi-bu-tong-an-niu.md)
    * [補充資料:passport導讀](zhu-ce-deng-ru-deng-chu-gong-neng/passport-ren-zheng/passport-dao-du.md)
  * [建立mongoDB資料表關聯\(將user跟todo關聯\)](zhu-ce-deng-ru-deng-chu-gong-neng/jian-li-mongodb-zi-liao-biao-guan-lian.md)
  * [bcryptjs把密碼加密](zhu-ce-deng-ru-deng-chu-gong-neng/bcryptjs-ba-mi-ma-jia-mi-1/README.md)
    * [註冊時把密碼加密](zhu-ce-deng-ru-deng-chu-gong-neng/bcryptjs-ba-mi-ma-jia-mi-1/bcryptjs-ba-mi-ma-jia-mi.md)
    * [登入時用來比對](zhu-ce-deng-ru-deng-chu-gong-neng/bcryptjs-ba-mi-ma-jia-mi-1/deng-ru-shi-yong-lai-bi-dui.md)
  * [passport-facebook登入](zhu-ce-deng-ru-deng-chu-gong-neng/passportfacebook-deng-ru/README.md)
    * [使用passport-facebook](zhu-ce-deng-ru-deng-chu-gong-neng/passportfacebook-deng-ru/untitled.md)
    * [製作Facebook登入按鈕](zhu-ce-deng-ru-deng-chu-gong-neng/passportfacebook-deng-ru/zhi-zuo-facebook-deng-ru-an-niu.md)
  * [dotenv使用環境變數隱藏敏感資料](zhu-ce-deng-ru-deng-chu-gong-neng/dotenv-shi-yong-huan-jing-bian-shu-yin-cang-min-gan-zi-liao.md)
  * [connect-flash增加警示訊息](zhu-ce-deng-ru-deng-chu-gong-neng/connectflash-zeng-jia-jing-shi-xun-xi.md)
* [佈署至heroku](bu-shu-zhi-heroku.md)
* [問題整理](wen-ti-zheng-li.md)
* [開發筆記](kai-fa-bi-ji.md)
* [callback、promise、async、await](callback/README.md)
  * [同步 v.s 非同步](callback/tong-bu-v.s-fei-tong-bu.md)
  * [認識callback function](callback/ren-shi-callback-function.md)
  * [callback hell](callback/callback-hell.md)
  * [以fs讀取檔案為例，練習callback、promise、async/await](callback/yi-fs-du-qu-dang-an-wei-li-lian-xi-callbackpromiseasyncawait.md)
  * [util.promisify套件](callback/util.promisify-tao-jian.md)
  * [Mongoose Queries為什麼有then?](callback/mongoose-queries-wei-shen-me-you-then.md)
