# 符号和函数存在多对一的关系

# 符号表

本质: 地址和字符串的对应关系

```c
/* Symbol table entry.  */
typedef struct
{
  Elf32_Word	st_name;		/* Symbol name (string tbl index) */
  Elf32_Addr	st_value;		/* Symbol value */
  Elf32_Word	st_size;		/* Symbol size */
  unsigned char	st_info;		/* Symbol type and binding */
  unsigned char	st_other;		/* Symbol visibility */
  Elf32_Section	st_shndx;		/* Section index */
} Elf32_Sym;
```
![20211227190249](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211227190249.png)


![20211227081625](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211227081625.png)

st_info(符号绑定信息)，低4位表示符号类型（Symbol Type）,高28位表示符号绑定信息（Symbol Binding）

![20211227081859](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211227081859.png)

![20211227081913](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211227081913.png)

st_shndx(符号所在段) 如果符号是定义在目标文件中，那么符号的意思就是符号所在的段的在段表中的下标 ，此外还有几种特殊的定义如下：

![20211227081954](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211227081954.png)

```shell
symbol address=0x00000000, size=00000000, name=. type=0 bind=0 ndx=0
symbol address=0x000005f0, size=00000000, name=. type=3 bind=0 ndx=a
symbol address=0x00011000, size=00000000, name=. type=3 bind=0 ndx=13
symbol address=0x00011008, size=00000000, name=_bss_end__. type=0 bind=1 ndx=fff1         //该符号包含了一个绝对的值
symbol address=0x00000668, size=00000048, name=printf_hello. type=2 bind=1 ndx=a          //export   
symbol address=0x00000000, size=00000000, name=printf. type=2 bind=1 ndx=0                //import
symbol address=0x00000000, size=00000000, name=__cxa_finalize. type=2 bind=1 ndx=0        //import
symbol address=0x0000062c, size=00000060, name=say_hello. type=2 bind=1 ndx=a             //export
symbol address=0x00011008, size=00000000, name=__bss_start. type=0 bind=1 ndx=fff1        //该符号包含了一个绝对的值
symbol address=0x00011008, size=00000000, name=__end__. type=0 bind=1 ndx=fff1            //该符号包含了一个绝对的值
symbol address=0x00000000, size=00000000, name=__android_log_print. type=2 bind=1 ndx=0   //import
symbol address=0x00011008, size=00000000, name=__bss_start__. type=0 bind=1 ndx=fff1      //该符号包含了一个绝对的值
symbol address=0x00011008, size=00000000, name=_edata. type=0 bind=1 ndx=fff1             //该符号包含了一个绝对的值
symbol address=0x00011008, size=00000000, name=__bss_end__. type=0 bind=1 ndx=fff1        //该符号包含了一个绝对的值
symbol address=0x00011008, size=00000000, name=_end. type=0 bind=1 ndx=fff1               //该符号包含了一个绝对的值
symbol address=0x00000000, size=00000000, name=__cxa_atexit. type=2 bind=1 ndx=0          //import
```


st_value(符号值) 符号如果是一个函数或者变量，符号值表示函数或者变量的地址，特殊地st_value还有以下几种可能：

* 在目标文件中，符号类型不是COMMON类型（st_shndx!=SHN_COMMON），st_value表示符号在段中的偏移，即符号位于st_shndx所在的段偏移st_value，在该例子中的global_init_var、func1、main都属于这种类型。
* 在目标文件中，符号类型是COMMON类型（st_shndx=SHN_COMMON），st_value表示符号的对其属性，在该例子中的global_uninit_var的Value值为0000000000000004，表示对其长度为4字节，即为global_uninit_var类型（int）的字节长度
* 在可执行文件中，st_value表示符号的虚拟地址，动态链接需要使用到

使用 readelf -s 命令查看目标文件的所有符号详细信息
```shell

[root@localhost simple-secion-linux-elf]# readelf -s SimpleSection.o
 
Symbol table '.symtab' contains 16 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS SimpleSection.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1 
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    3 
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    4 
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5 
     6: 0000000000000004     4 OBJECT  LOCAL  DEFAULT    3 static_var.2058
     7: 0000000000000000     4 OBJECT  LOCAL  DEFAULT    4 static_var2.2059
     8: 0000000000000000     0 SECTION LOCAL  DEFAULT    7 
     9: 0000000000000000     0 SECTION LOCAL  DEFAULT    8 
    10: 0000000000000000     0 SECTION LOCAL  DEFAULT    6 
    11: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    3 global_init_var
    12: 0000000000000004     4 OBJECT  GLOBAL DEFAULT  COM global_uninit_var
    13: 0000000000000000    36 FUNC    GLOBAL DEFAULT    1 func1
    14: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND printf
    15: 0000000000000024    57 FUNC    GLOBAL DEFAULT    1 main
```

