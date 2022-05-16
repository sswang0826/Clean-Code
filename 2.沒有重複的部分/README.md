如在外層所描述的class<br>
我們可以把重複的地方放在base的class<br>
再利用class的virtual function來取代，如:<br>

```C++
class rendor{
    void call_foo_A();
    void call_foo_B();
    
    void foo(bool isEven){
        call_foo_A(); // do something first
        
        if(isEven){
            fooDoSomethingForEven();      
        }else{
            fooDoSomethingForOdd();  
        }
    
        call_foo_B(); // do something
    }
}
```
可以改成<br>

```C++
class rendor{
public:
    void call_foo_A();
    void call_foo_B();
    virtual void fooDoSomething()=0;
    
    void foo(){
        call_foo_A();
        fooDoSomething();
        call_foo_B();
    }
};

class rendorEven : public rendor{
public:
    virtual void fooDoSomething() override {
        fooDoSomethingForEven();  
    }
};

class rendorOdd : public rendor{
public:
    virtual void fooDoSomething() override {
        fooDoSomethingForOdd(); 
    }
};
```
如此一來可以<br>

```
rendorEven r;
r.foo();
```
就可以達成與原本code裡面的<br>

```
foo(true);
```
一樣的效果<br>
<br>
書裡面是覺得這樣比較好，不過我個人是覺得這樣反而比較複雜啦<br>
但是還是記錄下來以供參考<br>
<br>
<br>
