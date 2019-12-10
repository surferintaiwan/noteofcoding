# 製作Facebook登入按鈕



![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6565/ExportedContentImage_00.png)

## 新增 Facebook Login 按鈕

請打開 `views/user/login.handlebars` 這個樣板，並修改你的程式碼：

```markup
<!-- views/register.handlebars -->
<div class="row mt-5">
  <div class="col-md-6 m-auto">
    <div class="card card-body">
      <h1 class="text-center mb-3"><i class="fas fa-sign-in-alt"></i> Login</h1>
      <form action="/users/login" method="POST">
        <div class="form-group">
          <label for="email">Email</label>
          <input type="email" id="email" name="email" class="form-control" placeholder="Enter Email" />
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" class="form-control" placeholder="Enter Password" />
        </div>
        <button type="submit" class="btn btn-primary btn-block">Login</button>
        <!-- 新增 Facebook Login Button -->
        <a href="/auth/facebook" class="btn btn-info btn-block">Facebook Login</a>
        
      </form>
      <p class="lead mt-4">
        No Account? <a href="/users/register">Register</a>
      </p>
    </div>
  </div>
</div>
```

