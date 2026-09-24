## 核心知识点总结

### 2.1 线性表的逻辑结构

#### 1. 线性表的定义

- 线性表是 \( n \ge 0 \) 个数据元素的有限序列，记作：
  \[
  (a_1, a_2, \dots, a_n)
  \]
- \( a_i \) 是线性表中的数据元素，\( n \) 是线性表长度。
- \( a_i \) 称为 \( a_{i+1} \) 的直接前驱，\( a_{i+1} \) 称为 \( a_i \) 的直接后继。

#### 2. 线性表的特点

- 除第一个元素外，其他每一个元素有一个且仅有一个直接前驱。
- 除最后一个元素外，其他每一个元素有一个且仅有一个直接后继。
- 逻辑上相邻的元素，在逻辑关系上具有前驱和后继关系。

#### 3. 抽象数据类型线性表 ADT List

**数据对象：**
\[
D = \{a_i \mid a_i \in ElemSet, i = 1, 2, \dots, n, n \ge 0\}
\]
- \( n \) 为线性表的表长；
- \( n = 0 \) 时为空表。

**数据关系：**
\[
R = \{ <a_{i-1}, a_i> \mid a_{i-1}, a_i \in D, i = 2, \dots, n \}
\]
- \( i \) 为 \( a_i \) 在线性表中的位序。

**基本操作分类：**
- 结构初始化操作
- 结构销毁操作
- 引用型操作
- 加工型操作

#### 4. 线性表的数据成员

```cpp
ElemType *elems; // 元素存储空间
int maxSize;     // 最大元素个数
int count;       // 元素个数
```

#### 5. 线性表的基本操作

| 操作 | 说明 |
|---|---|
| `SqList()` | 构造一个空的线性表 |
| `~SqList()` | 销毁线性表 |
| `bool Empty() const` | 线性表判空 |
| `int Length() const` | 返回线性表中元素个数 |
| `bool GetElem(int position, ElemType &e) const` | 求线性表中某个数据元素 |
| `void Traverse(void (*visit)(const ElemType &)) const` | 遍历线性表 |
| `void Clear()` | 将线性表置空 |
| `bool SetElem(int position, const ElemType &e)` | 改变指定数据元素的值 |
| `bool Delete(int position, ElemType &e)` | 删除指定数据元素，并用 e 返回其值 |
| `bool Delete(int position)` | 删除指定位置元素 |
| `bool Insert(int position, const ElemType &e)` | 在指定位置插入数据元素 |

---

### 2.2 线性表的顺序存储结构

#### 1. 顺序表的定义和特点

- **定义**：将线性表中的元素相继存放在一个连续的存储空间中。
- **特点**：
  - 可利用一维数组描述存储结构；
  - 采用顺序存储方式；
  - 逻辑相邻的元素物理位置相邻；
  - 可以随机存取任意一个数据元素；
  - 插入和删除需要移动大量数据。

#### 2. 顺序表类模板定义

```cpp
template <class ElemType>
class SqList {
protected:
    int count;          // 元素个数
    int maxSize;        // 顺序表最大元素个数
    ElemType *elems;    // 元素存储空间

public:
    SqList(int size = DEFAULT_SIZE);
    virtual ~SqList();
    int Length() const;
    bool Empty() const;
    void Clear();
    void Traverse(void (*visit)(const ElemType &)) const;
    bool GetElem(int position, ElemType &e) const;
    bool SetElem(int position, const ElemType &e);
    bool Delete(int position, ElemType &e);
    bool Delete(int position);
    bool Insert(int position, const ElemType &e);
    SqList(const SqList<ElemType> &source);
    SqList<ElemType> &operator=(const SqList<ElemType> &source);
};
```

#### 3. 顺序表部分操作的实现

**构造：**
```cpp
template <class ElemType>
SqList<ElemType>::SqList(int size) {
    maxSize = size;
    elems = new ElemType[maxSize];
    count = 0;
}
```

**析构：**
```cpp
template <class ElemType>
SqList<ElemType>::~SqList() {
    delete [] elems;
}
```

