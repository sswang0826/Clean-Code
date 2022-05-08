# 具表達力
<br>
增加表達力有幾種方式<br>
<br>
<br>
<br>
<br>

命名
====

適當的命名變數或函式，能讓閱讀更方便<br>
以下建議幾件需要注意的事情<br>

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
   
```C++
Complex pt = new Complex(2.0);
```
   還要好懂<br>
<br>

函式
====
以下對於函式提供增加表達能力的方式<br>
* function的參數<br>
  * 避免過多的引入參數<br>
過多的參數會造成閱讀困難<br>
在class封裝良好的情況下，應該用class底下的member來傳遞<br>
或是將要傳遞的參數用struct包裝起來<br>
  * 避免旗標參數(flag)<br>
當有flag時代表你的function一次不只想做一件事情<br>
所以會建議將它拆成2個function，如:<br>
```
rendor(bool isEven)
```
可以拆成
```
rendorForOdd()
rendorForEven()
```
  * 避免使用輸出型參數 <br>
輸出型參數容易被忽略掉，或是需要額外的精力去注意<br>
如果讓回傳值的時候不要建造副本來浪費時間
可以利用 'return shared_pointer' 或是 'return by reference'
或是讓你的輸出是在class的member上
* 避免出現多個return <br>
額外的return一樣會被吸引精力<br>
所以當你在一個大的function中想要用return來完成類似goto的功能時<br>
或許可以思考看看，要不要把function拆出更多的子function<br>
<br>

註解
====
要注意!! 註解常常用來輔助說明<br>
但是註解是需要額外的***精力***去維護他<br>
否則的話，當程式內容隨著時間不斷的更改<br>
註解卻完全沒動，後面的人在看code就會看到懷疑人生<br>
所以，應該是用良好的程式來說明意圖，而不是註解<br>
但以下還是有幾種case是適合使用註解的<br>
* 法律型註解<br>
這常常是公司方的規定，需要加就加吧<br>
* 資訊型註解<br>
有時其實程式並不是那麼方便的可以說明，這時候加入註解似乎是個不錯的選擇<br>
不過記得當有修改時不要忘記他，例如:<br>
```C++
// format of time: kk:mm:ss EEE, MMM 
log.printTime(now_time)
```
* 演算法註解<br>
有時function內部不容易看出演算法的過程<br>
或是為了做加速，讓你的方程式做變形<br>
讓算式跟原本長得不一樣了<br>
所以在這邊加入一些註解來方便閱讀<br>
* 意圖說明註解<br>
這邊的意圖說明，不是說明function內部是如何運作的<br>
而是比如說，有functionA及functionB都一樣可以達到效果<br>
那為何最後選擇functionA<br>
或是程式中間有加入某個function<br>
這是為了避免哪種corner case而增加的<br>
* 警告型註解<br>
程式中可能需要警告一下使用/不使用某個function的後果<br>
例如運算時間會大幅增加或是其他<br>
或是某個function沒有對多thread功能有足夠的保護<br>
所以先警告這個function先不要用在多thread中<br>
* 強調型註解<br>
與上兩項類似，某個不起眼的功能<br>
你可以加入註解強調他的重要性或失去它的後果<br>


+++++++++++++<br>
或是利用class及virtual function來取代，如:
```C++
class rendor{
    call_foo_A();
    call_foo_B();
    
    foo(bool isEven){
        call_foo_A(); // do something first
        
        if(isEven){
            // do something for even        
        }else{
            // do something for odd
        }
    
        call_foo_B(); // do something
    }
}
```
可以改成
```C++

```

<br>
<br>

