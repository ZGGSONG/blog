---
title: 顺序表操作 | 数据结构
date: 2019-07-19T18:14:00+08:00
lastmod: 2020-03-20T23:47:55+08:00
description: ""
featuredImage: ""
categories:
- coding
---

## 创建顺序表
```c
    typedef struct node{
    DataType data[MAXSIZE];
    int length;
    } SeqList, *PSeqList;
```
## 初始化
```c
    PSeqList Init_SeqList(void)
    {
    PSeqList PL;
    PL = (PSeqList)malloc(sizeof(SeqList));
    if (PL)
        PL->length = 0;
    return PL;
    }
```
## 添加
```c
    int Insert_SeqList(PSeqList PL, int i, DataType x)
    {
    int j;
    if (!PL)
    {
        printf("表不存在");
        return (-2);
    }
    if (PL->length >= MAXSIZE)
    {
        printf("表溢出");
        return (-1);
    }
    if (i < 1 || i > PL->length)
    {
        printf("插入位置异常");
        return 0;
    }
    for (j = PL->length - 1; j >= i - 1; j--)
        PL->data[j + 1] = PL->data[j];
    PL->data[i - 1] = x;
    PL->length++;
    return 1;
    }
```
## 检索
```c
    int Location_SeqList(PSeqList L, DataType x)
    {
    int i = 0;
    while (i < L->length && L->data[i] != x)
        i++;
    if (i >= L->length){
        printf("检索数据异常、n");
        return 0;
    }
    else
        return (i + 1);
    }
```
## 删除
```c
    int Delete_SeqList(PSeqList PL, int i)
    {
    int j;
    if (!PL)
    {
        printf("表不存在");
        return (-1);
    }
    if (i < 1 || i > PL->length)
    {
        printf("删除位置不合法");
        return 0;
    }
    for (j = i; j < PL->length; j++)
        PL->data[j - 1] = PL->data[j];
    PL->length--;
    return 1;
    }
```
## 编译运行
```c
    /*
    顺序表的相关操作
     */
    //创建顺序表
    #include "stdio.h"
    #include "stdlib.h"
    #define MAXSIZE 10000
    typedef int DataType;

    //将 data 和 length 封装成一个结构体作为顺序表
    typedef struct node
    {
    DataType data[MAXSIZE];
    int length;
    } SeqList, *PSeqList;

    //顺序表的初始化
    PSeqList Init_SeqList(void)
    {
        PSeqList PL;
        PL = (PSeqList)malloc(sizeof(SeqList));
        if (PL)
            PL->length = 0;
       return PL;
    }

    //检索
    int Location_SeqList(PSeqList L, DataType x)
    {
        int i = 0;
        while (i < L->length && L->data[i] != x)
            i++;
        if (i >= L->length){
            printf("检索数据异常、n");
            return 0;
        }
        else
            return (i + 1);
    }

    //插入
    int Insert_SeqList(PSeqList PL, int i, DataType x)
    {
        int j;
        if (!PL)
    {
        printf("表不存在");
        return (-2);
    }
    if (PL->length >= MAXSIZE)
    {
        printf("表溢出");
        return (-1);
    }
    if (i < 1 || i > PL->length)
    {
        printf("插入位置异常");
        return 0;
    }
    for (j = PL->length - 1; j >= i - 1; j--)
        PL->data[j + 1] = PL->data[j];
    PL->data[i - 1] = x;
    PL->length++;
    return 1;
    }

    //删除
    int Delete_SeqList(PSeqList PL, int i)
    {
        int j;
        if (!PL)
        {
            printf("表不存在");
            return (-1);
        }
        if (i < 1 || i > PL->length)
        {
           printf("删除位置不合法");
            return 0;
        }
        for (j = i; j < PL->length; j++)
            PL->data[j - 1] = PL->data[j];
        PL->length--;
        return 1;
    }

    //主函数
    int main()
    {
        int m;
        int x;
        SeqList L;
        DataType DataType;
        Init_SeqList();     //初始化
        printf("在表中插入数据的个数：");
        scanf("%d",&m);
        for(int i=0;i<m;i++){
            printf("请输入第%d 个数据：",i+1);
            scanf("%d",&x);
            Insert_SeqList(&L,i+1,x);
        }
        printf("查询所有数据：\n");
        for(int i=0;L.data[i]!=NULL;i++){   //查询所有数据
            printf("%d,",L.data[i]);
        }printf("\b \n");
        printf("输入你想检索的数据：");
        scanf("%d",&m);
        if(Location_SeqList(&L,m))
        printf("元素%d 在表中的位置：%d\n",m,Location_SeqList(&L, m)); //检索位置
        printf("输入您想删除位置上的数据：");
        scanf("%d",&m);
        printf("删除表中位置为%d 的数据、n",m);
        Delete_SeqList(&L,m);           //删除数据
        printf("查询所有数据：\n");
        for(int i=0;L.data[i]!=NULL;i++){   //查询所有数据
            printf("%d,",L.data[i]);
        }printf("\b \n");
        return 0;
    }
```

----------
## 应用——用顺序表解决约瑟夫问题

约瑟夫问题：人们站在一个等待被处决的圈子里。 计数从圆圈中的指定点开始，并沿指定方向围绕圆圈进行。 在跳过指定数量的人之后，执行下一个人。 对剩下的人重复该过程，从下一个人开始，朝同一方向跳过相同数量的人，直到只剩下一个人，并被释放。

函数：
```c
    int josephus_SeqList(PSeqList josephus_seq,int s,int m){
    int s1,i,w;
    if(!josephus_seq->length){
        printf("表中无元素、n");
        return 0;
    }   
    s1=s-1;                                             //data 数组中下标从零开始
    printf("长度：%d\n",josephus_seq->length);          //为什么生成长度是 18(vscode)、长度是 13(dev)
    printf("输出约瑟夫序列：\n");
    for(i=josephus_seq->length-8;i>0;i--){  
        s1=(s1+m-1)%i;                                  //找到列元素的下标
        // printf("s1=%d\ti=%d\n",s1,i);            
        w=josephus_seq->data[s1];                   
        printf("%d\t",w);                           
        Delete_SeqList(josephus_seq,s1+1);      //删除元素、s1+1 是指顺序表中数据的位置
    }
    return 1;
    }
```
