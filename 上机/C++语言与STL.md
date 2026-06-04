# C++ 语言与 STL

## 一、竞赛基础语法

### 1.1 输入输出优化

竞赛中 cin/cout 默认较慢，因为需要与 C 的 stdio 同步。加速方法：

```cpp
ios::sync_with_stdio(false);  // 关闭与 C stdio 的同步
cin.tie(nullptr);              // 解除 cin 和 cout 的绑定
```

加速后**不能混用** cin/cout 和 scanf/printf。

如果仍然不够快，使用 scanf/printf：

```cpp
int n;
scanf("%d", &n);
printf("%d\n", n);
// 常用格式：%d(int), %lld(long long), %f(float), %lf(double), %s(字符串), %c(字符)
```

读取整行（包括空格）：

```cpp
string s;
getline(cin, s);
// 注意：在 cin >> n 之后使用 getline 前，需要先 cin.ignore() 吃掉换行符
```

### 1.2 常用类型与范围

| 类型 | 大小 | 范围 |
|------|------|------|
| int | 4 字节 | ±2.1×10⁹ |
| long long | 8 字节 | ±9.2×10¹⁸ |
| unsigned int | 4 字节 | 0 ~ 4.3×10⁹ |
| double | 8 字节 | 精度约 15-16 位有效数字 |
| bool | 1 字节 | true / false |

```cpp
// 当数据范围超过 2×10⁹ 时必须用 long long
long long x = 1LL * a * b;  // 防止中间结果溢出
// 或者
#define ll long long
```

### 1.3 Lambda 表达式

Lambda 是匿名函数，竞赛中常用于自定义排序规则：

```cpp
// 语法：[捕获](参数) -> 返回类型 { 函数体 }
sort(a.begin(), a.end(), [](int x, int y) {
    return x > y;  // 降序排序
});

// 捕获外部变量
int target = 5;
auto cmp = [&target](int x, int y) {
    return abs(x - target) < abs(y - target);
};
```

### 1.4 结构体与运算符重载

```cpp
struct Point {
    int x, y;
    // 运算符重载，用于 sort 或 set/map
    bool operator<(const Point& o) const {
        if (x != o.x) return x < o.x;
        return y < o.y;
    }
};

// 也可以写外部比较函数
bool cmp(const Point& a, const Point& b) {
    return a.x < b.x;
}
sort(points.begin(), points.end(), cmp);
```

## 二、STL 容器

### 2.1 vector（动态数组）

vector 是最常用的容器，底层是连续内存的动态数组，支持随机访问 O(1)，尾部插入/删除均摊 O(1)。

```cpp
vector<int> v;              // 空 vector
vector<int> v(n);           // n 个元素，初始值为 0
vector<int> v(n, val);      // n 个元素，初始值为 val
vector<vector<int>> g(n);   // 二维 vector（邻接表常用）

v.push_back(x);            // 尾部添加元素
v.emplace_back(x);         // 原地构造，避免拷贝，通常更快
v.pop_back();              // 删除尾部元素
v.size();                  // 元素个数（返回 size_t，无符号类型）
v.empty();                 // 是否为空
v.clear();                 // 清空
v.front();                 // 第一个元素
v.back();                  // 最后一个元素
v.resize(m);               // 改变大小
v.resize(m, val);          // 改变大小并用 val 填充新元素
v.reserve(m);              // 预分配内存（不改变 size，改变 capacity）

// 遍历
for (int i = 0; i < (int)v.size(); i++) cout << v[i];
for (int x : v) cout << x;          // 范围 for（只读）
for (int& x : v) x *= 2;           // 范围 for（修改）

// 注意：v.size() 返回无符号类型，v.size()-1 当 size 为 0 时会下溢变成巨大数
// 安全写法：for (int i = 0; i < (int)v.size(); i++)
```

### 2.2 string（字符串）

```cpp
string s = "hello";
s += " world";             // 拼接
s.substr(1, 3);            // 从位置 1 开始取 3 个字符 → "ell"
s.find("lo");              // 查找子串位置，未找到返回 string::npos
s.compare(t);              // 字典序比较
s.length();                // 等同于 s.size()

// 数字 ↔ 字符串
to_string(123);            // int → string: "123"
stoi("123");               // string → int: 123
stoll("123456789012");     // string → long long

// 字符串流（拆分、格式化）
#include <sstream>
stringstream ss("1 2 3");
int a, b, c;
ss >> a >> b >> c;         // 按空格拆分

// 字符操作
isdigit(c);   // 是否数字
isalpha(c);   // 是否字母
tolower(c);   // 转小写
toupper(c);   // 转大写
```

