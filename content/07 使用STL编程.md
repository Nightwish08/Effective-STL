> ## 43 尽量用算法调用代替手写循环
* 很多要用循环来实现的任务可以改用算法来实现，算法内部也包含一个循环
* 使用算法有三个原因：高效、正确、可维护（直观易读）

> ## 44 尽量用成员函数代替同名的算法
* 有些容器拥有和STL算法同名的成员函数，关联容器提供了count、find、lower_bound、upper_bound和
equal_range，list提供了remove、remove_if、unique、sort、merge和reverse
* 尽量用成员函数代替算法，一是成员函数更快，二是比器算法它们与容器结合得更好（尤其是关联容器）
```cpp
set<int> s; // 建立set，放入1,000,000个数据
...
set<int>::iterator i = s.find(727); // 使用find成员函数
if (i != s.end()) ...
set<int>::iterator i = find(s.begin(), s.end(), 727); // 使用find算法
if (i != s.end()) ...
```
* find成员函数花费对数时间，不管727是否存在于此set中，set::find查找不超过40次比较，平均大约20次。find算法运行花费线性时间，如果727不在set中则需要执行1,000,000次比较，在此set中平均需要执行500,000次比较
* 算法判断两个对象相同基于相等，而关联容器基于等价。 因此，find算法搜索727用的是相等，而find成员函数用的是等价。这一差别对map和multimap尤其明显，它们的成员函数只关心key，比如count成员函数只统计key值匹配的元素数，而count算法的寻找基于相等和对象对的全部组成部分
* 每个被list作了特化的算法（remove、remove_if、unique、sort、merge和reverse）都要拷贝对象，而list的特化版本没有拷贝，只是简单地操纵连接list的节点的指针。算法和成员函数的算法复杂度是相同的，但如果操纵指针比拷贝对象的代价小的话，list的版本应该提供更好的性能
* list成员函数的行为和同名算法的行为经常不相同，比如list的remove、remove_if和unique成员函数真正删除了元素，不需要再调用erase。sort算法不能用于list，但list有sort成员函数

> ## 45 注意count、find、binary_search、lower_bound、upper_bound和equal_range的区别
* 把count用来作为是否存在的检查。count返回零或者一个正数，可以把非零转化为true而把零转化为false
```cpp
list<Widget> lw; // Widget的list
Widget w;
if (count(lw.begin(), lw.end(), w) != 0) ...
```
* 使用find则难懂一些，但效率高，因为find找到匹配目标后就停止了，count会继续搜索
```cpp
if (find(lw.begin(), lw.end(), w) != lw.end()) ... // 找到了
```
* count和find都是线性时间的，而有序区间的搜索算法（binary_search、lower_bound、upper_bound和equal_range）是对数时间的，对于有序区间应当使用后者，另外前者基于相等，后者基于等价
* 要测试有序区间中是否存在一个值，使用binary_search
* lower_bound查找一个值的第一个拷贝，它返回一个迭代器，如果找到，迭代器指向这个值的第一个拷贝，否则返回可以插入这个值的位置。必须测试lower_bound的结果所标示出的对象是不是目标值
```cpp
vector<Widget>::iterator i = lower_bound(vw.begin(), vw.end(), w);
if (i != vw.end() && *i == w) ...
```
* 但lower_bound搜索用的是等价，大多数情况下等价和相等的结果是一样的，但如果想完全完成，必须检测lower_bound指向的对象值是否和目标值等价
* 一个更好的方法是使用equal_range。equal_range返回一对迭代器，第一个等于lower_bound返回的迭代器，第二个等于upper_bound返回的，如果两个迭代器相同就说明没找到。equal_range只用等价，所以总是正确的
```cpp
vector<Widget> vw;
...
sort(vw.begin(), vw.end());
typedef vector<Widget>::iterator VWIter;
typedef pair<VWIter, VWIter> VWIterPair;
VWIterPair p = equal_range(vw.begin(), vw.end(), w);
if (p.first != p.second) ... // 找到了
```
* 对equal_range返回的两个迭代器作distance就等于区间中对象的数目，完成了计数
```cpp
VWIterPair p = equal_range(vw.begin(), vw.end(), w);
cout << "There are " << distance(p.first, p.second)
<< " elements in vw equivalent to w.";
```
* 有时要在区间中寻找一个位置，比如有一个Timestamp类和一个Timestamp的vector，它按照老的timestamp放在前面的方法排序
```cpp
class Timestamp { ... };
bool operator<(const Timestamp& lhs, const Timestamp& rhs);
vector<Timestamp> vt;
...
sort(vt.begin(), vt.end());
```
* 现在有一个特殊的timestamp对象ageLimit，要删除所有比ageLimit老的timestamp，首先要找到第一个不比ageLimit更老的元素的位置，使用lower_bound
```cpp
Timestamp ageLimit;
...
vt.erase(vt.begin(), lower_bound(vt.begin(), vt.end(), ageLimit));
```
* 如果要删除所有至少和ageLimit一样老的timestamp，即要找到第一个比ageLimit年轻的timestamp的位置，使用upper_bound
```cpp
vt.erase(vt.begin(), upper_bound(vt.begin(), vt.end(), ageLimit));
```
* 要把东西插入一个有序区间，对象的插入位置是在有序的等价关系下它应该在的地方时，upper_bound也很有用
```cpp
class Person {
public:
    ...
    const string& name() const;
    ...
};

struct PersonNameLess : public binary_function<Person, Person, bool> {
    bool operator()(const Person& lhs, const Person& rhs) const
    {
        return lhs.name() < rhs.name();
    }
};

list<Person> lp;
...
lp.sort(PersonNameLess());
```
* 要保持list顺序，用upper_bound来指定插入位置
```cpp
Person newPerson;
...
lp.insert(upper_bound(lp.begin(), lp.end(), newPerson, PersonNameLess()), newPerson);
```
* binary_search算法没有提供对应的成员函数，要测试在set或map中是否存在某个值，使用count成员函数
```cpp
set<Widget> s;
...
Widget w;
...
if (s.count(w)) ... // 找到了
```
* 要测试某个值在multiset或multimap中是否存在，find往往比count好，因为count要测试每个对象，但用count给关联容器计数比调用equal_range然后distance更快也更简单清晰