**插入：**
```cpp
template <class ElemType>
bool SqList<ElemType>::Insert(int position, const ElemType &e) {
    if (count == maxSize) return false; // 判满
    if (position < 1 || position > Length() + 1) return false; // 判位置范围
    int length = Length();
    ElemType temElem;
    count++;
    for (int temPos = length; temPos >= position; temPos--) {
        GetElem(temPos, temElem);
        SetElem(temPos + 1, temElem);
    }
    SetElem(position, e);
    return true;
}
```

**删除：**
```cpp
template <class ElemType>
bool SqList<ElemType>::Delete(int position, ElemType &e) {
    if (position < 1 || position > Length()) return false;
    GetElem(position, e);
    ElemType temElem;
    for (int temPos = position + 1; temPos <= Length(); temPos++) {
        GetElem(temPos, temElem);
        SetElem(temPos - 1, temElem);
    }
    count--;
    return true;
}
```

#### 4. 插入和删除的时间复杂度

- 在第 \( i \) 个元素前插入：
  \[
  p_i = \frac{1}{n+1}, \quad \sum_{i=1}^{n} p_i (n-i+1) = \frac{n}{2}
  \]
- 删除第 \( i \) 个元素：
  \[
  q_i = \frac{1}{n}, \quad \sum_{i=1}^{n} q_i (n-i) = \frac{n-1}{2}
  \]
- 时间复杂度：\( O(n) \)

#### 5. 顺序表的空间管理

- **静态空间管理**：
  - 上溢：`elems[]` 不足以存放所有元素；
  - 下溢：`elems[]` 中元素寥寥无几，浪费空间。
- **动态空间管理**：
  - 装填因子：\( \lambda = count / maxSize \)
  - 当 \( \lambda \ge Thresh \) 时触发扩容；
  - 扩容策略：容量加倍，复制原数据，释放原空间。

```cpp
template <class ElemType>
void SqList<ElemType>::Expand() {
    if (count < maxSize) return;
    ElemType* oldElemS = elemS;
    elemS = new ElemType[2 * maxSize];
    CopyFrom(oldElemS, 0, maxSize);
    delete [] oldElemS;
}
```

**思考：**
- 为何采用容量加倍策略？
- 为何装满了才扩容？是否可以选择其他 \( \lambda \)？

#### 6. 顺序表的应用

**例2.1 求集合 A 和 B 的差集 C = A - B**

```cpp
template <class ElemType>
void Difference(const SqList<ElemType> &la,
                const SqList<ElemType> &lb,
                SqList<ElemType> &lc) {
    ElemType aElem, bElem;
    lc.Clear();
    for (int aPos = 1; aPos <= la.Length(); aPos++) {
        la.GetElem(aPos, aElem);
        bool isExist = false;
        for (int bPos = 1; bPos <= lb.Length(); bPos++) {
            lb.GetElem(bPos, bElem);
            if (aElem == bElem) {
                isExist = true;
                break;
            }
        }
        if (!isExist) {
            lc.Insert(lc.Length() + 1, aElem);
        }
    }
}
```

**补例：有序顺序表去重**

算法1：
```cpp
template <class ElemType>
int Uniquify() {
    int oldSize = count;
    int i = 1;
    while (i < count)
        elems[i-1] == elems[i] ? Delete(i) : i++;
    return oldSize - count;
}
```

算法2：
```cpp
template <class ElemType>
int Uniquify() {
    int i = 1, j = 1;
    while (++j < count)
        if (elemS[i] != elemS[j])
            elemS[++i] = elemS[j];
    count = ++i;
    return j - i;
}
```

---

### 2.3 线性表的链式存储结构

#### 1. 单链表

- **概念**：用一组地址任意的存储单元存放线性表中的数据元素。
- **结点结构**：`data` + `next`
- **特点**：
  - 逻辑关系由结点中的指针成分表示；
  - 元素的存储位置由其直接前驱的指针成分指示；
  - 是非随机存取的；
  - 尾结点的直接后继为空。

