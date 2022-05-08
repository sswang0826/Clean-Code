# 能執行完所有測試
<br>
模組化是對unit test很重要的方向<br>
以下提供一些模組化的建議<br>
<br>
<br>

函式
====

* 一次做一件事<br>
一次只做一件事，並把這件事情做好<br>
在一個function中做太多事，容易造成閱讀困難<br>
所以良好的將子功能切成子function，讓一個function的縮排不超過2個tab<br>
這樣讓這個function只做一件事<br>
其餘的細節在子function中完成<br>
如此也方便將各個功能切開來測試<br>
* 避免回傳錯誤碼<br>
回傳錯誤碼有點違反一次只做一件事情<br>
他同時做了下指令跟查詢動作是否完成<br>
另外也有容易出現需要巢狀結構的if，如:
```C++
if(delete(page) == E_OK){
  if(deleteReference(page) == E_OK){
    if(deleteKey(page) == E_OK){
      log("delete page success");
    }else{
      log("deleteKey fail");
    }
  }else{
    log("deleteReference fail");
  }
}else{
  log("delete fail");
}
```
可以利用例外處理來避免這件事情
```C++
void delete(Page page){
  try{
    deletePageAndReference(page);
  }catch (Exception e){
    log(e.getMessage());
  }
  return;
}
void deletePageAndReference(Page page) throws Exception{
    delete(page);
    deleteReference(page);
    deleteKey(page);
    return;
}
```