|目标|无序区间算法|有序区间算法|set或map的成员函数|multiset或multimap的成员函数|
|:-:|:-:|:-:|:-:|:-:|
|值是否存在|find|binary_search|count|find|
|值是否存在，第一个等于这个值的对象在哪里|find|equal_range|find|find或lower_bound|
|第一个不在期望值之前的对象在哪里|find_if|lower_bound|lower_bound|lower_bound|
|第一个在期望值之后的对象在哪里|find_if|upper_bound|upper_bound|upper_bound|
|有多少对象等于期望值|count|equal_range再distance count|count|count|
|等于期望值的所有对象在哪里|find（迭代）|equal_range|equal_range|equal_range|

> ## 46 考虑使用函数对象代替函数作算法的参数
* 把函数对象传递给算法所产生的代码一般比传递函数高效，比如要降序排序一个double的vector，最直接的STL方式是通过sort算法和greater<double>类型的函数对象实现
```cpp
vector<double> v;
...
sort(v.begin(), v.end(), greater<double>());
```
* 可能你会使用内联函数来减少调用开销
```cpp
inline
bool doubleGreater(double d1, double d2)
{
    return dl > d2;
}
...
sort(v.begin(), v.end(), doubleGreater);
```
* 但两者相比，用函数对象的那个更快，因为greater<double>::operator()是一个内联函数，编译器在实例化sort时内联展开，于是sort没有包含一次函数调用，而且编译器可以对这个没有调用操作的代码进行优化
* 而把函数作为参数时，编译器把函数转化为一个指向那个函数的指针，传递的是这个指针，因此第二个sort调用传了一个doubleGreater的指针，sort模板实例化时，声明为
```cpp
void sort(vector<double>::iterator first,
    vector<double>::iterator last, 
    bool (*comp)(double, double));
```
* 因为comp是一个指向函数的指针，每次在sort中用到时，编译器产生一个通过指针的间接函数调用，编译器不会试图去内联通过函数指针调用的函数
* 把函数指针作为参数会抑制内联也解释了C++的sort实际上总比C的qsort快的原因
* 用函数对象代替函数还可以避免细微的语言陷阱
```cpp
// 返回两个浮点数的平均值
template<typename FPType>
FPTypeaverage(FPType val1, FPType val2)
{
    return (val1 + val2) / 2;
}
// 把两个序列的成对的平均值写入一个流
template<typename InputIter1, typename InputIter2>
void writeAverages(InputIter1 begin1, InputIter1 end1, InputIter2 begin2, ostream& s)
{
    transform(begin1, end1, begin2,
        ostream_iterator<typename iterator_traits
            <lnputIter1>::value_type> (s, "\n"),
        average<typename iteraror_traits
            <InputIter1>::value_type>
    );
}
```
* 有可能存在带有一个类型参数的函数模板叫做average，average<typename iterator_traits<lnputIter1>::value_type>将存在二义性，解决方法是建立一个函数对象
```cpp
template<typename FPType>
struct Average : public binary_function<FPType, FPType, FPType> {
FPType operator()(FPType val1. FPType val2) const
{
    return average(val 1 , val2);
}
template<typename InputIter1, typename InputIter2>
void writeAverages(lnputIter1 begin1, InputIter1 end1,
    InputIter2 begin2, ostream& s)
{
    transform(begin1, end1, begin2,
        ostream_iterator<typename iterator_traits
            <lnputIter1>::value_type>(s, "\n"),
        Average<typename iterator_traits
            <InputIter1>::value_type>());
}
```

