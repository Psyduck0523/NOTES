[TOC]

# MOOC 翁恺 C语言程序设计进阶 笔记

## 指针与字符串

### 指针使用



#### 指针基础运用

- 交换函数外变量

```c
void swap(int *pa,int *pb)
{
    int t = *pa;
    *pa = *pb;
    *pb = t;
}
```

> 函数返回多个值,就必须通过指针.
>
> 传入的参数实际上需要保存带回的结果的变量.
>
> 例如 我们用指针写一个函数来找出数组中最大,最小值并存回min,max.
>
> 所以指针负责带回函数的运算结果,不止一个.

- 函数用指针返回错误/正确

> 我们用函数返回值来表明运算状态/判断,用指针来返回结果,return 0 代表运算异常,*result来表示结果.

- 指针常见错误

> 定义了一个指针,未指向任何一个变量地址就使用.

#### 指针与数组

由入门笔记中我们看到,数组变量是特殊的指针.

#### 指针与const

- 指针本身可以是const,指针所指的变量也可以是const

> -  ==指针const :一旦得到了某个变量的地址,就不能再指向其他变量.==
>
> > int const *p = &i; //q是const
> >
> > *q = 26;(√)  q++;(×); *q ++;(√)
>
> -  ==const指针:表示不能通过这个指针去修改那个变量(并不是使得那个变量成为const)==
>
> > const int *p = &i;
> >
> > *p = 26;(×)  i = 26;(√)  p = &j(√)
>
> - 实例
>
> ```c
> int i;
> const int *p1 = &i;
> int const *p2 = &i;
> int *const p3 = &i;
> ```
>
> > 判断到底属于那种类型的标志是const和*的位置关系
> >
> > const在*的前面 → 不能通过指针去修改所指变量
> >
> > const在*的后面 → 指针本身不能修改(不能指向另一个变量)

- const转换

总是可以把一个非const转换为const

> ```c
> void f(const int *p);
> ```
>
> 我们定义了一个函数头,参数表力要一个const指针,但我们调用时传一个非const指针也不会出错,所以这句话实际上指的是,你给函数一个指针,函数保证不会在内部去改动指着所指的变量的值.
>
> 这个用法常用于:要传递的参数类型比地址还大,我们就用这样的方法,既能用比较少的字节数传递值给参数,又能避免函数对外面的变量的修改(比如结构)

- const数组

> 数组本身就是const的指针,我们再加const指的是让数组每个单元都变成const变量.
>
> 同样,我们这个操作用法和上面类似,因为数组传入函数传的实际上是地址,所以函数可以修改,为了保护数组不被函数破坏,我们就用const数组.

### 指针运算

#### 指针运算初步

指针加1是什么?

```c
char ac[] = {0,1,2};
char *p = ac;
printf("%p",p);
printf("%p",p+1);//输出的结果比p多1(十六进制数值加1)
int ai[] = {0,1,2};
int *q = ai;
printf("%p",q);
printf("%p",q+1);//输出的结果比q多一个字节长(十六进制数值加4)

```

> sizeof(char) = 1;
>
> sizeof(int) = 4;
>
> 所以我们得到结论,**指针加1,并不是加1个数值,而是加上一个它所指向的变量类型的长度**.
>
> 对指针加1,意味着把它指向的对象移到下一个单元里!
>
> ```c
> *p → ac[0];
> *(p+1) → ac[1];
> ```
>
> 但如果指针不是指向一片连续的空间,那这种操作是没有意义的.

- 算数运算

> - +/- 一个整数
>
> - 递增递减
>
> - 两个指针可以相减(结果是指针在数组中的单元距离)

- *p++

> 取出p所指的那个数据来,然后顺便把p移到下一个位置(后缀++是这样的)
>
> *的优先级虽然高,但没有++高
>
> 常用于数组类的连续空间操作(比如遍历)
>
> ```c
> int ac[] = {0,1,2,3,4,5,6,7,-1};
> for(p = ac;*p != -1;p++)
> {
>     printf("%d\n",*p++);
> }
> ```

- 指针比较

> - < , <= , == 等都可以对指针做

- 0地址

> 0地址不能随便碰,因此可以用0地址来表示特殊的事情:返回的指针无效/指针没有被真正初始化(先初始化为0).
>
> NULL是一个预定定义的符号,它表示0地址.

