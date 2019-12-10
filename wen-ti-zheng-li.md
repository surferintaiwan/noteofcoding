# 問題整理

## Model.save\(\)其實有第二個參數可以用

```javascript
// POST進行餐廳資料新增
app.post('/new', (req, res) => {
    const restaurant = new Restaurant({
        name: req.body.name,
        category: req.body.category,
        location: req.body.location,
        phone: req.body.phone,
        description: req.body.description,
        image: req.body.image,
        rating: req.body.rating
    })
    restaurant.save(err => {
        if (err) return console.error(err)
        return res.redirect('/restaurants')
        // ??助教，請問這邊有甚麼辦法可以直接拿到剛剛存進資料庫該筆資料的id嗎? 因為我想要再新增完資料後先跳轉到餐廳詳細頁面，而不是跳轉到所有餐廳頁面。
    })
})
```

Model.save 內的 callback 其實是有第二個參數的，而他就是經過處理並儲存過後的 restaurant 哦。

```text
restaurant.save((err, doc) => {
  if (err) return console.error(err)
  return res.redirect(`/restaurants/${doc.id}`)
})
```



## **在首頁每個delete button都加上addEventListent的寫法**

為了能夠在使用者按下delete時，都跳出確認是否刪除的alert，所以我寫了一支public/javascripts/delete-alert.js  
**我的疑問是，像遇到首頁有那麼多個button要加上監聽器時，這種寫法是對的嗎，還是其實有更好的寫法去達到我要跳出alert的效果?**  
我用了forEach迴圈  
又用了preventDefault  
又用了.submit\(\)

```text
let buttons = document.querySelectorAll('.delete-button')
buttons.forEach((eachButton) => {
    eachButton.addEventListener('click',(event) => {
        event.preventDefault()
        let result = window.confirm("確定要刪除?")
        if (result) {event.target.parentElement.submit()}
    })
})
```

提示刪除是個很常見的行為，做得好也問得好！其實有更好的寫法，就是把監聽器綁在 parent 上，看點擊到的 element 是否包含於 delete-button：

```text
const cards = document.querySelector('.cards-columns')
cards.addEventListener(function(e) {
  if (e.target.closest('.delete-button')) {
    console.log('delete')
  }
})
```

而 preventDefault 跟 submit 是要主動處理的沒有錯哦～