### 2.3 stack（栈）

后进先出 (LIFO)，底层默认用 deque 实现。

```cpp
stack<int> st;
st.push(x);     // 入栈
st.pop();        // 出栈（无返回值）
st.top();        // 栈顶元素
st.empty();      // 是否为空
st.size();       // 元素个数
```

### 2.4 queue（队列）

先进先出 (FIFO)。

```cpp
queue<int> q;
q.push(x);      // 入队
q.pop();         // 出队（无返回值）
q.front();       // 队首元素
q.back();        // 队尾元素
q.empty();
q.size();
```

### 2.5 deque（双端队列）

两端都可以 O(1) 插入和删除，也支持随机访问。

```cpp
deque<int> dq;
dq.push_front(x);   dq.push_back(x);
dq.pop_front();      dq.pop_back();
dq.front();          dq.back();
dq[i];               // 随机访问
```

### 2.6 priority_queue（优先队列/堆）

默认是**大顶堆**（最大值在顶部）。

```cpp
priority_queue<int> pq;                    // 大顶堆
priority_queue<int, vector<int>, greater<int>> pq;  // 小顶堆

pq.push(x);     // 插入 O(log n)
pq.pop();        // 删除堆顶 O(log n)
pq.top();        // 堆顶元素 O(1)
pq.empty();
pq.size();

// 自定义比较器（结构体）
struct Node {
    int dist, id;
    bool operator>(const Node& o) const { return dist > o.dist; }
};
priority_queue<Node, vector<Node>, greater<Node>> pq;  // 距离小的优先

// 或用 lambda
auto cmp = [](pair<int,int>& a, pair<int,int>& b) { return a.first > b.first; };
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

### 2.7 set / multiset（有序集合）

基于红黑树，元素自动排序，查找/插入/删除都是 O(log n)。

```cpp
set<int> s;
s.insert(x);           // 插入（已存在则不插入）
s.erase(x);            // 删除值为 x 的元素
s.erase(it);           // 删除迭代器指向的元素
s.count(x);            // 0 或 1
s.find(x);             // 返回迭代器，未找到则 s.end()
s.lower_bound(x);      // >= x 的第一个元素的迭代器
s.upper_bound(x);      // > x 的第一个元素的迭代器
s.begin(); s.end(); s.rbegin();  // 最小值: *s.begin()  最大值: *s.rbegin()
s.size(); s.empty();

multiset<int> ms;       // 允许重复元素
ms.erase(ms.find(x));  // 只删一个 x（不用 ms.erase(x)，那会删除所有 x）
```

### 2.8 map / multimap（有序映射）

基于红黑树的键值对容器，按键排序。

```cpp
map<string, int> m;
m["hello"] = 1;         // 插入或修改
m.insert({"world", 2});
m.count("hello");       // 0 或 1
m.find("hello");        // 返回迭代器
m.erase("hello");       // 按键删除

// 遍历（按键有序）
for (auto& [key, val] : m) {  // C++17 结构化绑定
    cout << key << ": " << val << endl;
}

// operator[] 的陷阱：访问不存在的键会自动插入默认值
// 只想查询时用 find() 或 count()
```

### 2.9 unordered_set / unordered_map（哈希容器）

基于哈希表，平均 O(1) 查找/插入/删除。无序。

```cpp
unordered_set<int> us;
unordered_map<int, int> um;
// 接口与 set/map 基本相同，但不支持 lower_bound/upper_bound
// 不保证遍历顺序

// 最坏情况 O(n)（哈希冲突严重时）
// 被卡哈希时可以自定义哈希函数或改用 map
```

### 2.10 pair / tuple

```cpp
pair<int, int> p = {1, 2};
// 或 make_pair(1, 2)
p.first;  p.second;

// 结构化绑定 (C++17)
auto [a, b] = p;

// pair 默认按 first 排序，first 相同按 second
vector<pair<int,int>> v;
sort(v.begin(), v.end());  // 自动按字典序