使用 readelf -S 查看目标文件的所有段信息，后面分析符号信息的时候会作为参照使用到。
```shell
root@localhost simple-secion-linux-elf]# readelf -S SimpleSection.o
There are 13 section headers, starting at offset 0x1a0:
 
Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       000000000000005d  0000000000000000  AX       0     0     4
  [ 2] .rela.text        RELA             0000000000000000  000006c8
       0000000000000078  0000000000000018          11     1     8
  [ 3] .data             PROGBITS         0000000000000000  000000a0
       0000000000000008  0000000000000000  WA       0     0     4
  [ 4] .bss              NOBITS           0000000000000000  000000a8
       0000000000000004  0000000000000000  WA       0     0     4
  [ 5] .rodata           PROGBITS         0000000000000000  000000a8
       0000000000000004  0000000000000000   A       0     0     1
  [ 6] .comment          PROGBITS         0000000000000000  000000ac
       000000000000002d  0000000000000001  MS       0     0     1
  [ 7] .note.GNU-stack   PROGBITS         0000000000000000  000000d9
       0000000000000000  0000000000000000           0     0     1
  [ 8] .eh_frame         PROGBITS         0000000000000000  000000e0
       0000000000000058  0000000000000000   A       0     0     8
  [ 9] .rela.eh_frame    RELA             0000000000000000  00000740
       0000000000000030  0000000000000018          11     8     8
  [10] .shstrtab         STRTAB           0000000000000000  00000138
       0000000000000061  0000000000000000           0     0     1
  [11] .symtab           SYMTAB           0000000000000000  000004e0
       0000000000000180  0000000000000018          12    11     8
  [12] .strtab           STRTAB           0000000000000000  00000660
       0000000000000066  0000000000000000           0     0     1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings)
  I (info), L (link order), G (group), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)
```
几个重要的符号解释：

1. func1 和 main 定义与目标文件中，Ndx值为1表示位于代码段中（参照 readelf -S 结果中的 [Nr] 值 ）

    Bind值为GLOBAL，表示符号绑定信息为全局符号，外部可见

    Type值为FUNC，表示符号类四个函数或其他可执行代码

    Size值表示符号大小

    Value符号所在的地址

2. printf符号在目标文件中没有定义，所以Ndx值为UND

3. global_init_var符号是已初始化的全局变量，Ndx值为3表示位于.data数据段，
Bind值为OBJECT，表示符号类型为STT_OBJECT，变量数组的符号类型都为该值
Size为4，表示符号大小占用4字节
Value为0，表示该符号位于Ndx段的第0个位置

4. global_uninit_var Ndx值为COM表示位于COMMON段，全局未初始化的符号是这种类型的

5. 有部分Name未定义的符号，符号名就是段名，比如Num为2、Ndx为1的的符号表示.text段，他的符号名就是段名

6. SimpleSection.c符号的Ndx为ABS、Type为FILE，表示文件名的符号

DT_JMPREL: 重定位记录的开始地址, 指向.rela.plt节在内存中保存的地址
DT_PLTREL: 重定位记录的类型 RELA或RE, 这里是RELAL
DT_PLTRELSZ: 重定位记录的总大小, 这里是24 * 2 = 48


     导入表和导出表都需要依赖符号表

# 重定位

```c
typedef struct elf32_rel {
  Elf32_Addr    r_offset; //r_offset表示重定位入口的偏移。对于可重定位文件来说，这个值是该重定位入口所要修正的位置的第一个字节相对于节起始的偏移；对于可执行文件或共享对象文件来说，这个值是该重定位入口所要修正的位置的第一个字节的虚拟地址
  Elf32_Word    r_info; //r_info表示重定位入口的类型和符号。这个成员的高8位表示重定位入口的类型，低24位表示重定位入口的符号在符号表中的下标
} Elf32_Rel;
```

# 导入表

1. 导入函数

2. 导入结构体

3. 导入全局变量

4. 导入数组



# 帮助

[https://blog.csdn.net/weixin_33895475/article/details/91930494](https://blog.csdn.net/weixin_33895475/article/details/91930494)

[https://malware.news/t/executable-and-linkable-format-101-part-2-symbols/17960](https://malware.news/t/executable-and-linkable-format-101-part-2-symbols/17960)

[https://www.freebuf.com/articles/system/193646.html](https://www.freebuf.com/articles/system/193646.html)


[https://lwn.net/Articles/192624/](https://lwn.net/Articles/192624/)