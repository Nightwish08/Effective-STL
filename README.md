# [《Effective STL》](https://learning.oreilly.com/library/view/effective-stl/9780321545183/)笔记
* 本书针对性地介绍标准库容器和算法的使用准则，但不是STL的参考教程，读者需要有一定STL使用经验。

## 参考
* [The C++ Reference](https://en.cppreference.com/w/cpp/header/algorithm)
* [The C++ Resources Network](http://www.cplusplus.com/reference/)
* [algorithm](https://github.com/downdemo/Effective-STL/blob/master/reference/algorithm.md)
* [numeric](https://github.com/downdemo/Effective-STL/blob/master/reference/numeric.md)
* [string类的六个查找函数](https://github.com/downdemo/Effective-STL/blob/master/reference/string%E7%B1%BB%E7%9A%84%E5%85%AD%E4%B8%AA%E6%9F%A5%E6%89%BE%E5%87%BD%E6%95%B0.md)
* [C++17 STL](https://github.com/downdemo/Effective-STL/blob/master/reference/C%2B%2B17%20STL.md)

## [1. 容器](https://github.com/downdemo/Effective-STL/blob/master/content/01%20%E5%AE%B9%E5%99%A8.md)
* 01 仔细选择你的容器
* 02 小心对“容器无关代码”的幻想
* 03 使容器里对象的拷贝操作轻量而正确
* 04 用empty来代替检查size()是否为0
* 05 尽量使用区间成员函数代替他们的单元素兄弟
* 06 警惕C++最令人恼怒的解析
* 07 当使用new得指针的容器时，记得在销毁容器钱delete那些指针
* 08 永不建立auto_ptr的容器
* 09 在删除选项中仔细选择
* 10 注意分配器的协定和约束
* 11 理解自定义分配器的正确用法
* 12 对STL容器线程安全性的期待现实一些

## [2. vector和string](https://github.com/downdemo/Effective-STL/blob/master/content/02%20vector%E5%92%8Cstring.md)
* 13 尽量使用vector和string来代替动态分配的数组
* 14 使用reserve来避免不必要的重新分配
* 15 小心string实现的多样性
* 16 如何将vector和string的数据传给遗留的API
* 17 使用“交换技巧”来修整过剩容量
* 18 避免使用vector<bool>

## [3. 关联容器](https://github.com/downdemo/Effective-STL/blob/master/content/03%20%E5%85%B3%E8%81%94%E5%AE%B9%E5%99%A8.md)
* 19 了解相等和等价的区别
* 20 为指针的关联容器指定比较类型
* 21 永远让比较函数对相等的值返回false
* 22 避免原地修改set和multiset的键
* 23 考虑用有序vector代替关联容器
* 24 当关乎效率时应该在map::operator[]和map-insert之间仔细选择
* 25 熟悉非标准散列容器

## [4. 迭代器](https://github.com/downdemo/Effective-STL/blob/master/content/04%20%E8%BF%AD%E4%BB%A3%E5%99%A8.md)
* 26 尽量用iterator代替const_iterator，reverse_iterator和const_reverse_iterator
* 27 用distance和advance把const_iterator转化成iterator
* 28 了解如何通过reverse_iterator的base得到iterator
* 29 需要一个一个字符输入时考虑使用istreambuf_iterator

## [5. 算法](https://github.com/downdemo/Effective-STL/blob/master/content/05%20%E7%AE%97%E6%B3%95.md)
* 30 确保目标区间足够大
* 31 了解你的排序选择
* 32 如果你真的想删除东西的话就在类似remove的算法后接上erase
* 33 提防在指针的容器上使用类似remove的算法
* 34 注意哪个算法需要有序区间
* 35 通过mismatch或lexicographical比较实现简单的忽略大小写字符串比较
* 36 了解copy_if的正确实现
* 37 用accumulate或for_each来统计区间

## [6. 仿函数、仿函数类、函数等](https://github.com/downdemo/Effective-STL/blob/master/content/06%20%E4%BB%BF%E5%87%BD%E6%95%B0%E3%80%81%E4%BB%BF%E5%87%BD%E6%95%B0%E7%B1%BB%E3%80%81%E5%87%BD%E6%95%B0%E7%AD%89.md)
* 38 把仿函数类设计为用于值传递
* 39 用纯函数做判断式
* 40 使仿函数类可适配
* 41 了解使用ptr_fun、mem_fun和mem_fun_ref的原因
* 42 确定less<T>表示operator<

## [7. 使用STL编程](https://github.com/downdemo/Effective-STL/blob/master/content/07%20%E4%BD%BF%E7%94%A8STL%E7%BC%96%E7%A8%8B.md)
* 43 尽量用算法调用代替手写循环
* 44 尽量用成员函数代替同名的算法
* 45 注意count、find、binary_search、lower_bound、upper_bound和equal_range的区别
* 46 考虑使用函数对象代替函数作算法的参数
* 47 避免产生只写代码
* 48 总是#include适当的头文件
* 49 学习破解有关STL的编译器诊断信息
* 50 让你自己熟悉有关STL的网站