- 指针的类型转换

> - void *p 表示不知道指向什么东西的指针(计算时与char *相同,但不相通)
> - 指针也可以转换类型
>
> > int *p = &i; void *q = (void *)p;
> >
> > 通过p去看i,认为是int,通过q去看i,我们认为它啥也不是了.

- 小结

> - 需要传入较大的数据时用指针做参数
> - 传入数组之后对数组做操作
> - 函数返回不止一个结果

#### 动态内存分配

> C99之前,如何实现数组的动态大小呢?

- malloc函数

> void* malloc(size_t,size)  包含在<stdlib.h>中
>
> ```c
> int *p;
> int num;
> scanf("%d",&num);
> p = (int*)malloc(num*sizeof(int));
> //这时候p中就存了所需大小的地址
> //之后就是把p当作数组操作
> //.......
> free(p);
> ```
>
> - ==malloc返回的是void*,所以我们要进行类型转换.==
> - malloc把内存借了一部分给指针,当你操作完了之后,一定要记住把内存还给系统,用free()来实现.
> - 内存是有限的,所以千万不要忘记free它,但是free的时机,需要经验和知识.
> - ==free()函数只能还回申请来的空间的首地址.==
> - 如果在其中你进行了p++,p--等操作,这时候p里面的地址就不是申请的首地址了.
> - free(0)也是可以实现的,因为前面我们讲过创建一个指针,先赋值0,或许中间有什么原因你没有对这个指针进行再赋值,所以free(0),会让程序不报错.



### 字符串操作

#### 单字符操作

- putchar函数

> - int putchar(int c);
> - 向标准输入写一个字符,返回写了几个字符,一般是1,但如果写入失败,返回EOF(-1);

- getchar函数

> - int getchar(void);
> - 从标准输入读入一个字符,返回类型为int是为了返回EOF(-1);
> - 程序结束在windows下是ctrl+Z,在Unix下是ctrl+D;

#### 字符串数组(涉及指针)

> 如果我想创建一个数组指向多个字符串怎么办?

```c
char a[][10] = {
	"hello",
    "world",
    "abcdefghijk"//报错
}
```

> char **a的意思是,a是一个指针,这个指针指向另一串指针,而那些指针指向字符串.
>
> char a[ ] [ ]的第二个方括号里面必须有东西,这实际上是一个二维数组,第二个方括号里表示的是字符串的最大长度
>
> char *a[ ] 这个写法就不会限制长度(如果后面紧跟着初始化的话)

- char a[ ] [10 ]

> 这个意思就是一个矩形,每一行都是a[x],然后长度是第二个括号里面的值

- char *a[ ]

> 这个意思是有一个a数组,但是数组的每个元素都是指针,这些指针指向字符串数组
>
> (如何用字符串数组来代替switchcase月份?)
>
> ```c
> #include <stdio.h>
> int main()
> {
>     char a[][10] = {
>         "january",
>         "february",
>         "march",
>         "april",
>         "may",
>         "june",
>         "july",
>         "august",
>         "september",
>         "october",
>         "november",
>         "december",
>     } ;
>     printf("%s", a[8]);
>     return 0;
> }
> ```

- 字符串数组还可以当程序参数

>  int main(int argc,char const*argv[ ])
>
> argc是用来告诉函数后面的字符串有多大的,argv[0]是命令本身
>
> 我们试一试这个argv是什么,但是没看懂



### 字符串函数实现string.h

#### strlen

> size_t strlen(const char*s);
>
> 返回s的字符串长度(**不包括结尾的0**)
>
> ```c
> #include <stdio.h>
> #include<string.h>
> int main()
> {
>     char a[] = "hello";
>     printf("strlen = %lu\n", strlen(a));
>     printf("sizeof = %lu\n", sizeof(a));
>     return 0;
> }
> //strlen = 5
> //sizeof = 6
> ```
>
> 所以我们看到,strlen返回5而sizeof返回6
>
> ```c
> int mylen(const char *s)
> {
>     int idx = 0;
>     while(s[idx] != '\0'){
>         idx++;
>     }
>     return idx;
> }
> ```
>
> 上面就是我们自己写的len函数,它是可行的

