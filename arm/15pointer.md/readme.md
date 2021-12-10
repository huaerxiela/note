# 指针的本质

对编译器而言, 指针仅仅就是一种类型

## 声明/赋值/Sizeof

![20211209163004](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211209163004.png)

Arm64 带*号就是8个字节

## 自增++/--

![20211209163657](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211209163657.png)

    ++/-- 等价于 +=1/-=1

    对应数值为去掉一个*,对应的宽度

## 加减运算+/-

![pointer01](https://cdn.jsdelivr.net/gh/nzcv/picgo/pointer01.png)


总结：

1、带*类型的变量可以加、减一个整数，但不能乘或者除；

2、带*类型变量与其他整数相加或者相减时：

    带*类型变量 + N = 带*类型变量 + N*(去掉一个*后类型的宽度)；
    
    带*类型变量 - N = 带*类型变量 - N*(去掉一个*后类型的宽度)。

## 指针相减

    带*类型变量1 - 带*类型变量2  = (v(带*类型变量1) - v(带*类型变量2))/去掉一个*后类型的宽度

![20211210104915](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211210104915.png)


## &符号的使用

    & 可以获取任何一个变量的地址
    &a 的类型 就是a的类型+*

```c
int a;
void test01() {
    int b;
    int* pa = &a;
}

void test02() {
    int p = 10;

    int *******p7;
    int ******p6;
    int *****p5;
    int ****p4;
    int ***p3;
    int **p2;
    int *p1;

    p1 = &p;
    p2 = &p1;
    p3 = &p2;
    p4 = &p3;
    p5 = &p4;
    p6 = &p5;
    p7 = &p6;
}

void test03() {
    char a = 0xa1;
    short b = 0xa2;
    int c = 0xa3;

    char *pa= &a;
    short *pb = &b;
    int *pc = &c;

    char **ppa = &pa;
    short **ppb = &pb;
    int **ppc = &pc;
}
```
![20211210083548](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211210083548.png)


![20211210084340](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211210084340.png)

## *符号的使用

