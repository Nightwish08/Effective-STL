* 查找string s中某个字符x出现次数
```cpp
count(s.begin(), s.end(), x);
```
* string子串
```cpp
string s("hello world");
string s2 = s.substr(0, 5); // 从位置0开始的5个字符的拷贝，hello
string s3 = s.substr(6); // world
size_t pos = str.find("world"); // pos = 6
string s7 = str.substr(pos); // world
```
* 对string使用find返回size_t而非迭代器，对string使用erase参数可以不是迭代器
```cpp
string s("123456");
auto pos1 = s.find("2");
auto pos2 = s.find("5");
s.erase(pos1, pos2 - pos1);
cout << s; // 156
```
* remove-erase删除指定区间内的某个元素
```cpp
string s("do.wn.de.mo@qq.com");
auto pos = s.find("@");
s.erase(remove(s.begin(), s.begin() + pos, '.'), s.begin() + pos);
cout << s; // downdemo@qq.com
```
* 迭代器删除元素不要在for循环中自加，或者直接用while
```cpp
auto it = v.begin();
while(it != v.end())
{
    if (*it == 42) it = v.erase(it);
    else ++it;
}
```
* string转小写
```cpp
for(auto& x : s) x = tolower(x);
```
* set和map插入元素
```cpp
s.insert(tmp);
s.insert(v.begin(), v.end());

m.insert(make_pair(key, value));
```
* sort按奇偶排序
```cpp
sort(v.begin(), v.end(), [](int i, int j){ return i%2 < j%2;});
```
* 用与操作和移位判断二进制数n中1的个数
```cpp
while(n)
{
    if(n & 1) ++count;
    n >>= 1; // 注意不是n>>1
}
```
* vector下标用ASCII码做映射
```cpp
vector['y' - 'a']; // 差值计算索引

vector<int> v(100, 0);
for (auto x : str) v[x]++; // 字符串中每个字母出现次数
```
* 翻转容器
```cpp
reverse(v.begin(), v.end());
```
* 双向列表list的插入与删除（以下操作也适用于deque），注意不能用下标访问
```cpp
list<int> v;
for(int i = 0; i < 10; ++i) v.push_back(i); // push_front插入到首元素
v.pop_back(); // 移除尾元素
v.pop_front(); // 移除首元素 
v.back(); // 尾元素
v.front(); // 首元素
```