#### strcmp

> int strcmp(const char *s1,const char *s2);
>
> 比较两个字符串,返回
>
> 0:s1 == s2
>
> 1:s1>s2
>
> -1:s1<s2
>
> ```c
> int mycmp(const char *s1, const char *s2)
> {
>     int idx = 0;
>     int equ = 1;
>     while (equ == 1)
>     {
>         if (s1[idx] == s2[idx])
>         {
>             equ = 1;
>         }
>         else
>         {
>             int x = s1[idx] - s2[idx];
>             return x;
>         }
>         if (s1[idx] == '\0' && s2[idx] == '\0')
>         {
>             return 0;
>         }
>         idx++;
>     }
> ```
>
> 当然,有更好的写法
>
> ```c
> int mycmp(const char *s1;const char *s2){
>     while(*s1 == *s2 && *s1 != '\0'){
>         s1++;
>         s2++;
>     }
>     return *s1-*s2;
> }
> ```

#### strcpy

> char * strcpy(char *restrict dst,const char *restrict src);
>
> 把src的字符串拷贝到dst里
>
> restrict表明二者没有重叠部分
>
> 返回dst
>
> - 我们经常会做这样的操作
>
> ```c
> char *dst = (char*)malloc(strlen(src)+1);
> //这表明我们申请了一块和src一样大的空间叫dst以便把src复制过来
> //千万别忘+1
> ```
>
> 代码实现(数组版本)
>
> ```c
> char *mycpy(char *dst,const char *src)
> {
> int idx = 0;
>     while(src[idx] != '\0')
>     {
>         dst[idx] = src[idx];
>         idx++
>     }
>     dst[idx] = '\0';
>     return dst;
> }
> ```
>
> 指针版本
>
> ```c
> char *mycpy(char *dst,const char *src)
> {
>     char *ret = dst;
>     while(*dst++ = *src++;);
> //发现了吗,形参里面的const char 表明了指针可以更改,但是所指元素不能更改
>     return ret;
> }
> ```
>
> 
>
> 

#### 字符串搜索函数

> char *strchr(const char *s,int c);
>
> char *strrchr(const char *s,int c);
>
> 在s里寻找ASCII码是c的第一个位置,并返回那个指针,前者从左边找,后者从右边找
>
> 返回NULL表示没找到
>
> char *strstr(const char *s1,const char *s2);
>
> char *strcasestr(const char *s1,const char *s2);



## 结构类型

### 枚举

#### 枚举初步

> 我们之前讨论过常量符号化,把magic number改成符号.
>
> 当然,今天我们要学习更方便的方法,叫枚举.

```c
enum COLOR{RED,YELLOW,GREEN};
```

枚举的结构如上.

- 枚举是一种用户定义的数据类型,用关键字enum来书写;
- 通常我们不去用枚举类型名(COLOR),我们要的是枚举的名字(RED等);
- 枚举量可以作为值,枚举类型可以跟上enum作为类型,但是实际上是作为整数来计算和外部输入输出的.
- 枚举套路;自动计数的枚举

```c
enum COLOR{RED,YELLOW,GREEN,NumCOLORS};
```

- 当然,枚举可以指定值.

```c
enum COLOR{RED = 1,YELLOW,GREEN = 5,NumCOLORS};
//这时候 red 1,yellow 2,green 5,numcolors 6;
```

### 结构

> 一个结构就是一个复合的数据类型,我们用一个变量来表达多个数据.

#### 结构类型初步

- **结构声明**

```c
#include <stdio.h>

struct date{
    int month;
    int day;
    int year;
};//第一种声明方式:有名声明


int main()
{
    struct date today;//定义一个变量名字叫today，类型是结构date
    today.day = 10;//结构变量名.成员名 是最小单元
    today.month = 10;
    today.year = 22;
    return 0;
}
```

```c
struct{
    int x;
    int y;
}p1,p2;//第二种声明方式:无名声明(不常见)
```

> - p1,p2都是无名结构,里面有x,y.
>
> - 结构声明通常放在所有函数外面

- **结构变量初始化**