// tuple（三元组及以上）
tuple<int, int, int> t = {1, 2, 3};
auto [x, y, z] = t;  // C++17
get<0>(t);            // 访问第 0 个元素
```

### 2.11 bitset（位集）

固定大小的位数组，空间和时间都比 bool 数组高效（按 64 位一组操作）。

```cpp
bitset<1000> bs;
bs.set(i);       // 第 i 位设为 1
bs.reset(i);     // 第 i 位设为 0
bs.flip(i);      // 第 i 位取反
bs.test(i);      // 第 i 位是否为 1
bs.count();      // 1 的个数
bs.any();        // 是否有 1
bs.none();       // 是否全为 0
// 支持 &, |, ^, ~, <<, >> 运算
```

## 三、STL 算法

### 3.1 排序

```cpp
sort(a, a + n);                          // 数组排序
sort(v.begin(), v.end());                // vector 排序
sort(v.begin(), v.end(), greater<int>()); // 降序
sort(v.begin(), v.end(), [](int a, int b) { return a > b; }); // 自定义

stable_sort(v.begin(), v.end());         // 稳定排序（保持相等元素的相对顺序）
partial_sort(v.begin(), v.begin()+k, v.end()); // 前 k 小
nth_element(v.begin(), v.begin()+k, v.end());  // 第 k 小在正确位置，左右无序，O(n)
```

### 3.2 查找

```cpp
// 二分查找（要求有序）
binary_search(v.begin(), v.end(), x);     // 是否存在 x，返回 bool
lower_bound(v.begin(), v.end(), x);       // >= x 的第一个位置
upper_bound(v.begin(), v.end(), x);       // > x 的第一个位置

// 注意：返回迭代器，转下标用 it - v.begin()
int pos = lower_bound(v.begin(), v.end(), x) - v.begin();

// 线性查找
find(v.begin(), v.end(), x);              // 返回迭代器

// 统计范围内 x 的个数（有序）
int cnt = upper_bound(..., x) - lower_bound(..., x);
```

### 3.3 去重

```cpp
// 先排序，再 unique，最后 erase
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
// unique 将相邻重复元素移到末尾，返回去重后的新末尾
```

### 3.4 全排列

```cpp
sort(v.begin(), v.end());  // 必须先排序
do {
    // 处理当前排列
} while (next_permutation(v.begin(), v.end()));

// prev_permutation：上一个排列
```

### 3.5 极值

```cpp
min(a, b);  max(a, b);
min({a, b, c, d});  max({a, b, c, d});  // C++11 initializer_list
min_element(v.begin(), v.end());  // 返回最小值的迭代器
max_element(v.begin(), v.end());
```

### 3.6 累加与前缀和

```cpp
#include <numeric>
accumulate(v.begin(), v.end(), 0);       // 求和，初值为 0
accumulate(v.begin(), v.end(), 0LL);     // long long 求和
partial_sum(v.begin(), v.end(), s.begin()); // 前缀和
```

### 3.7 填充与生成

```cpp
fill(v.begin(), v.end(), 0);              // 全部填 0
iota(v.begin(), v.end(), 1);              // 填入 1, 2, 3, ...（常用于初始化下标数组）
```

### 3.8 其他常用

```cpp
reverse(v.begin(), v.end());              // 反转
rotate(v.begin(), v.begin()+k, v.end());  // 左旋 k 位
count(v.begin(), v.end(), x);             // 统计 x 出现次数
```

## 四、常用技巧

### 4.1 快速读入

当数据量极大（>10⁶）时，即使是 scanf 也可能不够快，可以用手写快读：

```cpp
inline int read() {
    int x = 0, f = 1; char c = getchar();
    while (c < '0' || c > '9') { if (c == '-') f = -1; c = getchar(); }
    while (c >= '0' && c <= '9') { x = x * 10 + c - '0'; c = getchar(); }
    return x * f;
}
```

### 4.2 memset 初始化

```cpp
memset(a, 0, sizeof a);      // 全部设为 0
memset(a, -1, sizeof a);     // 全部设为 -1（每个字节 0xFF → int 为 -1）
memset(a, 0x3f, sizeof a);   // 每个 int 变为 0x3f3f3f3f ≈ 1.06×10⁹，常用作 INF
// 0x3f3f3f3f 的好处：两个相加不会溢出 int
```

### 4.3 常量定义

```cpp
const int INF = 0x3f3f3f3f;
const long long LINF = 0x3f3f3f3f3f3f3f3fLL;
const int MOD = 1e9 + 7;
const double EPS = 1e-8;
const double PI = acos(-1.0);
```

### 4.4 常用数学函数

```cpp
abs(x);           // 绝对值（整数用 abs，浮点用 fabs）
ceil(x);          // 上取整
floor(x);         // 下取整
round(x);         // 四舍五入
sqrt(x);          // 平方根
pow(x, y);        // x 的 y 次方（浮点，整数幂建议用快速幂）
log(x);           // 自然对数
log2(x);          // 以 2 为底

