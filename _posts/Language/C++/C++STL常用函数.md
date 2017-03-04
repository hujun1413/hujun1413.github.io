---
title:  C++STL常用函数
date: 2016-09-26 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - C++
tags:   # 文章标签，参数可省略
    - STL
---
下面包含set string vector map 的一些最常用的基本用法
<!--more-->
## set的用法：
### 构造：
```C++
set<int> a;   //构造
int s[] = {1,2,3};
set<int> a(s,s+3);
```
### 增加：
```C++
a.insert(1);   //增加
```
### 删除：
```C++
erase(iterator)  ,删除定位器iterator指向的值
erase(first,second),删除定位器first和second之间的值
erase(key_value),删除键值key_value的值
```
### 修改元素即先删除再添加

### 遍历：
```C++
set<int>::iterator it; //定义前向迭代器
//中序遍历集合中的所有元素
for(it=a.begin();it!=a.end();it++)    //遍历
cout<<*it<<endl;
```
### 查找：
```C++
if((it = s.find(2)) != s.end())   //查找
{
cout<<*it<<endl;
}
else
{
      cout << "没找到" << 2 << endl;
}
```


## string用法：
### 构造:
```C++
string a = "456";
string a(5,'4');   //a=“44444”

char *c = "asdf";
string b(c,3);   //b = “asd”
```
### 访问下标：
   a[i]或a.at(i), 但at函数提供范围检查，当越界时会抛出out_of_range异常，下标运算符[]不提供检查访问。

### string转char[]字符串数组：
```C++
string a="123456789";
char b[20];
//strcpy(b,a.c_str());  //当a改变时，b不改变，b以'\0'结束
//strcpy(b,a.data());   // b不以'\0'结束

    int len = a.copy(b,9,0);   //从位置0开始的9个字符
    b[len] = '\0';
```
### string转char*字符串数组：
```C++
char *b = new char;   // char *b = (char*)malloc(10);
    //strcpy(b,a.c_str());  //当a改变时，b不改变，b以'\0'结束
//strcpy(b,a.data());   // b不以'\0'结束

strcpy(b,"1");
int len = a.copy(b,9,0);   //从位置0开始的9个字符
b[len] = '\0';
```
### char[]，char*转string：
```C++
char str[]="hello";
//char *str = new char;
//strcpy(str,"hello");
    //string ss1 = str;
    string ss1;
    ss1.insert(0,str);
```
### 成员函数：
```C++
int capacity()const;    //返回当前容量（即string中不必增加内存即可存放的元素个数）
int max_size()const;    //返回string对象中可存放的最大字符串的长度
int size()const;        //返回当前字符串的大小
int length()const;       //返回当前字符串的长度
bool empty()const;        //当前字符串是否为空
void resize(int len,char c);//把字符串当前大小置为len，并用字符c填充不足的部分
```
### 读取：
```C++
//接收不带空格的字符串 
    string a;
    cin >> a; //不含空格，且与cin.get()一样留下回车 
    
    //接收带空格的字符串 
  cin.get();  //将之前未读取的回车留下
    getline(cin,a);  = getline(cin,a,'\n'); //接收后不留回车
    
    读取不带空格的字符串，直到EOF
    while(cin >> a)
    {
    	if(a[0] == EOF)
        {
        	break;
        }
        cout << a << endl;
    }
```
### 赋值：
```C++
string &operator=(const string &s);//把字符串s赋给当前字符串
string &assign(const char *s);//用c类型字符串s赋值
string &assign(const char *s,int n);//用c字符串s开始的n个字符赋值
string &assign(const string &s);//把字符串s赋给当前字符串
string &assign(int n,char c);//用n个字符c赋值给当前字符串
string &assign(const string &s,int start,int n);//把字符串s中从start开始的n个字符赋给当前字符串
string &assign(const_iterator first,const_itertor last);//把first和last迭代器之间的部分赋给字符串
```
### 查找：
```C++
int find(char c, int pos = 0) const;//从pos开始查找字符c在当前字符串的位置
int find(const string &s, int pos = 0) const;//从pos开始查找字符串s在当前串中的位置
//查找成功时返回所在位置，失败返回string::npos的值 
int rfind(char c, int pos = npos) const;//从pos开始从后向前查找字符c在当前串中的位置
int rfind(const string &s,int pos = npos) const;
//从pos开始从后向前查找字符串s中前n个字符组成的字符串在当前串中的位置，成功返回所在位置，失败时返回string::npos的值 
int find_first_of(char c, int pos = 0) const;//从pos开始查找字符c第一次出现的位置
int find_first_of(const string &s,int pos = 0) const;
//从pos开始查找当前串中第一个在s的前n个字符组成的数组里的字符的位置。查找失败返回string::npos 
int find_first_not_of(char c, int pos = 0) const;
int find_first_not_of(const string &s,int pos = 0) const;
//从当前串中查找第一个不在串s中的字符出现的位置，失败返回string::npos 
int find_last_of(char c, int pos = npos) const;
int find_last_of(const string &s,int pos = npos) const; 
int find_last_not_of(char c, int pos = npos) const;
int find_last_not_of(const string &s,int pos = npos) const;
//find_last_of和find_last_not_of与find_first_of和find_first_not_of相似，只不过是从后向前查找
```
### 子串：
```C++
string substr(pos1,num);   //从pos1开始，长度为num的子串
string substr(pos);   //从pos开始到结束的子串
```
### 替换：
```C++
string &replace(int p0, int n0,const string &s);//删除从p0开始的n0个字符，然后在p0处插入串s
```
### 插入：
```C++
string &insert(int p0,const string &s);//在p0位置插入字符串s
void insert(iterator it, const_iterator first, const_iterator last);//在it处插入[first，last）之间的字符
void insert(iterator it, int n, char c);//在it处插入n个字符c
```
### 删除：
```C++
  iterator erase(iterator first, iterator last);//删除[first，last）之间的所有字符，返回删除后迭代器的位置
string &erase(int pos = 0, int n = npos);//删除pos开始的n个字符，返回修改后的字符串
```
### 迭代器：
```C++
const_iterator begin()const;
iterator begin();      //返回string的起始位置
iterator end();      //返回string的最后一个字符后面的位置
iterator rbegin();    //返回string的最后一个字符的位置
iterator rend();     //返回string第一个字符位置的前面
rbegin和rend用于从后向前的迭代访问，通过设置迭代器
string::reverse_iterator,string::const_reverse_iterator实现
```