```c
#include <stdio.h>
struct date{
    int month;
    int day;
    int year;
};
int main()
{
    /*第一种方式*/
    
    struct date today;//创建一个结构变量
    today.day = 10;
    today.month = 10;
    today.year = 22;//对变量的成员赋值
    
    /*第二种方式*/
    struct date tomorrow = {10,11,2022};
    struct date thismonth = {.month = 10,.year = 2022};//离散赋值
    
    return 0;
}
```

> 注意:离散赋值时那些没被赋值的成员会被赋为0;
>
> 用  **.**  运算符来访问结构的成员

- **结构运算**

> - 访问整个结构,直接使用结构变量的名字
> - 对于整个结构,可以赋值,取地址,也可以传递给函数参数
>
> ```c
> today = tomorrow;//相当于把tomorrow里的三个值对应赋值给today
> ```
>
> - 结构变量的名字并不是结构变量的地址,必须使用&运算符,**这和数组性质完全不同**
>
>   



#### 结构与函数

- 结构作为函数参数

```c
int numberofdays(struct date d);
```

> - 整个结构可以作为参数传入函数
> - 这时候是在函数内部新建一个结构变量,然后复制调用者的值(传值类型)
> - 当然也可以返回一个结构

- 结构的输入

> - **没有直接方式可以一次scanf一个结构**,我们可以写一个函数来读入结构,但是要注意结构的传递是传值.
>
>   > 一种解决方案是用指针,当然另一种思路是我们直接传一个结构出去.
>   >
>   > ```c
>   > struct point getstruct(void)
>   > {
>   >     struct point p;
>   >     scanf("%d%d",&p.x,&p.y);
>   >     return p;
>   > }
>   > ```
>   >
>   > 别忘了结构是可以直接赋值的,所以我们就可以写出这样的语句:
>   >
>   > ```c
>   > struct y = {0,0};
>   > y = getstruct();
>   > ```
>   >
>   > 但是指针的方案是更有效的.(特别是结构很大的时候)
>
> - **指向结构的指针**
>
> > ```c
> > struct date{
> >     int month;
> >     int day;
> >     int year;
> > }myday;
> > 
> > struct date *p = &myday;
> > (*p).month = 12;//这样不好写
> > p->month = 12;//用 -> 来表示指针所指的结构变量中的成员
> > ```
> >
> > 读入函数的实现
> >
> > ```c
> > struct date *getstruct(struct date *p)
> > {
> >     scanf("%d%d%d",&(p->day),&(p->month),&(p->year));
> >     return p;
> > }
> > //.....
> > ```



#### 结构中的结构

> 我们之前一直拿结构和数组比较,看似他们很多方面不同,但是偏偏就有一种奇怪玩意,它的名字就是**结构数组**

- 结构数组

> ```c
> struct date dates[100];
> struct date dates[] = {
>     {4,5,2022},
>     {2,4,2022},
>     {2,6,2022},
>     //...
> }
> ```

- **嵌套结构**

> ```c
> struct dateAndTime{
>     struct date sdate;
>     struct time stime;
> }
> ```
>
> 这时候就会出现两个点的情况:  **r.ptl.x  ,  r.ptl.y**
>
> ```c
> struct ptl{
>     int x;
>     int y;
> }//ptl是一个二维点(整数点)
> struct rectangle
> {
> struct ptl1;
> struct ptl2;
> }//两个整数点确定一个矩形
> struct rectangle r,*rp;
> rp = &r;
> ```
>
> 如果有上面的程序,那么下面四种形式等价
>
> ```c
> r.ptl.x
> rp->ptl.x
> (r.ptl).x
> (rp->ptl).x
> rp->ptl->x//最后是不对的,因为ptl不是指针
> ```
>
> 你以为这完了?
>
> 不不不.
>
> **人类组合事物的能力永无止境**
>
> 结构中的结构的结构数组......这都是可以存在的.

### 联合

#### 类型定义

> 我们学习结构的时候每次都要写上struct,这未免有点太过烦人,我们有什么办法能不写这个struct呢?

- **typedef**

> ```c
> typedef int Length;
> ```
>
> 这句话使得Length成为了int的别名.
>
> 所以有:
>
> ```c
> typedef struct date{
>     int month;
>     int day;
>     int year;
> } DATE;
> ```
>
> 这就把结构声明简化成了DATE了.

#### 联合初步