**结点类模板：**
```cpp
template <class ElemType>
struct Node {
    ElemType data;
    Node<ElemType> *next;
    Node();
    Node(ElemType e, Node<ElemType> *link = NULL);
};
```

**简单线性链表类模板：**
```cpp
template <class ElemType>
class SimpleLinkList {
protected:
    Node<ElemType> *head;
    Node<ElemType> *GetElemPtr(int position) const;
public:
    SimpleLinkList();
    virtual ~SimpleLinkList();
    int Length() const;
    bool Empty() const;
    void Clear();
    void Traverse(void (*Visit)(ElemType &));
    bool GetElem(int position, ElemType &e) const;
    bool SetElem(int position, const ElemType &e);
    bool Delete(int position, ElemType &e);
    bool Delete(int position);
    bool Insert(int position, const ElemType &e);
    SimpleLinkList(const SimpleLinkList<ElemType> &source);
    SimpleLinkList<ElemType> &operator=(const SimpleLinkList<ElemType> &source);
};
```

**插入：**
```cpp
newPtr = new Node<ElemType>(e, temPtr->next);
temPtr->next = newPtr;
```

**删除：**
```cpp
nextPtr = temPtr->next;
temPtr->next = nextPtr->next;
delete nextPtr;
```

**思考：** 在单链表的前端和末尾的插入和删除，复杂度相同吗？

#### 2. 循环链表

- **概念**：单链表的变形，最后一个结点的 `link` 指针不为 `NULL`，而是指向表的前端。
- **特点**：
  - 只要知道表中某一结点的地址，就可搜寻到所有其他结点的地址；
  - 为简化操作，往往加入表头结点。

**循环链表类模板：**
```cpp
template <class ElemType>
class SimpleCircLinkList {
protected:
    Node<ElemType> *head;
    Node<ElemType> *GetElemPtr(int position) const;
public:
    SimpleCircLinkList();
    ~SimpleCircLinkList();
    int Length() const;
    bool Empty() const;
    void Clear();
    void Traverse(void (*Visit)(ElemType &));
    bool GetElem(int position, ElemType &e) const;
    bool SetElem(int position, const ElemType &e);
    bool Delete(int position, ElemType &e);
    bool Insert(int position, const ElemType &e);
    SimpleCircLinkList(const SimpleCircLinkList<ElemType> &copy);
    SimpleCircLinkList<ElemType> &operator=(const SimpleCircLinkList<ElemType> &copy);
};
```

**构造函数：**
```cpp
template <class ElemType>
CircLinkList<ElemType>::CircLinkList() {
    head = new Node<ElemType>;
    head->next = head;
    curPtr = head;
    curPosition = 0;
    count = 0;
}
```

#### 3. 双向链表

- **概念**：在前驱和后继方向都能遍历的线性链表。
- **结点结构**：`lLink` + `data` + `rLink`
- **特点**：
  - 通常采用带表头结点的循环链表形式；
  - 满足：`p == p->lLink->rLink == p->rLink->lLink`

#### 4. 循环链表的应用——约瑟夫问题

**问题描述：**
n 个人围成一个圆圈，首先第 1 个人从 1 开始一个人一个人顺时针报数，报到第 m 个人，令其出列。然后再从下一个人开始，从 1 顺时针报数，报到第 m 个人，再令其出列，……如此下去，直到圆圈中只剩一个人为止。此人即为优胜者。

**解法：**
```cpp
void Josephus(int n, int m) {
    SimpleCircLinkList<int> la;
    int pos = 0;
    int out, winner;
    for (int k = 1; k <= n; k++)
        la.Insert(k, k);
    for (int i = 1; i < n; i++) {
        for (int j = 1; j <= m; j++) {
            pos++;
            if (pos > la.Length()) pos = 1;
        }
        la.Delete(pos--, out);
        cout << "出列者:" << out << " ";
    }
    la.GetElem(1, winner);
    cout << endl << "优胜者:" << winner << endl;
}
```

#### 5. 在链表结构中保存当前位置和元素个数