## vector用法：
### 初始化：
```C++
vector               // 创建一个空的vector。
vector c1(c2)        // 复制一个vector
vector c(n)        // 创建一个vector，含有n个数据，数据均已缺省构造产生
vector c(n, elem)    // 创建一个含有n个elem拷贝的vector
```
### 增加：
```C++
c.push_back(elem)  // 在尾部加入一个数据。
c.insert(pos,elem)    // 在pos位置插入一个elem拷贝，传回新数据位置。
c.insert(pos,n,elem)  // 在pos位置插入n个elem数据。无返回值。
c.insert(pos,beg,end) // 在pos位置插入在[beg,end)区间的数据。无返回值。
```
### 删除：
```C++
c.pop_back()       // 删除最后一个数据。
c.erase(pos)  // 删除pos位置的数据，传回下一个数据的位置。
c.erase(beg,end)  //删除[beg,end)区间的数据，传回下一个数据的位置。
```
### 修改：
```C++
  a.at(i) = value;    //指定位置赋值
```
### 遍历：
```C++
  vector<int>::iterator it;
for(it = a.begin(); it != a.end(); it++)
{ 
     cout << (*it) << " " << (*it) << endl;
}

  for(int i=0; i<a.size(); i++)
{
     cout << a.at(i) << endl;
}
```
### 查找：
```C++
vector<int>::iterator it;
it = find(a.begin(),a.end(),2);
if(it != a.end())
{
      cout << "找到" << *it << "序号为："  << it-a.begin() << endl;
}
else
{
      cout << "没找到" << 2 << endl
}
```
### 排序：
```C++
sort(a.begin(),a.end(),comp);  //默认升序 
reverse(a.begin(),a.end()-1); //将a中除最后一个元素翻转
```
### 成员函数：
```C++
c.capacity()  // 返回容器中数据个数。
c.max_size()       // 返回容器中最大数据的数量。
c.clear()     // 移除容器中所有数据。
c.empty()     // 判断容器是否为空。
c.resize(num)      // 重新指定队列的长度。
c.reserve()        // 保留适当的容量。
c.size()           // 返回容器中实际数据的个数。
c.back()      // 传回最后一个数据，不检查这个数据是否存在。
c.begin()     // 传回迭代器中的第一个数据地址。
c.end()       // 指向迭代器中末端元素的下一个，指向一个不存在元素。
c.front()     // 传回第一个数据。
c.rbegin()         // 传回一个逆向队列的第一个数据。
c.rend()           // 传回一个逆向队列的最后一个数据的下一个位置。

c1.swap(c2)
swap(c1,c2)        // 将c1和c2元素互换。同上操作。
get_allocator // 使用构造函数返回一个拷贝。
```
## map用法：
### 构造：
```C++
map<string , int> mapstring;
```
### 增加：
```C++
map.insert(pair<sting, int> ("aclive", 2));
map.insert(map<string, int>::value_type ("aclive",2));//当键已存在时插入失败
map["April"]=112;//当键已存在时覆盖旧值，更新为新的值
```
### 删除：
```C++
map<string,int>::iterator ite;
ite = mapstring.find("hujun");

if(ite == mapstring.end())
{
   cout << "没找到hujun" << endl;
}
else
{
    cout<<ite->first<<" "<<ite->second << endl;
    mapstring.erase(ite);
}
```
### 修改：
```C++
  map<string,int>::iterator ite;
  ite = mapstring.find("hujun");

  if(ite == mapstring.end())
  {
      cout << "没找到hujun" << endl;
  }
  else
  {
      cout<<ite->first<<" "<<ite->second << endl;
      mapstring["hujun"] = 6;
  }
```
### 遍历：
```C++
for(map<string,int>::iterator it = mapstring.begin(); it != mapstring.end(); it++)
  {
      cout<<it->first<<" "<<it->second<<endl;
  }
```
### 查找：
```C++
map<string,int>::iterator ite;
  ite = mapstring.find("hujun");

  if(ite == mapstring.end())
  {
      cout << "没找到hujun" << endl;
  }
  else
  {
      cout<<ite->first<<" "<<ite->second << endl;
  }
```
### 排序：
  map中的元素是自动按key升序排序,所以不能对map用sort函数：

### 成员函数：
      begin()          返回指向map头部的迭代器
      clear(）         删除所有元素
      count()          返回指定元素出现的次数
      empty()          如果map为空则返回true
      end()            返回指向map末尾的迭代器
      equal_range()    返回特殊条目的迭代器对
      erase()          删除一个元素
      find()           查找一个元素
      get_allocator()  返回map的配置器
      insert()         插入元素
      key_comp()       返回比较元素key的函数
      lower_bound()    返回键值>=给定元素的第一个位置
      max_size()       返回可以容纳的最大元素个数
      rbegin()         返回一个指向map尾部的逆向迭代器
      rend()           返回一个指向map头部的逆向迭代器
      size()           返回map中元素的个数
      swap()            交换两个map
      upper_bound()     返回键值>给定元素的第一个位置
      value_comp()      返回比较元素value的函数