```c
union AnElt{
    int i;
    char c;
}elt1,elt2;
elt1.i = 4;//等价于elt1.c = 4;
elt2.c = '0';//等价于elt2.i = 48;
```

我们联合!!!

- 联合的书写形式和结构相似

- 联合的成员共用一个内存空间(值统一)

- union储存:
  - 所有成员共享一个空间
  - 同一时间只有一个成员是有效的
  - union的大小是其最大的成员大小
- union初始化:对第一个成员初始化

- union的应用场合比较少(目前来说);

## 链表(hard)

### 链表基础

#### 分析可变数组的缺陷

- 每一次可变数组变大的时候我们都要去申请一个额外空间,并拷贝原数组.
  - 拷贝和申请都要时间.
  - 如果数组一直变大,最后不是总会不够申请吗?

**所以我们要学习链表**

#### 链表初步

> 链表的关键是链,所以我们需要指针.

- 链表结构

> - 全表 = 节点+节点+节点+.......
> - 节点 = 数据+指针

- 代码实现

> - 类型创建
>
> ```c
> typedef struct _node{
>     int value;
>     struct _node *next;
> }Node;//我们类型定义了Node.
> ```
>
> - 增加元素
>
> > 代码实例:读入整数,并储存
>
> ```c
> int main()
> {
>     Node * head = NULL;
>     int number;
>     do{
>         scanf("%d",&number);
>         if(number != -1){//储存数据
>            Node *p = (Node*)malloc(sizeof(Node));
>             p->value = number;
>             p->next = NULL;
>             //找到最后的那个节点然后让它接上去
>             Node *last = head;
>             if(last){
>             while(last->next){
>                 last = last->next;
>             }//这时候我们找到了最后的节点
>             last->next = p;
>             }else{
>                 head = p;
>             }       
>         }
>     }while(number != -1);
> }
> ```
>
> 

#### 链表中的函数(hard)

- 写入函数

> 我们想把上面得代码抽象成函数,那么我们首先需要给出存入得数据,其次我们还要给出链表得头.函数命名为add
>
> 但是我们遇到了麻烦:如果这个head是空,我们就要去修改head对不对,但是我们传进去得head是个结构体,它是没法修改的!
>
> 有什么解决变法?
>
> - 全局变量.(这个太蠢了,只能适用于一个程序)
> - 传出去一个结构,让程序员自己注意.(风险太大)
> - 传指针进去(听上去不错)
> - 第四种做法//代码可能有误
>
> ```c
> typedef struct _node{
>     int number;
>     struct _nude *next;
> }Node;
> typedef struct _list{
>     Node* head;
>     Node* tail;
> }List;
> 
> void add(List* pList,int number){
>     Node *p = (Node*)malloc(sizeof(Node));
>     p -> value = number;
>     p -> next = NULL;
> 	if(plist->head->next == NULL)
>     {
>         plist->head = p;
>         plist->head->next = tail;
>     }else{
>         Node *t = (Node*)malloc(sizeof(Node));
>         t = NULL;
>         plist->tail = p;
>         plist->tail->next =t;
>     }
> }
> int main()
> {
>     List list;
>     list.head = list.tail = NULL;
>     int number;
>     do{
>         scanf("%d",&number);
>         if(number != -1){
>       		add(&list,number);
>         }
>     }while(number != -1);
> }
> 
> 
>      
> ```
>
> 

#### 链表搜索初步(hard)

> 链表如何用for循环?

```c
Node*p;
for(p = list.head;p;p = p->next){//这样的写法是链表非常常用的写法
    printf("%d\t",p->value);
}
```

我们同样可以把这个东西封装成函数.

#### 链表的删除与清除(hard)

> 删除链表,实际上就是让前面的node指向后面那个,然后再free p;
>
> 但是我们现在知道的链表没有往回走的东西啊!(所以我们的for循环得改,必须保留一个指针指向前面那个)
>
> ```c
> Node *q;
> Node *p;
> for(q=NULL,p = list.head;p;q=p,p=p->next){
>     if(p->value == number){
>         if(q){
>         q->next = p->next;
>         }else{
>             list.head = p->next;
>         }
>         free(p);
>         isfound = 1;
>         break;//寻找
>     }
> }
> ```
>
> - 经验:==所有位于->左侧的指针必须非空==

