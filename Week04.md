## bind() and function() 

### function example

```cpp
//保存普通函数
void print(int a)
{
    cout<<a<<endl;
}
std::function<void(int a)> func;
func=print;
func(1);
```

```cpp
//保存lambda表达式
std::function<void()> func_1=[](){cout<<"hello world"<<endl;}
func_1();//hello world

```

```cpp
//保存成员函数
class Foo{
    Foo(int num):num_(num){}
    void print(int i) const {cout<<i+num_<<endl;}
};

std::function<void(const Foo&,int)> func=&Foo::print;
Foo foo(2);
func(foo,1);
```
实际应用中可以使用auto关键字

********************

### bind example

```cpp
class A{
    public:
    	void fun1(int k,int m){
            cout<<k<<"	"<<m<<endl;
        }
}
void fun2(int x,int y,int z)
 {
     cout<<x<<"		"<<y<<"		"<<z<<endl;
 }
void fun3(int &a,int &b)
{
    a++;
    b++;
    cout<<a<<"		"<<b<<endl;
}

auto f1=std::bind(fun2,1,2,3);
f1();//x=1,y=2,z=3

auto f2 =std::bind(fun2,placeholders::_1,placeholders::_2,3);
f2(1,2);//x=1,y=2,z=3

auto f3 =std::bind(fun2,placeholders::_2,placeholders::_1,3);
f3(2,1);//x=1,y=2,z=3

int m=2, n=3;

auto f4=std::bind(fun3,placeholders::_1,n);
f4(m);//m=3,n=4

cout<<"m=	"<<m<<endl;//m=3
cout<<"n=	"<<n<<endl;//n=3
A a;
auto f5=std::bind(&A::fun1,a,placeholders::_1,placeholders::_2);
f5(10.20);//k=10,m=20

std::function<void(int,int)>func=std::bind(&A::fun1,a,std::placeholders::_1,std::placeholders::_2);
func(10,20);//k=10,m=20

         
```