**线性链表类模板：**
```cpp
template <class ElemType>
class LinkList {
protected:
    Node<ElemType> *head;
    mutable int curPosition;
    mutable Node<ElemType> *curPtr;
    int count;
    Node<ElemType> *GetElemPtr(int position) const;
public:
    LinkList();
    virtual ~LinkList();
    int Length() const;
    bool Empty() const;
    void Clear();
    void Traverse(void (*Visit)(ElemType &)) const;
    bool GetElem(int position, ElemType &e) const;
    bool SetElem(int position, const ElemType &e);
    bool Delete(int position, ElemType &e);
    bool Delete(int position);
    bool Insert(int position, const ElemType &e);
    LinkList(const LinkList<ElemType> &copy);
    LinkList<ElemType> &operator=(const LinkList<ElemType> &copy);
};
```

---

## 三、考点归纳

### 1. 线性表的逻辑结构

- 线性表的定义与特点；
- 直接前驱与直接后继的概念；
- ADT List 的数据对象与数据关系；
- 线性表基本操作的分类与功能。

### 2. 顺序存储结构

- 顺序表的定义与特点；
- 顺序表类模板的数据成员与成员函数；
- 插入、删除操作的实现与时间复杂度分析；
- 顺序表的空间管理：上溢、下溢、装填因子、动态扩容；
- 顺序表应用：集合差运算、有序顺序表去重。

### 3. 链式存储结构

- 单链表、循环链表、双向链表的概念与特点；
- 结点结构、头结点、头指针、空指针；
- 单链表的插入与删除操作；
- 循环链表的构造与遍历；
- 双向链表的结点结构与特点；
- 约瑟夫问题的循环链表解法；
- 在链表结构中保存当前位置和元素个数；
- 链表代码编写技巧：理解指针、警惕指针丢失与内存泄漏、利用哨兵、边界条件处理、画图辅助、多写多练。

### 4. 常见题型

- 选择题：线性表特点、顺序表与链表区别、循环链表头结点作用等；
- 填空题：时间复杂度、插入删除元素移动次数；
- 简答题：顺序表与链表的优缺点比较；
- 算法设计题：顺序表插入删除、链表反转、有序链表合并、约瑟夫问题等；
- 编程题：实现顺序表或链表的基本操作。

---

## 四、PPT 中出现的题目整理

### 多选题 1分（Page 10）

**题目：**  
以下哪些选项正确地描述了线性表构造函数的基本作用？

A. 分配存储空间以容纳线性表中的数据元素  
B. 初始化线性表的逻辑结构，比如将元素个数设为0  
C. 清空线性表中的所有元素  
D. 自动释放线性表的存储空间

**答案：** A、B

---

### 单选题 1分（Page 63）

**题目：**  
链式存储结构中，为什么链表通常不使用尾结点作为链表的唯一访问入口？

A. 因为链尾节点不包含有效数据  
B. 因为插入和删除操作只能在链头进行  
C. 因为链尾节点无法直接访问链表的其他节点  
D. 因为链尾节点会导致链表变成循环链表

**答案：** C

---

### 单选题 1分（Page 72）

**题目：**  
在循环链表中，为了简化操作，通常会增加一个头结点，其作用是：

A. 存储链表的长度信息  
B. 使得插入和删除操作更统一和简便  
C. 提高链表的访问速度  
D. 用作最后一个节点的指针

**答案：** B

---

## 五、重点思考题

1. 顺序表插入和删除的时间复杂度是多少？为什么？
2. 顺序表动态扩容为何采用容量加倍策略？其他策略是否可行？
3. 单链表前端和末尾插入、删除的复杂度是否相同？为什么？
4. 循环链表中头结点的作用是什么？
5. 双向链表结点结构如何表示？如何判断指针关系？
6. 约瑟夫问题如何用循环链表求解？
7. 如何轻松写出正确的链表代码？有哪些技巧？

---


如果需要，我还可以继续帮你整理成 **期末复习提纲**、**思维导图** 或 **习题答案详解**。