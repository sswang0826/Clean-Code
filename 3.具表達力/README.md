# 具表達力
增加表達力有幾種方式<br>
<br>
<br>
<br>
<br>
適當的命名變數或函式，能讓閱讀更方便<br>
以下列出幾件需要注意的事情<br>
<br>
* 避免誤導<br>
例如 AccountList的型態不是list這種會讓人誤導的名詞<br>
或是 ControllerForEfficientHandlingOfStrings與ControllerForEfficientStorageOfStrings這種名字之間只有小小的差別<br>
* 避免模糊<br>
例如 account_data及account_info這種不夠清晰的名字<br>
* 避免否定描述<br>
否定描述容易讓閱讀變困難<br>
* class使用名詞說明，function使用動詞說明<br>
如此一來就 class_A.fun_B 代表的是<br>
讓 名詞A 做 動作B 的行為<br>
* 不要使用建構子<br>
使用另外的function來init可以更好的說明在做什麼動作，如:<br>
```C++
Complex pt = Complex.CreateFromRealNumber(2.0);
```
會比<br>
```
Complex pt = new Complex(2.0);
```
還要好懂<br>
<br>
