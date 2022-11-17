---
title: 单链表操作 | 数据结构
date: 2019-07-24T22:10:00+08:00
lastmod: 2020-03-20T23:49:30+08:00
description: ""
featuredImage: ""
categories:
- coding
---

## 结点定义
```c
    typedef struct node
    {
        DataType data;     //每个元素数据信息
        struct node *next; //存放后继元素的地址
    } LNode, *LinkList;
```
## 创建空单链表
```c
    LinkList Creat_LinkList(void)
    {
        /*创建空单链表，入口参数：无，返回值：单链表的头指针，0 代表创建失败，非 0 代表创建成功 */
        LinkList H;
        H = (LinkList)malloc(sizeof(LNode)); //申请内存空间
        if (H)                               //确认创建头结点是否成功，成功则修改单链表头结点的指针域为 NULL 代表空表
            H->next = NULL;
        return H;
    }
```
## 销毁单链表
```c
    void Destroy_LinkList(LinkList *H)
    { //将头指针作为参数进行销毁
    /*销毁单链表，入口参数：单链表头指针的地址 */
        LinkList p, q;
        p = *H;
        while (p) //释放单链表的所有结点
        {
            q = p;
            p = p->next;
            free(q);
        }
        *H = NULL;
    }
```
## 求表长
```c
    int Length_LinkList(LinkList H)
    {
    /*求单链表表长，入口参数：单链表头指针，出口参数：表长，-1 表示单链表不存在 */
        LinkList p = H; //*p 指向头结点
        int count = -1; //*H 带头节点所以从-1 开始
        while (p)       //*p 所知的是第 count+1 个结点
        {
            p = p->next;
            count++;
        }
        return count;
    }
```
## 查询操作-按序号查找
```c
    LinkList Locate_LinkList(LinkList H, int i)
    {
    /*i 不正确或者链表不存在，则返回 NULL，i==0 返回头指针，否则返回第 i 个结点的指针 */
        LinkList p;
        int j;
        p = H;
        j = 0;
        while (p && j < i) //查找第 i 个结点
        {
            p = p->next;
            j++;
        }
        if (j != i || !p)
        {
            printf("参数 i 错误或者单链表不存在、n");
            return NULL;
        } //第 i 个结点不存在
        return p;
    }
```
## 查找操作——按值查找
```c
    LinkList Locate_LinkList_Value(LinkList H, DataType x)
    {
    /*在单链表中查找值为 x 的结点，入口参数：单链表指针，检索元素 */
    /*出口参数：找到后返回其指针，否则返回：NULL */
        LinkList p = H->next;
        while (p && p->data != x)
        {
            p = p->next;
        }
        return p;
    }
```
## 插入操作
```c
    int Insert_LinkList(LinkList H, int i, DataType x)
    {
    /*在单链表 H 的第 i 个为之前插入值为 x 的结点，入口参数：单链表，插入位置，插入元素，返回参数：0 表示失败，1 表示成功 */
        LinkList p, q;
       p = Locate_LinkList(H, i - 1); //找到第 i-1 个结点地址
        if (!p)
        {
            printf("i 有误、n");
            return 0;
        }
        q = (LinkList)malloc(sizeof(LNode));
        if (!q)
        {
            printf("申请空间失败、n"); //申请空间失败，不能插入
            return 0;
        }
       q->data = x;
        q->next = p->next; //新节点插入在第 i-1 个结点后面
        p->next = q;
        return 1;
    }
```
## 删除操作
```c
    int Delete_LinkList(LinkList H, int i)
    {
    /*删除单链表 H 上的第 i 个结点，入口参数：单链表，删除元素序号，返回参数：0 失败，1 成功 */
        LinkList p, q;
        if (H == NULL || H->next == NULL)
        {
            printf("链表不存在或者空表不能删除、n");
            return 0;
        }
        p = Locate_LinkList(H, i - 1); //找到第 i-1 个结点
        if (p == NULL || p->next == NULL)
        {
            printf("参数 i 错误、n"); //第 i 个参数不存在
            return 0;
        }
        q = p->next;       //q 指向第 i 个结点
        p->next = q->next; //从链表中删除
       free(q);           //释放 q
       return 1;
    }
```

