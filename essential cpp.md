《essential c++》[源码](https://ptgmedia.pearsoncmg.com/images/0201485184/sourcecode/non_windows.zip)

## 1.2 对象的定义与初始化

“构造函数语法”初始化变量

```cpp
int number(0);
```
## 1.5 如何运用Array和Vector
数组初始化vector

```cpp
int arr[5] = {1, 2, 3, 4, 5};
//way 1
vector vec(arr, arr+5);
vector vec(arr, arr+arr.size()) //error 暂时不知道原因
//way 2
vector vec(begin(arr), end(arr));
```
## 1.7 文件的读写
读写操作
```cpp
#include <fstream>
ofstream outfile("test.txt". ios_base::app) //追加模式append mode，文件位置位于末尾
outfile << "something"  << endl;
```
读取操作

```cpp
ifstream infile("test.txt")
string content;
while(infile >> content)
{
	cout << content ; // 按行按空格划分内容
}
```
同时读写文件

```cpp
fstream iofile("test.txt", ios_base::in|ios_base::app); //追加模式，文件位置位于末尾
iofile.seekg(0); //重新定位到文件起始处（末尾）
```
## 2.1 如何编写函数
终止整个程序

```cpp
#include <cstdlib>
exit(-1); //极端做法， 第7章异常（exception）
```
## 2.4 使用局部静态对象
函数内局部对象，每次函数结束时会被弃置。
对象定义在file scope内，会打乱不同函数间的独立性，使得难以理解

```cpp
void foo()
{
	static vector<int> elems; //局部静态对象，在其他函数调用过程中，依旧持续存在
}
```
## 2.5 声明inline函数
inline函数，编译器对该函数调用操作改为以一份函数代码副本代替，性能改善
必须将inline函数的定义放在头文件
**适合体积小，常被调用**

## 2.7 定义并使用函数模板

```cpp
template <typename T>
void foo( T msg)
{ /*函数主体*/ }
```
## 2.8 函数指针
函数作为参数传入其他函数，简单示例（和书上不一样）

```cpp
int add1(int &num)
{
    num += 1;
    return num;
}
int add2(int &num)
{
    num += 2;
    return num;
}
int foo(int i, int (*ptrAdd)(int&))
{
    ptrAdd(i);
    return i;
}
/*** （1）函数指针调用函数 ***/
int (*ptr)(int&) = 0;
ptr = add1;
int num = 1;
ptr(num); // =2 注意不能传常量，因为函数定义的引用类型，所以引入一个变量num
ptr(num); // =3

/***（2）调用 add1和add2函数作为指针传入调用函数***/
//way 1 便于理解，另一种初始化方式
int (*ptr)(int&) = 0;
ptr = &add1;
foo(1, ptr); // =2

//way 2 简洁
foo(1, add1); // =2

foo(1, add2); // =3
```
##  2.9 设定头文件
函数的定义只有一份，可以有许多声明。不把函数的定义放在.h头文件里，因为同一个程序的多个代码问价可能都会含入这个头文件。
**inline定义必须置于头文件**
**头文件声明，cpp文件定义。**
```cpp
const vector<int> (*foo)(int );			//定义
extern const vector<int> (*foo)(int );	//声明
```
## 3.1 指针的算术运算
**可以通过指针访问array的元素，也可以通过下标运算符**。文中的69页最后代码困惑了很久，还是基础不好。

```cpp
int* find( int *array, int size, const int &value)
{
    if(! array || size < 1) return 0;
    for (int ix = 0; ix < size; ++ix)
    {
        if (array[ix] == value) // array[ix] = *(array+ix)
            return &array[ix];
    }
    return 0;
}
int array[] = {1,2,3,4,5};
find(array, 5, 5);
```
传入的是array的第一个元素的指针，指针可以使用下标运算符（subscript）来访问元素。
**下标操作就是将array的起始地址加上索引值，产生出某个元素的地址，然后解引用（dereference）返回元素值**

可以写出一个模板函数（如find）同时处理array和vector，但是**指针的连续运算不适用于list**，因此不能用一个函数实现三者的同一功能。iterator解决了
## 3.2 理解Iterator
在底层指针的行为之上提供一层抽象，取代程序原本的“指针直接操作”（STL库）。利用Iterator读取下一个元素或者上一个元素，可以实现一个函数同时处理array、vector和list （**泛型算法**）。Iterator的实现文中没有提供。

Iterator的实现依赖了“==”相等运算符，如果底层元素并未提供这个运算符，或者赋予了相等运算符不同的意义，就稍显不足了。可以通过传入函数指针取代原有的相等运算符，或者运用所谓的function object。
## 3.3 所有容器的共同操作
 <font color=red size=4  font="monospace">
equality（==） inequality（！=） assignment（=）
empty、size、clear、begin、end、insert、erase</font>

## 3.4 使用顺序性容器
**vector、list、deque**
- vector：连续内存、随机访问颇有效率、插入或删除非末端元素缺乏效率
- list：双向链表非连续内存、随机访问效率低、插入和删除中间元素效率高
- deque：（和vector类似）连续内存、两端端插入和删除效率更高（比vector）。标准库queue以deque实现

- 同：都有 <font color=red size=4  font="monospace">push_back </font>和 <font color=red size=4  font="monospace"> pop_back</font> ， <font color=red size=4  font="monospace">front()</font> 和  <font color=red size=4  font="monospace">back()</font> 读取前端和末端元素、 <font color=red size=4  face="monospace">insert、erase</font>
- 不同：list 和 deque 有  <font color=red size=4  font="monospace">push_front</font> 和  <font color=red size=4  font="monospace">pop_front</font>

**insert四种变形：**

```cpp
1、iterator insert(iterator position, elemType value); //返回iterator指向插入元素，插入到position之前/
2、void insert（iterator position, int count, elemType value）;//position之前插如count个value
3、void insert(iterator1 position, iterator2 fisrt, iterator2 last); //position之前插入first到last的元素
4、iterator insert(iterator position); //position之前插入默认所属类型初始值
```
**erase两种变形**

```cpp
1、iterator erase(iterator posit); 					//删除posit元素
2、iterator erase(iterator first, iterator last); 	//删除范围内元素
```

## 3.5使用泛型算法

```cpp
#include <algorithm>

1、find()			用于搜索无序集合
2、binary_search()	用于搜索有序集合，二分查找
3、count()			返回数值相符的元素数目
4、search()			比对容器内是否存在某个子序列，返回子序列起始处，无则指向容器末尾
5、*max_element() 	返回范围内的最大值
6、copy()			复制一个到另外一个容器
```
## 3.6 如何设计一个泛型算法
给定一个vector进行不同比较操作，返回新vector。
利用函数指针实现如下：
```cpp
vector<int> filter(const vector<int> &vec, 		
					int filter_value,	
					bool (*pred) (int, int));
bool less_than(int v1, int v2){return v1 < v2 ? true:false;}
bool greater_than(int v1, int v2){return v1 > v2 ? true:false;}

find(vec.begin(), vec.end(), value);  //返回找到value的Iterator
```
**Function Object**
标准库预先定义好的<font color=red size=4  face="monospace">function object</font>，是某种<font color=red size=4  face="monospace">class</font>的实例对象，对运算符重载，可当做一般函数来使用，为了效率。
标准库定义的一组<font color=red size=4  face="monospace">function object</font>

<font color=red size=4  face="monospace">#include &lt;functional&gt;</font>
- 六个算术运算  <font color=red size=4  face="Monospace">plus<>, minus<>, negate<>, multiplies<>, divides<>, modules<></font>
- 六个关系运算  <font color=red size=4  face="monospace">less<>, less_equal<>, greater<>, greater_equal<>, equal_to<>, not_equal_to<></font>
- 三个逻辑运算  <font color=red size=4  face="monospace">logical_and&lt;type&gt;, logical_or&lt;type&gt;, logical_not&lt;type&gt;</font>

例如，对降序排序，不用自己定义第三个参数的函数（如上面的 <font color=red size=4  face="monospace">greater_than</font>函数），调库就行：

```cpp
sort(vec.begin(), vec.end(), greater<int>() );
```
另外还有<font color=red size=4  face="monospace">transform()</font>泛型算法，没咋介绍，没看懂，日后用到补充

**Function Object Adapter**
功能扩展，对<font color=red size=4  face="monospace">function object</font>进行修改操作，使用<font color=red size=4  face="monospace">binder adapter</font>将<font color=red size=4  face="monospace">function object</font>
的参数绑定为固定值。
<font color=red size=4  face="monospace">bind1st</font> 将指定值绑定第一个参数
<font color=red size=4  face="monospace">bind2nd</font> 将指定值绑定第二个参数
例如：

```cpp
less<int> lt;
auto iter = vec.begin();
for(;iter != vec.end(); ++iter)
{
    cout << *find_if(iter, vec.end(), bind2nd(lt, 41)) << endl; //返回vector中小于41的值
}
note：
bind2nd把41绑定到less<int>的第二个参数上，less会把每个元素和41做对比。
```

<font color=red size=4  face="monospace">negator</font>对<font color=red size=4  face="monospace">function object</font>的真伪值取反
- <font color=red size=4  face="monospace">not1</font> 对<font color=red size=4  face="monospace">unary function object</font> 的真伪值取反（会覆盖原始值，没啥卵用啊==）
- <font color=red size=4  face="monospace">not2</font> 对<font color=red size=4  face="monospace">binary function object</font> 的真位置取反

## 3.7 使用Map
任何一个key值只会在map中存在一份，多份要是用<font color=red size=4  face="monospace">multimap</font>
```cpp
#include <map>
map<string, int> words; //string是key， int是value
```
三种查询map中是否存在key


 1. key当索引，不存在时，会自动添加入map，value=0
```cpp
int count = 0;
if(!(count = words["viewer"]))
```
 2. <font color=red size=4  face="monospace">find()</font>函数

```cpp
auto iter = words.find("viewer");
```

 3. <font color=red size=4  face="monospace">count()</font>函数

```cpp
if(words.count("viewer"))
	value = words["viewer"];
```
## 3.9 如何使用Iterator Inserter
- <font color=red size=4  face="monospace">back_inserter()</font> 传入其参数（容器本身）

```cpp
vector<int> result_vec;
unique_copy(ivec.begin(), ivec.end(), back_inserter(result_vec));
```

-  <font color=red size=4  face="monospace">inserter()</font> insert替代assignment运算符，两个参数，容器和起点

```cpp
vector<string> svec_res;
unique_copy(svec.begin(), svec.end(), inserter(svec_res, svec_res.end()));
```
-  <font color=red size=4  face="monospace">front_inserter()</font> 以push_front代替assignment运算符，适用于list和deque

```cpp
list<int> ilist_clone;
copy(vec.begin(), vec.end(), front_inserter(ilist_clone)); //复制vector(1,2,3,4) list=4,3,2,1
```
上述三种adapter都不能用在array中，不支持插入操作

## 3.10 使用iostream Iterator
任务：输入一串string，存到vector，一般方法
```cpp
string word;
vector<string> text;
int count = 5;
while(count--)
{
    cin >> word;
    text.push_back(word);
}
```
利用iostream Iterator类

```cpp
istream_iterator<string> is(cin); 		//绑定至cin标准输入设备， first
istream_iterator<string> eof;			//end of file，代表last
vector<string> text;
copy (is, eof, back_inserter(text));	// 无法结束cin的循环？？？？enter tap space都没用
sort (text.begin(), text.end());

ostream_iterator<string> os(cout, " "); // 绑定输出流，类型为string，“ ”是C-style字符串，也可以是字符串常量
copy (text.begin(), text.end(), os);
```
甚至，加入文件的输入输出流，将<font color=red size=4  face="monospace">stream_iterator</font>绑定对应的文件流即可

```cpp
istream infile("a.txt");
ostream outfile("b.txt");
istream_iterator<string> is(infile);
ostream_iterator<string> os(outfile);
```
这些迭代器感觉目前没啥用啊==



## 4.1 如何实现一个Class
- 作者的编码习惯是class内的data member之前加下划线

```cpp
vector<string> _stack;
```
- 所有menber function必须在class主体内声明，一般在主体外定义。如果在主体内定义，会被自动视为inline函数

## 4.2 构造函数和析构函数
无
## 4.3 何谓可变（mutable）和不变（const）

 1. <font color=color font=monospace size=4> const </font>
- 保证不会更改调用者，标注const告诉编译器。const声明和定义要同步。如果一个const成员函数，但是修改了成员变量，编译错误。

```cpp
class Triangular{
public:
    int length() const {return _length;} //inline
    int elem(int pos) const;
private:
    int _length;
    static vector<int> _elem;
};
int Triangular::elem(int pos) const     //const声明和定义同步
{
    return _elem[pos-1];
}
```
- 以下虽然没有修改成员变量，但是返回了非常量的引用，向其他函数公开了私有变量，变得可修改，会产生问题（虽然语法层面正确）

```cpp
class Triangular{
public:
    int& length() const {return _length;}  //书上例子是这个，反正我编译不过==
private:
    int _length = 5;
};
```
修改之后的，非const的对象调用非const的length()，能修改对象内容， const的对象调用const的length()，不能修改对象内容。
```cpp
class Triangular{
public:
    int& length() {return _length;}
    const int& length() const {return _length;}
private:
    int _length = 5;
};
int main(){
    const Triangular t1;    
    int num1 = t1.length();
    num1 = 6;
    cout << t1.length() << endl; // 5
    
    Triangular t2;
    int &num2 = t2.length();
    num2 = 6;
    cout << t2.length() << endl; //6
    return 0;
}
```
总之，不要用一个非const的引用函数公开调用非const成员。（一般人也不会用引用调私有变量吧=.=）

 2. <font color=color font=monospace size=4> mutable </font>
 - 对变量标示为mutable，对变量所做的改变不会破坏对象的常量性


```cpp
mutable int _length;
int length() const {return _length++};

const Triangular train;
train.length(); //不加mutable报错，加之后可以
```
## 4.4 this指针
无
## 4.5 静态类成员
类中的静态类成员，可以被同类的其他对象访问，访问时要加域作用符，类似全局对象。在class外定义时也无需加static关键字

```cpp
class A{
private：
	static int num;
	static int foo();
}；
int A::foo(){}
int main(){
	int num = A::num;	//不用声明对象 A a1;
	int foo = A::foo();
}
```
## 4.6 打造一个Iterator Class
主要是重载运算符，实现过程就不阐述了

```cpp
class Triangular_iterator
{
public:
    Triangular_iterator(int index): index(index-1) {}
    bool operator ==(const Triangular_iterator&) const;
    bool operator !=(const Triangular_iterator&) const;
    int operator*() const;
    Triangular_iterator& operator++();
    Triangular_iterator  operator++(int);
private:
    void check_integrity() const;
    int _index;
};
```
## 4.7 友元函数friend
非成员函数，在类中声明friend，就可以有成员函数的访问权限。

## 4.9 实现一个function object

```cpp
class LessThan
{
public:
    LessThan(int val):_val(val){}
    int comp_val() const {return _val;}
    void comp_val(int nval) {_val = nval;}

    bool operator() (int value) const {return value < _val;}  //重载！！
private:
    int _val;
};
int main(){
    vector<int> vec = {1,2,3,4,5,6};
    LessThan lt(5);
    for (auto iter = vec.begin(); iter != vec.end(); ++iter)
    {
        if(lt(*iter))
            cout << *iter << ","; // 1,2,3,4,
    }
    return 0;
}
```
## 4.10 重载iostream运算符
无
## 4.11 指向成员函数的指针
没看懂 后续看

# 5 面向对象编程风格

## 5.1 面向对象编程概念

- 继承

  父类和子类，父类定义所有子类的共通的公有接口和私有实现，子类可以增加和override（覆盖或重载）继承的东西

  父类=基类，子类=派生类

  | 基类    | libMat    |            |         |
  | ------- | --------- | ---------- | ------- |
  | 派生类0 | Book      | Films      |         |
  | 派生类1 | AudioBook | RentalBook | CDIBook |

- 多态

  非面向对象编程中，<font color=red font=monospace size=4>mat.print()</font> 属于**静态绑定**

  在面向对象编程总，<font color=red font=monospace size=4>print()</font> 编译器会在实际调用中找出到底是哪一个派生类调用，属于**动态绑定**

## 5.2 漫游：面向对象编程思维

示例三层类体系：LibMat ——Book——AudioBook

默认情形下，成员函数的解析都是在编译时静态地进行，<font color=red font=monospace size=4>virtual</font>关键词使得可以动态进行。也就是，不加virtual，在全局print函数里，其他派生类调用的<font color=red font=monospace size=4>mat.print()</font>是基类的，加了virtual，其他派生类调用的<font color=red font=monospace size=4>mat.print()</font>是自己类的，实现覆盖基类

```cpp
class LibMat {
public:
    LibMat(){
        cout << "0 LibMat()\n";
    }

    virtual ~LibMat(){
        cout << "0 ~LibMat()\n";
    }

    virtual void print() const {
        cout << "0 print()\n";
    }
};
```

```cpp
class Book : public LibMat {
public:
    Book( const string &title, const string &author )
        : _title( title ), _author( author ){
        cout << "1 Book::Book( " << _title << ", " << _author << " )  constructor\n";
    }
    ~Book(){
        cout << "1 ~Book()\n";
    }
    virtual void print() const {
        cout << "1 Book::print()\n";
    }

    const string& title() const { return _title; }
    const string& author() const { return _author; }

protected:
    string _title;
    string _author;
};
```

```cpp
class AudioBook : public Book {
public:
    AudioBook( const string &title,
               const string &author, const string &narrator )
        : Book( title, author ), _narrator( narrator ){
        cout << "2 AudioBook::AudioBook( " << _title << ", " << _author << ", " << _narrator << " )  constructor\n";
    }

    ~AudioBook(){
        cout << "2 ~AudioBook()\n";
    }

    virtual void print() const {
        cout << "2 AudioBook::print()\n";
    }

    const string& narrator() const { return _narrator; }

protected:
    string _narrator;
};

```

定义一个全局print函数

```cpp
void print( const LibMat &mat )
{
    cout << "IN GLOBAL PRINT()\n";
    mat.print();
}
```

调用：

```cpp
int main()
{
        LibMat m;
        print( m );
		cout << endl;
    
        Book b( "The Castle", "Franz Kafka" );
        print( b );
		cout << endl;
    
        AudioBook ab( "Man Without Qualities", "Robert Musil", "Kenneth Meyer" );
        print( ab );
    	cout << endl;

    return 0;
}
```

输出：**析构是所有程序执行完之后再释放的，先进后出**

```
0 LibMat()
IN GLOBAL PRINT()
0 print()

0 LibMat()
1 Book::Book( The Castle, Franz Kafka )  constructor
IN GLOBAL PRINT()
1 Book::print()

0 LibMat()
1 Book::Book( Man Without Qualities, Robert Musil )  constructor
2 AudioBook::AudioBook( Man Without Qualities, Robert Musil, Kenneth Meyer )  constructor
IN GLOBAL PRINT()
2 AudioBook::print()

2 ~AudioBook()
1 ~Book()
0 ~LibMat()
1 ~Book()
0 ~LibMat()
0 ~LibMat()
```

## 5.4 定义一个抽象基类

- **静态成员函数无法被声明为虚函数**
- **基类之外不需要用到就设为private，即使是派生类也无法访问，因为派生类继承的是public**
- **访问层级protected，可以让派生类访问，但不允许一般程序使用。**
- **虚函数赋值为0， 表明它为一个纯虚函数，含有纯虚函数是抽象基类**
- **一般规则，凡是基类定义了虚函数，析构函数要声明为virtual，但不建议声明为纯虚函数，空白定义即可**



## 5.5 定义一个派生类

- 必须为从基类继承下来的每个纯虚函数提供对应的实现
- 基类的指针或引用无法访问派生类的非virtual成员
- 基类和派生类中同名的非virtual函数，不太好 



<font color=red font=monospace size=4>（反正书上的代码我编译不通过，不知道为啥，书上的函数定义看的头疼，自相矛盾，明明前面说的const加在函数是不能改变class的成员的，gen_elems函数又会改变vector的数值，去掉const就编译过了，真坑）</font>

```cpp
class num_sequence{
public:
    num_sequence(){}
    ~num_sequence(){}
    virtual int         elem(int pos)  = 0;
    virtual const char* what_am_i() const = 0;
    static int         max_elems(){return _max_elems;}
    virtual ostream&    print(ostream &os = cout)  = 0;


    virtual void        gen_elems(int pos)  = 0;       		//generater生成
    bool                check_integrity(int pos) const;     //有效位置
    const static int    _max_elems = 1024;

};
bool num_sequence::check_integrity(int pos) const
{
    if(pos <= 0 || pos > _max_elems)
    {
        cerr << "error: invalid positon!\n";
        return false;
    }
    return true;
}

/*--------------------------------------------------------------------------------------------------------------------*/
class Fibonacci: public num_sequence {
public:
    Fibonacci(int len = 1, int beg_pos = 1):_length(len), _beg_pos(beg_pos){}
    ~Fibonacci(){}
    virtual int         elem(int pos) ;
    virtual void        gen_elems(int pos) ;
    virtual ostream&    print(ostream &os) ;
    virtual const char* what_am_i() const {return "Fibonacci";}
    int                 length() const {return _length;}
    int                 beg_pos() const {return _beg_pos;}


    vector<int> _elems;
    int _length;
    int _beg_pos;

};
int Fibonacci::elem(int pos)
{
    if(!check_integrity(pos)) return 0;
    if(pos > _elems.size()) Fibonacci::gen_elems(pos);
    return _elems[pos-1];
}
void Fibonacci::gen_elems(int pos)
{
    if (_elems.empty())
    {
        _elems.push_back( 1 );
        _elems.push_back( 1 );
    }
    if (_elems.size() <= pos)
    {
        int ix = _elems.size();
        int n_2 = _elems[ix - 2];
        int n_1 = _elems[ix - 1];
        for ( ; ix <= pos; ix++)
        {
            int elem = n_2 + n_1;
            _elems.push_back(elem);
            n_2 = n_1;
            n_1 = elem;
        }
    }
}

ostream& Fibonacci::print(ostream &os)
{
    int elem_pos = _beg_pos - 1;
    int end_pos = elem_pos + _length;
    if ( end_pos > _elems.size())
        Fibonacci::gen_elems(end_pos);
    while(elem_pos < end_pos)
        os << _elems[elem_pos++] << ' ';
    return os;
}
```



## 5.9 在派生类中定义一个虚函数

- 派生类override基类的函数时，一定要和基类函数一模一样，尤其是返回类型和const等修饰词，
- 派生类override基类的某个虚函数时，不一定要加上关键字virtual。编译器会判断

## 5.10 运行时的类型鉴定机制 

- <font color=red font=monospace size=4>typeid</font>运算符，<font color=red font=monospace size=4>Run-Time Type Identification（RTTI）</font>机制的一部分，查询多态化的类指针和类引用
- <font color=red font=monospace size=4>type_info</font>支持相等和不等比较操作
- <font color=red font=monospace size=4>dynamic_cast</font>也是RTTI，会进行类型鉴定，优于static_cast

```cpp
#include <typeinfo>
class AA{
public:
    AA(){}
    ~AA(){}
    virtual void print() const {cout << typeid(*this).name() << endl;}
};

class BB: public AA{
public:
    BB(){}
    ~BB(){}
    virtual void print() const {cout << typeid(*this).name() << endl;}
};
```

```cpp
    BB bb;
    bb.print(); 
    AA *aa = &bb;
    if (typeid(*aa) == typeid(BB))
        cout << "yes!" << endl;
```



# 6 以template进行编程

template=被参数化的类型

本章以二叉树的所有实现过程为例，实现<font color=red font=monospace size=3>insert，remove，find、clear、print</font>（前中后序）

两个类：

- BTnode 存储节点实值、节点实值的重复次数、左右子节点的指针
- BinaryTree 仅仅声明一个BTnode指针，指向根节点

## 6.1 被参数化的类型

实现BTnode的模板（部分），valType相当于占位符，可以取代任意类型的东西

```cpp
template <typename valType>
class BTnode; //前置声明
template <typename valType>
class BTnode
{
public:
    friend class BinaryTree<valType>;
    BTnode();
    ~BTnode();

private:
    string _val;
    int _cnt;
    BTnode *_lchild;
    BTnode *_rchild;
};
```

## 6.2 Class Template 的定义

6.1相当于声明。BinaryTree的部分定义如下：

```cpp
 template <typename elemType>
class BinaryTree{
public:
    BinaryTree();
    BinaryTree(const BinaryTree&);
    ~BinaryTree();
    BinaryTree& operator=(const BinaryTree&);
    
    bool empty() { return _root = 0;}
    void clear();
private:
    BTnode<elemType> *_root;
    
    void copy(BTnode<elemType> *tar, Btnode<elemType> *src);
       
};
```

BinaryTree<‘elemType>进行类型限定，在::域作用符后，其后的所有东西都视为在class范围内。

```cpp
template <typename elemType>
inline BinaryTree<elemType>::BinaryTree(): //构造函数在域作用符后，视为class范围内，不需要再类型限定
    _root(nullptr) {}

template <typename elemType>
inline BinaryTree<elemType>::BinaryTree(const BinaryTree &rhs)
{
    copy(_root, rhs._root);
}
template <typename elemType>
inline BinaryTree<elemType>::~BinaryTree()
{
    clear();
}

template <typename elemType>
inline BinaryTree& BinaryTree<elemType>::operator =(const BinaryTree& rhs)
{
    if (this != &rhs)
    {
        clear();
        copy(_root, rhs._root);
    }
    return *this;
}
```

## 6.3 Tempalte类型参数的处理

总结就是

- 函数体的参数 <font color=red font=monospace size=3>以传引用(&reference)方式传递</font> 比 以传值(by value)的方式传递 更有效率
- 构造函数初始化列表初始化成员参数更有效率，成员是class时能保证效率最佳，相对于在构造函数体内初始化。（其他博客关于初始化列表有讲，函数体内初始化的话会先赋值给一个临时变量，不必要的开销）

## 6.4 实现一个Class Template

## 6.5 一个以Function Template完成的Output运算符

具体实现二叉树的细节，略



## 6.6 常量表达式与默认参数值

typename部分可以使用常量表达式作为template参数。

```cpp
template<int len>
class num_sequence{
public:
    num_sequence(int beg_pos=1);
};

num_sequence< 16 > fib1;
num_sequence< 16 > fib2(17);
```

## 6.8 成员模板函数

单独定义类中的函数为模板，在类里面声明template即可，但是定义要在同文件下实现，不然会出现未定义的报错。

见[【问题记录】开发遇到的一些问题](https://blog.csdn.net/weixin_43152152/article/details/109309540)

# 7 异常处理

有点泛泛而谈，随便整理了点

C++ 异常处理涉及到三个关键字：**try、catch、throw**。

- **throw:** 当问题出现时，程序会抛出一个异常。这是通过使用 **throw** 关键字来完成的。

- **catch:** 在您想要处理问题的地方，通过异常处理程序捕获异常。**catch** 关键字用于捕获异常。

- **try:** **try** 块中的代码标识将被激活的特定异常。它后面通常跟着一个或多个 catch 块。

  

1. 捕获异常：catch 类型或对象
2. 抛出异常：throw任意对象

```cpp
try
{
   // 觉得有问题的代码放进来
}catch( ExceptionName e1 )
{
   // 收到有问题的对象
}
catch( ... ) //所有异常
{}
throw 43;
throw foo();
throw "some";
```

菜鸟教程的示例

```cpp
#include <iostream>
using namespace std;
 
double division(int a, int b)
{
   if( b == 0 )
   {
      throw "Division by zero condition!";
   }
   return (a/b);
}
 
int main ()
{
   int x = 50;
   int y = 0;
   double z = 0;
 
   try {
     z = division(x, y);
     cout << z << endl;
   }catch (const char* msg) { //msg类型由throw抛出的类型而定，或者写...全部接受也行
     cerr << msg << endl;
   }
 
   return 0;
}
```

```
g++ main.cpp即可
输出：Division by zero condition!

（不知道为啥我cmake写的不能正常输出，编译出来是这个=.= ）
terminate called after throwing an instance of 'int'
Aborted (core dumped)
...干 我测试的时候把throw改成了 throw b了，难怪抛出int错误，所以如下：

```

**当函数的try块发生某个异常，但没有相应的catch子句捕获它时，函数运行便会中断，由系统自带的异常处理机制接管，简言之：throw之后要catch（自己写的话）**

- 如果new表达式无法分配到足够的内存，会抛出**<font color=red font=monospace size=4>bad_alloc</font>**异常对象(一个class，派生于exception基类)

