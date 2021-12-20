# 虚函数

```c
#include <stdio.h>

class Base {
    public:
    void Fun1() {
        printf("Fun\n");
    }
    virtual void Fun2() {
        printf("Fun\n");
    }
};

int main() {
    Base a;
    Base* pb = &a;
    a.Fun1();
    a.Fun2();

    pb->Fun1();
    pb->Fun2();
    return 0;
}
```


## 虚函数表

当类声明中有虚函数时, 编译器会自动生成虚函数表, 并在构造函数中进行初始化.

![20211220081834](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211220081834.png)


