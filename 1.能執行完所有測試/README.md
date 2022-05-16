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
} catch (PortFail e){
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
以上面case為例，第二種寫法就是使用多型來取代 if/else 或 switch/case<br>
有時候用上面的寫法比較好，有時候是下面，所以每次在寫的時候都記得思考一下<br>
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
雖然理論上要完全包裝好的話，不應該在class中直接get別的class的變數拿來用<br>
但有時候沒辦法，屬於必要之惡<br>

類別
====
* 一個class應該要夠簡短，只負責一件事情<br>
符合單一職責原則(Single Responsibility Principle, SRP)<br>
```
單一職責原則: 一個類別或模組應該有一個，而且只能有一個修改的理由
```
而當一個class的成員或function使用到的變數越少，代表他越專注做一件事<br>
而耦合度就越低<br>
* 保持凝聚性<br>
凝聚性越高，代表class裡的function使用到越多的class裡的變數<br>
保持高凝聚性也可以讓class切割出很多小型的class<br>
* class之間的相依性用inferface取代<br>
例如: classA會使用到classB的funct_C<br>
那先寫一個classB的interface，並在其中先說有funct_C<br>
之後classB再實作<br>
如此一來，想要測試classA的時候，可以用classB的interface實作出classB_test<br>
使得在不改classA的情況下，很好的控制所有classA的input以做測試<br>
* 不同層次的概念不要混在一起<br>
高層次概念應該放在基底類別，接近低層次與實現細節相關的應該放在衍生類別<br>
不要在基底類別就先寫一些跟實作細節相關的function<br>
* 不要在基底類別放常數、變數<br>
想像你在一個衍生類別看到一個常數，你想找他實際上是多少<br>
結果你往回翻了3,4個類別才找到<br>
有需要的話，用靜態引入的方式<br>


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
測試驅動開發(Test-driven development,TDD)<br>
意即先寫好單元測試，再寫產品程式，流程如以下<br>
1. 寫單元測試前，不要寫產品程式<br>
2. 寫剛好可以測試出產品程式失敗的單元測試<br>
3. 寫剛好可以通過單元測試的產品程式<br>
不斷重複以上流程<br>
如此一來，單元測試與產品程式都是需要維護的東西<br>

另外如同一個function只做一件事<br>
最好一次測試一個概念<br>
再把相同概念的測試包在同一個function裡面<br>

整潔的測試應該要遵守FIRST<br>
* Fast 快速:測試應該要快速，讓你能時常使用<br>
* Independent 獨立: 測試程式應該相互獨立，不應該要先跑哪個才能跑另一個<br>
這樣會使在第一個測試失敗後，後面一連串的測試都跟著失敗<br>
* Repeatable 可重複性: 測試程式應該在任何環境都能被重複執行<br>
* Self-Validating 自我驗證: 測試程式應該在最後輸出是否成功，而不是還要手動比較記錄檔<br>
這代表應該會有一個客觀的標準判斷是否通過，而非主觀判斷<br>
* Timely 及時: 測試程式應該跟產品程式差不多時間出來，讓他能及時的被使用<br>
<br>
另外書中還有提供一些參考建議<br>

* 使用涵蓋率工具<br>
現在有涵蓋率工具，可以幫助你確認有哪邊的code還沒有被測試過<br>

* 注意邊界條件<br>
還有注意測試邊界條件，有時候程式是可以正常運作<br>
但在邊界條件時會出錯<br>

* 在錯誤的地方多測一點<br>
bugs往往會匯集再一起，多測一點你可能發現錯誤不只一個<br>

* 如何失敗也是一種啟示<br>
當你跑完單元測試發現輸入是5個字元的都失敗了，那或許可以幫助你找到錯誤<br>
<br>