> ## 47 避免产生只写代码
* 代码的读比写更经常，所以写代码时需要考虑可读性，比如有一个vector<int>，要删掉最后一个不小于y的元素之后的小于x的元素
```cpp
v.erase(remove_if(
    find_if(v.rbegin(), v.rend(), bind2nd(greater_equal<int>(), y)).base(),
    v.end(),
    bind2nd(less<int>(), x)),
    v.end());
```
* 上述代码比较难以阅读，需要一层层拆分才能看懂
```cpp
find_if(v.rbegin(), v.rend(), bind2nd(greater_equal<int>(), y)
// 从后往前找到不小于y的数。假设找不到满足的元素，返回尾后迭代器v.rend()
v.rend().base()
// 相当于v.begin()
v.erase(std::remove_if(v.begin(), v.end(), std::bind2nd(std::less<int>(), x))
```
* 上述代码一般被称为只写（write-only）代码，很容易写但很难读和理解，代码是否只写依赖于读它的人。要让上述代码更好理解，应该分解成几个块
```cpp
typedef vector<int>::iterator VecIntIter;
VecIntIter rangeBegin = find_if(v.rbegin(), v.rend(),
    bind2nd(greater_equal<int>(), y)).base();
v.erase(remove_if(rangeBegin, v.end(), bind2nd(less<int>(), x)), v.end());
```

> ## 48 总是#include适当的头文件
* 容器几乎都在同名的头文件中，例外的是<set>声明了set和multiset，<map>声明了map和multimap
* 所有的算法都在<algorithm>和<numeric>中声明
* 特殊的迭代器，包括istream_iterators和istreambuf_iterators，在<iterator>中声明
* 标准仿函数（比如less<T>）和仿函数适配器（如not1、bind2nd）在<functional>中声明

> ## 49 学习破解有关STL的编译器诊断信息
* 定义一个特定大小的vector是合法的
```cpp
vector<int> v(10);
```
* 于是想到用同样的方法定义string
```cpp
string s(10);
```
* 这是错误的，报错如下
`errorC2664:'std::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string(std::initialzer_list<_Elem>,const std::allocator<char> &)' : cannot convert argument 1 from 'int' to 'const std::basic_string<char,std::char_traits<char>,std::allocator<char>> &'`
* string不是一个类，实际上是这个的typedef
```cpp
basic_string<char, char_traits<char>, allocator<char>>
```
* 通常诊断信息会明确指出basic_string在std中
```cpp
std::basic_string<char, std::char_traits<char>, std::allocator<char>>
```
* 如果把上述完整写法用string替换，就很容易识别错误信息了
`errorC2664:'string::string(const class std::allocator<char> &)':' : cannot convert argument 1 from 'int' to 'const std::basic_string<char,std::char_traits<char>,std::allocator<char>> &'`
> ## 50 让你自己熟悉有关STL的网站
* [cppreference](https://en.cppreference.com/w/%E9%A6%96%E9%A1%B5)
* [cplusplus.com](http://www.cplusplus.com/reference/)
* [STLport](http://www.stlport.org/)
* [Boost](https://www.boost.org/)