__gcd(a, b);      // 最大公约数（GCC 内建）
__builtin_popcount(x);    // x 的二进制中 1 的个数（32位）
__builtin_popcountll(x);  // 64 位版本
__builtin_ctz(x);         // 末尾 0 的个数
__builtin_clz(x);         // 前导 0 的个数
```

### 4.5 位运算

```cpp
x & 1           // 判断奇偶（1 为奇）
x >> k          // 除以 2^k
x << k          // 乘以 2^k
x & (x - 1)    // 去掉最低位的 1
x & (-x)        // 取最低位的 1（lowbit）
x | (1 << k)   // 将第 k 位设为 1
x & ~(1 << k)  // 将第 k 位设为 0
x ^ (1 << k)   // 翻转第 k 位
(x >> k) & 1   // 取第 k 位的值
```

## 五、竞赛代码模板

### 5.1 万能头文件

```cpp
#include <bits/stdc++.h>  // GCC 专用，包含所有标准库头文件
using namespace std;

typedef long long ll;
typedef pair<int, int> pii;
typedef vector<int> vi;

const int N = 200010;
const int INF = 0x3f3f3f3f;
const int MOD = 1e9 + 7;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    // ...

    return 0;
}
```

### 5.2 多组输入

```cpp
// 方式1：给定 T 组
int T;
cin >> T;
while (T--) {
    // 处理一组
}

// 方式2：读到 EOF
int n;
while (cin >> n) {
    // 处理一组
}

// 方式3：读到特殊值结束
int n;
while (cin >> n && n != 0) {
    // 处理一组
}
```

### 5.3 图的建法

```cpp
// 方法1：vector 邻接表（最常用）
vector<int> adj[N];  // 无权图
adj[u].push_back(v);

vector<pair<int,int>> adj[N];  // 带权图 (目标节点, 权重)
adj[u].push_back({v, w});

// 方法2：链式前向星（常数小，竞赛常用）
int head[N], nxt[M], to[M], wt[M], cnt = 0;
void add(int u, int v, int w) {
    to[++cnt] = v;
    wt[cnt] = w;
    nxt[cnt] = head[u];
    head[u] = cnt;
}
// 遍历 u 的邻接点
for (int i = head[u]; i; i = nxt[i]) {
    int v = to[i], w = wt[i];
}
```

## 六、C++11/14/17 实用特性

### 6.1 auto 与范围 for

```cpp
auto x = 42;            // 自动推导为 int
auto it = m.begin();    // 自动推导迭代器类型

for (auto& x : v) { }   // 范围 for，引用可修改
for (const auto& [k, v] : m) { }  // map 遍历 (C++17)
```

### 6.2 结构化绑定 (C++17)

```cpp
auto [a, b] = make_pair(1, 2);
auto [x, y, z] = make_tuple(1, 2, 3);
for (auto& [key, val] : mymap) { }
```

### 6.3 emplace_back vs push_back

```cpp
vector<pair<int,int>> v;
v.push_back({1, 2});     // 先构造 pair，再拷贝/移动到 vector
v.emplace_back(1, 2);    // 直接在 vector 内部构造，少一次拷贝
// 对于简单类型差别不大，对于复杂对象 emplace_back 更高效
```

### 6.4 初始化列表

```cpp
vector<int> v = {1, 2, 3, 4, 5};
map<string, int> m = {{"a", 1}, {"b", 2}};
return {a, b};  // 返回 pair 或 tuple
min({a, b, c, d});  // 多元素取最小
```