----------
## 单链表的完整代码
```c
    /*
     *  单链表相关操作
     */
    #include "stdio.h"
    typedef char DataType;

    //定义节点
    typedef struct node
    {
        DataType data;     //每个元素数据信息
        struct node *next; //存放后继元素的地址
    } LNode, *LinkList;

    //创建空单链表
    LinkList Creat_LinkList(void)
    {
        /*创建空单链表，入口参数：无，返回值：单链表的头指针，0 代表创建失败，非 0 代表创建成功 */
        LinkList H;
        H = (LinkList)malloc(sizeof(LNode)); //申请内存空间
        if (H)                               //确认创建头结点是否成功，成功则修改单链表头结点的指针域为 NULL 代表空表
            H->next = NULL;
        return H;
    }

    //销毁单链表
    void Destroy_LinkList(LinkList *H)
    { //将头指针作为参数进行销毁
        /*销毁单链表，入口参数：单链表头指针的地址 */
        LinkList p, q;
        p = *H;
        while (p) //释放单链表的所有结点
        {
           q = p;
           p = p->next;
           free(q);
        }
        *H = NULL;
    }

    //求表长
    int Length_LinkList(LinkList H)
    {
        /*求单链表表长，入口参数：单链表头指针，出口参数：表长，-1 表示单链表不存在 */
        LinkList p = H; //*p 指向头结点
        int count = -1; //*H 带头节点所以从-1 开始
       while (p)       //*p 所知的是第 count+1 个结点
        {
            p = p->next;
            count++;
        }
        return count;
    }

    //查找操作——按序号查找
    LinkList Locate_LinkList(LinkList H, int i)
    {
        /*i 不正确或者链表不存在，则返回 NULL，i==0 返回头指针，否则返回第 i 个结点的指针 */
        LinkList p;
        int j;
        p = H;
        j = 0;
        while (p && j < i) //查找第 i 个结点
        {
            p = p->next;
            j++;
        }
        if (j != i || !p)
        {
            printf("参数 i 错误或者单链表不存在、n");
            return NULL;
        } //第 i 个结点不存在
        return p;
    }

    //查找操作——按值查找
    LinkList Locate_LinkList_Value(LinkList H, DataType x)
    {
        /*在单链表中查找值为 x 的结点，入口参数：单链表指针，检索元素 */
        /*出口参数：找到后返回其指针，否则返回：NULL */
        LinkList p = H->next;
        while (p && p->data != x)
        {
            p = p->next;
        }
        return p;
    }

    //插入操作
    int Insert_LinkList(LinkList H, int i, DataType x)
    {
        /*在单链表 H 的第 i 个为之前插入值为 x 的结点，入口参数：单链表，插入位置，插入元素，返回参数：0 表示失败，1 表示成功 */
        LinkList p, q;
        p = Locate_LinkList(H, i - 1); //找到第 i-1 个结点地址
        if (!p)
        {
            printf("i 有误、n");
            return 0;
        }
        q = (LinkList)malloc(sizeof(LNode));
        if (!q)
        {
            printf("申请空间失败、n"); //申请空间失败，不能插入
            return 0;
        }
        q->data = x;
        q->next = p->next; //新节点插入在第 i-1 个结点后面
        p->next = q;
        return 1;
    }
    
    //删除操作
    int Delete_LinkList(LinkList H, int i)
    {
        /*删除单链表 H 上的第 i 个结点，入口参数：单链表，删除元素序号，返回参数：0 失败，1 成功 */
        LinkList p, q;
        if (H == NULL || H->next == NULL)
        {
            printf("链表不存在或者空表不能删除、n");
            return 0;
        }
        p = Locate_LinkList(H, i - 1); //找到第 i-1 个结点
        if (p == NULL || p->next == NULL)
        {
            printf("参数 i 错误、n"); //第 i 个参数不存在
            return 0;
        }    
        q = p->next;       //q 指向第 i 个结点
        p->next = q->next; //从链表中删除
        free(q);           //释放 q
        return 1;
    }

    //主函数
    int main()
    {
        LinkList H;
        int i = 1;
        char x = '0';
        H = Creat_LinkList(); //创建链表
        printf("请问需要插入几个元素：");
        scanf("%d", &i);
        getchar(); //接收回车字符，不可缺
        printf("请输入字符：");
        for (int j = 1; j <= i; j++)
        {
            scanf("%c", &x);          //不能写在函数里面，否则会出错
            getchar();                //接收回车字符，不可缺
            Insert_LinkList(H, j, x); //插入结点数据——如果不确定 i 的大小而用 i++，最后表长会错误（表长大于 i 的实际值）
        }
        for (int j = 1; j <= i; j++)    //输出所有结点数据
        {
            printf("%c\t", Locate_LinkList(H, j)->data);
        }

        printf("\n 请输入您想查询的结点位置：\n"); //位置查找
        scanf("%d", &i);
        printf("结点元素为：%c\n", Locate_LinkList(H, i)->data);
        getchar();                            //接受回车字符
        printf("请输入您想查询的结点的值："); //值查找
        scanf("%c", &x);
        if (Locate_LinkList_Value(H, x))
        {    
            printf("查询成功、n");
        }
        else
        {
            printf("查询失败、n");
        }
        printf("表长为：%d\n", Length_LinkList(H)); //求表长
        printf("请输入想要删除结点的位置：");
        scanf("%d", &i);
        int m = Delete_LinkList(H, i); //删除结点
        if (m == 1)
            printf("删除成功、n");
        Destroy_LinkList(&H); //销毁链表
        return 0;
    }

```
----------
## 应用

**这里的应用是用单链表解决约瑟夫问题**