> 我们的链表全都是malloc来的,所以我们最后一定要清除链表.怎么实现?
>
> ```c
> for(p = head;p;p=q){
>     q = p->next;
>     free(p);
> }
> ```
>
> 



## 程序结构

### 全局变量

#### 全局变量初步

人如其名.

> - 全局变量定义在所有函数之外.
> - 全局变量的生命周期是整个程序.
> - 没有做初始化的全局变量会得到0值.
> - 没有做初始化的指针全局变量会得到NULL.
> - 只能用编译时刻已知的值来初始化全局变量.

#### 静态本地变量

- 本地变量定义时加上static修饰符就是静态本地变量.
- 当函数离开的时候静态本地变量会继续存在并保持其值.
- 静态本地变量的初始化只会在第一次进入这个函数的时候做,以后进入函数时会保持上次离开的值.
- 静态本地变量实际上是特殊的全局变量,他们位于相同内存区域.

#### 全局变量技巧

- 尽量避免使用全局变量.





### 编译预处理与宏

#### 宏定义

- #开头的是编译预处理指令

- 它们不是C语言的一部分,但是C程序离不开他们

- #define用来定义一个宏.

  > - 名字必须是一个单词,值可以是任何东西
  > - 完全的文本替换
  > - 如果一个宏的值中有其他宏的名字,也是会被替换的
  > - 如果一个宏的值超过一行,最后一行之前的行末要加\
  > - 宏的值不包含注释

#### 参数宏

- 宏可以带参数.
- 编写参数宏的两个原则:==整个宏的值一定有括号,所有参数一定有括号==

```c
#define cube(x) ((x)*(x)*(x));
```

- 使用场合
  - 大型代码十分常见
  - 可以非常复杂,甚至产生函数
  - 运行速度比函数稍微快一点,但是占用空间.
  - 但是宏不存在类型检查,可能会出问题.

### 大程序结构

#### 多个源代码文件

- 如何组合两个独立的源代码文件?

> 放在同一个项目里.(很抱歉v s c o d e不是一个I D E只是一个文本编辑器)
>
> 一个.c文件是一个编译单元,编译器每次只能编译一个.c文件,编译之后形成的是.o文件,然后通过链接器把这些.o文件连起来.

#### 头文件

> 把函数原型放到一个头文件中(以.h结尾),在需要调用这个函数的源代码文件(.c文件)中#include这个头文件,就能让编译器在编译的时候知道函数的原型

- " "和< >的区别

> " "要求编译器首先在当前目录寻找这个文件,如果没有到编译器指定的目录去找
>
> < > 让编译器只在指定的目录去找
>
> 编译器知道自己的标准库的头文件在哪里

#### 声明

- 声明与定义

> ```c
> int i;//定义
> extern int i;//声明
> ```
>
> - 声明是不产生代码的东西
>
> > 函数原型,变量声明,结构声明,宏声明,枚举声明,类型声明, inline 函数
>
> - 定义是产生代码的东西
>
> - 同一个编译单元里,同名结构不能被重复声明
> - 如果头文件里面有结构的声明,很难这个头文件不会在一个编译单元里被#include多次
> - 于是需要"标准头文件结构"
>
> ```c
> #ifndef _LIST_HEAD_
> #define _LIST_HEAD_
> //******
> 
> #endif
> ```
>
> 



## 指针(hard)

### 函数指针及其应用

#### 函数指针技巧

**函数有地址**

用函数的名字就可以得到函数的地址

#### 函数指针应用

可以用一个变量来去表示一个函数

**这是魔法**

## 位运算

### 位运算基础

#### 按位运算

> & 按位与	| 按位或	~按位非	^按位异或	<<左移	>>右移

- 应用

> - &
>
> > 让某一位或某些位变成0  x & 0xFE(使得最后一位成0)
> >
> > 取一个数中的一段   x & 0xFF(取最后八位)
>
> - **|**
>
> > 让某一位或某一段变成1 x | 0x01
> >
> > 把两个数拼起来 0x00FF | 0xFF00
>
> - ~
>
> > 把1变成0,0变成1
> >
> > 想得到全部位是1的数就~0



#### 移位运算

- <<

> x << 1 等价于 x *= 2(x为int)
>
> x << n 等价于 x*= 2的n次方



