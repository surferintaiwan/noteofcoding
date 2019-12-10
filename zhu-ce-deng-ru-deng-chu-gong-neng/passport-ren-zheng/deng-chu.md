# req.logout\(\)登出

## req.logout\(\)來登出

```text
// route/user.js

// 註冊頁面
// ...

// 註冊檢查
// ...

// 登出
router.get('/logout', (req, res) => {
  req.logout()
  res.redirect('/users/login')
})
```

目前還沒有登出按鈕可以按，因為登出按鈕是在使用者登入的狀況下才看得到，所以會在下一節說明，判別使用者是登入還是登出狀況下，分別該顯示甚麼按鈕。