**首先创建循环单链表**
```c
        //定义节点
    typedef struct node
    {
        DataType data;     //每个元素数据信息
        struct node *next; //存放后继元素的地址
    } LNode, *LinkList;

    //创建循环单链表
    LinkList Creat_LinkList(int n)
    {
        LinkList head;
        LinkList p;
        LinkList q;
        p = (LinkList)malloc(sizeof(LNode));
        p->data = 1;
        head = p;
        for(int i=2;i<=n;i++)
        {
            q = (LinkList)malloc(sizeof(LNode));
            q->data = i;
            p->next = q;
            p = q; //p 永远指向最后一个节点
        }
        p->next = head;
        return head;
    }
```
**约瑟夫问题的单链表完整代码**
```c
        /*
        约瑟夫问题——单链表
    */
    #include "stdio.h"
    typedef int DataType;

    //定义节点
    typedef struct node
    {
        DataType data;     //每个元素数据信息
        struct node *next; //存放后继元素的地址
    } LNode, *LinkList;

    //创建循环单链表
    LinkList Creat_LinkList(int n)
    {
        LinkList head;
        LinkList p;
        LinkList q;
        p = (LinkList)malloc(sizeof(LNode));
        p->data = 1;
        head = p;
        for (int i = 2; i <= n; i++)
        {
            q = (LinkList)malloc(sizeof(LNode));
            q->data = i;
            p->next = q;
            p = q; //p 永远指向最后一个节点
        }
        p->next = head;
        return head;
    }

    //查找操作——按序号查找
    LinkList Locate_LinkList(LinkList H, int i)
    {
        /*i 不正确或者链表不存在，则返回 NULL，i==0 返回头指针，否则返回第 i 个结点的指针 */
        LinkList p;
        int j;
        p = H;
        j = 0;
        while (p && j < i) //查找第 i 个结点
        {
            p = p->next;
            j++;
        }
        if (j != i || !p)
        {
            printf("参数 i 错误或者单链表不存在、n");
            return NULL;
        } //第 i 个结点不存在
        return p;
    }

    //插入操作
    int Insert_LinkList(LinkList H, int i, DataType x)
    {
        /*在单链表 H 的第 i 个为之前插入值为 x 的结点，入口参数：单链表，插入位置，插入元素，返回参数：0 表示失败，1 表示成功 */
        LinkList p, q;
        p = Locate_LinkList(H, i - 1); //找到第 i-1 个结点地址
        if (!p)
        {
            printf("i 有误、n");
            return 0;
        }
        q = (LinkList)malloc(sizeof(LNode));
        if (!q)
        {
            printf("申请空间失败、n"); //申请空间失败，不能插入
            return 0;
        }
        q->data = x;
        q->next = p->next; //新节点插入在第 i-1 个结点后面
        p->next = q;
        return 1;
    }

    //约瑟夫问题-链表解决（这里需要循环链表）
    int josephus_LinkList(LinkList josephus_Link, int s, int m)
    {
        /*求约瑟夫问题得出列元素序列，入口参数：已经存放数据的链表头指针，起始位置 s，从 1 报数到 m，出口参数：1 成功，0 表示表中没有元素 */
        LinkList pre, p; //p 表示当前结点，pre 指向其前驱结点
        int count;
        if (!josephus_Link)
        {
            printf("表中无元素！\n");
            return 0;
        }
        /*找到第 i 个元素 */
        p = josephus_Link;
        for (count = 1; count < s; count++) //查找第 s 个结点，用 p 作为第 s 个结点的指针
        {
            p = p->next;
        }
        printf("输出约瑟夫序列：\n");
        while (p != p->next) //输出 n-1 个结点
        {
            pre = p->next;
            while (pre->next != p)
            {
                pre = pre->next; //pre 指针初始化，pre 是 p 的前驱指针（单链表，使之让 pre 成为 p 的前驱结点）
            }
            for (count = 1; count < m; count++)
            {
                pre = p;
                p = p->next;
            }
            printf("%d\t", p->data);
            pre->next = p->next; //进行删除操作
            free(p);
            p = pre->next;
        }
        printf("%d\t", p->data); //输出最后一个结点
        free(p);
        return 1;
    }

    //主函数
    int main()
    {
        LinkList H;
        LinkList pre;
        int s, m, x;
        int i = 1;
        printf("请问需要插入几个元素：");
        scanf("%d", &i);
        H = Creat_LinkList(i); //创建链表
        pre = H->next;
        while (pre->next != H)
        {
            pre = pre->next; //pre 指针初始化，pre 是 H 的前驱指针（单链表，使之让 pre 成为 H 的前驱结点）
        }
        for (int j = 1; j <= i; j++) //输出所有结点数据
        {
            printf("%d\t", Locate_LinkList(pre, j)->data); //如使查询顺序正确，第一个数据的结点应该是头结点的前驱结点
        }
        printf("\n 请输入起始的序列号 s、以及计数值 m:\n");
        scanf("%d%d", &s, &m);
        josephus_LinkList(H, s, m);
    }
```
