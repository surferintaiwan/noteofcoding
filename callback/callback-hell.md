# callback hell

![](https://assets-lighthouse.s3.amazonaws.com/uploads/image/file/6738/ExportedContentImage_02.png)

本來callback hell這個問題我大概知道怎麼產生出來的，就是因為一堆非同步程式，一個要帶入另一個，一層一層就變成callback hell，但是AC給了個作業是要做callback的，反而讓我陷入疑惑之中...

我想說在這個作業竟然教我們callback，那我應該有辦法實驗看看做出callback hell，搞得超久。

{% embed url="https://repl.it/@ShawnYu\_Hsiang/MetallicLoyalInterfacestandard" %}

最後是有用上面那個程式改寫大概做出callback hell，才意會到回想到才知道，callback hell是要搭配非同步程式才會出現的。  
不然如果只是像我下面一堆StepEvent的函式，要單單靠這些是實現不出來的。

{% hint style="warning" %}
補充個其它發現，就是原來在執行函式的時候，可以帶入一個要執行的函式進去，只要不干擾到原本函式的參數就行。  
**可是要記得，他會先執行那一支函式喔!!**

像這個，我就再stepEvent1裡面帶了個要執行的函式stepEvent2\(\)，它就會先執行stepEvent2\(\)，接著才執行stepEvent1  
stepEvent1\('step 1 - run', stepEvent2\(\)\)
{% endhint %}

{% embed url="https://repl.it/@ShawnYu\_Hsiang/callbackhell" %}





