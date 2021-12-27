## mmap文件映射

![20211226221439](https://cdn.jsdelivr.net/gh/nzcv/picgo/20211226221439.png)

1. 映射大小超过文件大小1个page, 读取的时候会出现Bus Error. 因为并没有分配真实的物理内存.

2. Segment Error, 代表有真实的物理内存. 但是权限不匹配

3. 按页分配, 且页边界对齐
