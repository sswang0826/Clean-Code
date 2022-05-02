# Clean Code筆記

以下為在閱讀無瑕的程式碼(Clean Code, Robert C. Martin)後<br>
自己整理的筆記，方便日後快速複習
<br>
<br>
<br>

clean code目的及目標
-------------
由於在持續寫程式的過程中會不斷的新增功能<br>
使的此程式逐漸變的雜亂<br>
在毫無維護的情況下，讀code及改code的代價會越來越大<br>
因此為了降低代價的發生<br>
每次在新加的功能能正常跑之後，需要***額外***多花時間維護整潔<br>
以下可就4個方面討論
* [能執行完所有測試](#能執行完所有測試)
* [沒有重複的部分](#沒有重複的部分)
* [具表達力](#具表達力)
* [最小化class與function的數量](#最小化class與function的數量)
<br>

能執行完所有測試
-------------
能完成目標功能為首要目的，所以在這邊排在第一點<br>
而完成功能後，當然需要確認有沒有執行正確<br>
測試方法最好對每個部分unit test<br>
所以就會需要有模組化，將每個部份的相依性降到最低<br>
讓每個模組的輸入輸出可以替換成假的測試替身<br>
以全面測試單個模組有無問題<br>
<br>
藉由以上確保所有程式都能完成測項後<br>
就能確認之後為了整潔而做的程式改動不會影響到結果<br>
更細節內容可以參考這裡<br>
<br>

沒有重複的部分
-------------
重複的程式通常會代表額外的工作及風險<br>
可以想像一下<br>
```C++
class bank{
  void CheckJuniorEmployee(employee junior_staff){
    //  do something...
    if(junior_staff.IsPay){
      junior_staff.CalculatePay();
      junior_staff.DiliverPay();
    }
  }

  void CheckSeniorEmployee(employee senior_staff){
    //  do something else...
    if(senior_staff.IsPay){
      senior_staff.CalculatePay();
      senior_staff.DiliverPay();
    }
  }
}
```
當IsPay裡面有需要做任何額外的改動的時候<br>
你會需要修改不同function或是你會***漏掉***去修改某一個function<br>
所以在此書中建議單一職責原則(Single responsibility principle)<br>
```
單一職責原則: 一個function或一個class都應該只有一個職責
             如此一來他在需要被修改時穎該也只有一個理由
```
另外以上例子除了將
```C++
if(senior_staff.IsPay){
  senior_staff.CalculatePay();
  senior_staff.DiliverPay();
}
```
這段重複的的藏到employee的class裡面之外<br>
或許也能考慮建一個employee class<br>
然後建額外的SeniorEmployee及JuniorEmployee的class來繼承employee<br>
更細節內容可以參考這裡<br>
<br>

具表達力
-------------
<br>
<br>

最小化class與function的數量
-------------
<br>
<br>



