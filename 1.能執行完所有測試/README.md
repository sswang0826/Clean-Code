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
可以利用例外處理來避免這件事情<br>
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
不過也不要到處都用例外處理<br>
否則要從內層throw到外層，萬一更改會需要改很多地方<br>
另外，如果使用到第三方api，並且這api也會throw error<br>
那建議用class把api包一層起來，讓外層在call時不用管有哪些error項<br>
如此一來，萬一api做更改了就不用在實際call api的地方改動(只改LocalPort內層)<br>
對外層而言還是做一樣的事情<br>
```C++
LocalPort port = new LocalPort();
try{
  port.open();
} cache (PortFail e){
  reportError(e);
  log(e.getMessage);
}

class LocalPort{
  private ApiPort port_instance;
  
  LocalPort(){
    port_instance = new ApiPort();
  }
  
  void open(){
    try{
      port_instance.open();
    } catch (ErrorType1 e){
      throw new PortFail(e);
    } catch (ErrorType2 e){
      throw new PortFail(e);
    } catch (ErrorType3 e){
      throw new PortFail(e);
    }
  }
}
```
<br>
另外注意，建議不用回傳/傳遞null，這會讓程式內部到處都是需要做做額外的檢查<br>
如果真的有需要，回傳一個空的vector之類的都比null好
<br>

物件與資料結構
=============
包裝起來是測試的第一步<br>
而包裝成物件是一個將資料抽象化的過程<br>
例如透過以下包裝後，你不需要在意是用直角坐標系或是及坐標系來記錄<br>

```C++
class point{
  float getX();
  float getY();
  float getR();
  float getTheta();
  virtual void doSomeThing();
}
```
或是有2個class都是繼承point這個class<br>
在外面可以直接call doSomeThing()而不用在意實際的運作方式<br>
<br>
<br>
class及structure是相對立的存在<br>
可以參考以下<br>
```C++
struct Rectangle{
  float height;
  float width;
}

struct Circle{
  float radius;
}

float area(object shape){
  if(shape instanceof Rectangle){
    Rectangle r = (Rectangle)shape;
    return r.height * r.width;
  }else if(shape instanceof Circle){
    Circle c = (Circle)shape;
    return PI * c.radius* c.radius;
  }
}
```
與
```C++
struct Rectangle{
  float height;
  float width;
  float area(){
    return height * width;
  }
}

struct Circle{
  float radius;
  float area(){
    return PI * radius* radius;
  }
}
```
在以上2段程式中，上面的是以structure導向，下面是以class導向<br>
可以看到<br>
structure導向可以方便增加新的function(如:area())而不用改變原本結構<br>
class導向可以方便增加新的物件(如:Rectangle、Circle)而不用改變原本結構<br>
所以兩者是相對立的，不要讓一個東西同時又是structure導向，又是class導向<br>
<br>
* 避免火車事故(train wreck)<br>
火車事故是以下這段程式<br>
```
output_dir = obj.get_options().get_scratch_dir().get_absolute_path()
```
可以用拆開來避免
```
opt = obj.get_options()
scratch_dir = opt.get_scratch_dir()
output_dir = scratch_dir.get_absolute_path()
```
或是用class的function來傳遞
```
output_dir = obj.get_absolute_path_of_scratch_dir()
```
<br>

邊界
====
邊界指的是自己於他人的程式之間連接的地方<br>
也是屬於我們unit test接口的地方<br>
* 建議可以對他人做邊界測試，以確保結果有符合預期<br>
  邊界測試也可以留著，當別人的code有更新時可以再次確認<br>
* 建議可以把他人的介面外再包一層class<br>
  以確保他人api改動時，要做的工不會太多<br>
  也可以在別人還沒完成時<br>
  讓class預留接口，讓自己的進度先繼續前進<br>
<br>

單元測試
========



